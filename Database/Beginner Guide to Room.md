---
tags:
  - android
  - room
  - done
---
Video: https://www.youtube.com/watch?v=bOd3wO0uFr8
- Room is a SQLite object mapping library: 
	- Designed to implement local SQLite database transactions
	- Helps to avoid boilerplate code
- Room developer docs: https://developer.android.com/training/data-storage/room
- There are three major components in Room:
	- The [database class](https://developer.android.com/reference/kotlin/androidx/room/Database) that holds the database and serves as the main access point for the underlying connection to your app's persisted data.
	- [Data entities](https://developer.android.com/training/data-storage/room/defining-data) that represent tables in your app's database.
	- [Data access objects (DAOs)](https://developer.android.com/training/data-storage/room/accessing-data) that provide methods that your app can use to query, update, insert, and delete data in the database.
- Room database example: Contact list app
- Create a `Contact` data class:
```kotlin
@Entity
data class Contact(
    @PrimaryKey(autoGenerate = true)
    val id: Int? = null,
    val firstName: String,
    val lastName: String,
    val phoneNumber: String
)
```
- The `@Entity` annotation defines the class as a table
	- Can be used with parenthesis for further table configuration options
- The `@PrimaryKey` annotation defines the `id` field as the table's primary key
	- Set `autoGenerate = true` to enable Room to automatically pick the next integer for the `id` when inserting a new row with `id = null`
- Create a `ContactDao` interface:
```kotlin
@Dao
interface ContactDao {
    @Upsert
    suspend fun upsertContact(contact: Contact)

    @Delete
    suspend fun deleteContact(contact: Contact)

    @Query("SELECT * FROM Contact ORDER BY firstName ASC")
    fun getContactsOrderedByFirstName(): Flow<List<Contact>>

    @Query("SELECT * FROM Contact ORDER BY lastName ASC")
    fun getContactsOrderedByLastName(): Flow<List<Contact>>

    @Query("SELECT * FROM Contact ORDER BY phoneNumber ASC")
    fun getContactsOrderedByPhoneNumber(): Flow<List<Contact>>
}
```
- The `@Dao` annotation defines the interface as a [Data Access Object](https://developer.android.com/training/data-storage/room/accessing-data)
- There are many annotations for the `@Dao`:
	- `@Insert`: Insert a row
	- `@Upsert`: Upsert a row
	- `@Query`: Query the table (entity)
- Note that `@Insert` comes with a default conflict strategy of `ABORT`:
```kotlin
@Insert(onConflict = OnConflictStrategy.ABORT)
fun insertContact(contact: Contact)
```
- For simplicity, you can just use `@Upsert` instead of using `@Insert` with a modified conflict strategy
- Define the database:
```kotlin
@Database(
    version = 1,
    entities = [Contact::class]
)
abstract class ContactDatabase: RoomDatabase() {
    // Define any dao's for your database
    abstract val contactDao: ContactDao
}
```
- Note that the `version = 1` will be utilized for migration strategies later
- Create a `ContactSortType` enum class:
```kotlin
enum class ContactSortType {
    FIRST_NAME,
    LAST_NAME,
    PHONE_NUMBER
}
```
- Create a `ContactEvent` sealed interface:
```kotlin
// Define user action events for the contact screen
sealed interface ContactEvent {
    
    // Dialog events
    object ShowDialog: ContactEvent
    object HideDialog: ContactEvent
    
    // Creating a contact
    data class SetFirstName(val firstName: String): ContactEvent
    data class SetLastName(val lastName: String): ContactEvent
    data class SetPhoneNumber(val phoneNumber: String): ContactEvent
    object SaveContact: ContactEvent
    
    // Sorting contacts
    data class SortContacts(val sortType: ContactSortType): ContactEvent
    
    // Deleting a contact
    data class DeleteContact(val contact: Contact): ContactEvent
    
}
```
- Create a `ContactState` data class:
```kotlin
data class ContactState(
    val contacts: List<Contact> = emptyList(),
    val firstname: String = "",
    val lastName: String = "",
    val phoneNumber: String = "",
    val showAddContactDialog: Boolean = false,
    val sortType: ContactSortType = ContactSortType.FIRST_NAME
)
```
- Create a `ContactViewModel` view model class:
```kotlin
@OptIn(ExperimentalCoroutinesApi::class)
class ContactViewModel(
    private val contactDao: ContactDao
): ViewModel() {
    // Private sort type state flow for the ViewModel to mutate
    private val _sortType = MutableStateFlow(ContactSortType.FIRST_NAME)

    // Private contacts state flow for the ViewModel to mutate
    private val _contacts = _sortType
        // Each sort query from the database emits a different flow
        // flatMapLatest will cancel the current flow and instead emit the new flow
        // With this we can have a single state of _contacts which emits whichever sort query flow that was selected
        .flatMapLatest { sortType ->
            when(sortType) {
                ContactSortType.FIRST_NAME -> contactDao.getContactsOrderedByFirstName()
                ContactSortType.LAST_NAME -> contactDao.getContactsOrderedByLastName()
                ContactSortType.PHONE_NUMBER -> contactDao.getContactsOrderedByPhoneNumber()
            }
        }
        // stateIn converts the cold flow (Flow) into a hot flow (StateFlow) so the state can be saved/cached
        // Selects the scope to viewModelScope so that is where the state will be saved
        // SharingStarted.WhileSubscribed() means the flatMapLatest will only execute if there is a subscriber to the _contacts state flow
        // Then set the initial value of the saved state to emptyList()
        .stateIn(viewModelScope, SharingStarted.WhileSubscribed(), emptyList())

    // Private state for the ViewModel to mutate
    private val _state = MutableStateFlow(ContactState())

    // Create the public version of the state for the UI to observe
    // When any of the flows: _state, _sortType, or _contacts emits a new value, then state.copy() will run
    // Set the stopTimeoutMillis to 5 seconds to avoid a bug when in the onDestroy lifecycle state
    // Then set the initial value of the saved state to ContactState()
    val state = combine(_state, _sortType, _contacts) { state, sortType, contacts ->
        state.copy(
            contacts = contacts,
            sortType = sortType
        )
    }.stateIn(viewModelScope, SharingStarted.WhileSubscribed(5000), ContactState())

    fun onEvent(event: ContactEvent) {
        when(event) {
            // Dialog events
            ContactEvent.ShowDialog -> {
                _state.update { it.copy(
                    showAddContactDialog = true
                )}
            }
            ContactEvent.HideDialog -> {
                _state.update { it.copy(
                    showAddContactDialog = false
                )}
            }

            // Creating a contact
            is ContactEvent.SetFirstName -> {
                _state.update { it.copy(
                    firstName = event.firstName
                )}
            }
            is ContactEvent.SetLastName -> {
                _state.update { it.copy(
                    lastName = event.lastName
                )}
            }
            is ContactEvent.SetPhoneNumber -> {
                _state.update { it.copy(
                    phoneNumber = event.phoneNumber
                )}
            }
            ContactEvent.SaveContact -> {
                val firstName = state.value.firstName
                val lastName = state.value.lastName
                val phoneNumber = state.value.phoneNumber

                if (firstName.isBlank() || lastName.isBlank() || phoneNumber.isBlank()) {
                    // Skip doing any further action if one of the fields is blank
                    // You can improve this with form validation logic instead
                    return
                }

                // Initialize the contact from the given fields
                val contact = Contact(
                    firstName = firstName,
                    lastName = lastName,
                    phoneNumber = phoneNumber
                )

                // Upsert the contact to the database
                viewModelScope.launch {
                    contactDao.upsertContact(contact)
                }

                // Update state to hide the add contact dialog and reset the text fields
                _state.update { it.copy(
                    showAddContactDialog = false,
                    firstName = "",
                    lastName = "",
                    phoneNumber = ""
                )}
            }

            // Sorting contacts
            is ContactEvent.SortContacts -> {
                _sortType.value = event.sortType
            }

            // Deleting a contact
            is ContactEvent.DeleteContact -> {
                viewModelScope.launch {
                    contactDao.deleteContact(event.contact)
                }
            }
        }
    }
}
```
- Tip: For a `when` switch case you can press `Alt+Enter` to see `Add remaining branches` option which will generate a list of cases the `when` can hit and stub them for you with `TODO()`
	- `TODO()` is a built-in Kotlin function that automatically throws `NotImplementedError`
	- The Kotlin compiler knows that a case can be missing and requires you to add all cases or add an `else` case
- Tip: `isBlank()` is a built-in Kotlin function for `String` type that returns `true` if the string is empty or consists solely of whitespace characters
- Create a `ContactScreen` composable:
```kotlin
@Composable
fun ContactScreen(
    state: ContactState,
    onEvent: (ContactEvent) -> Unit
) {
    Scaffold(
        // Add a FAB to the screen
        floatingActionButton = {
            FloatingActionButton(onClick = {
                // Emit a ShowDialog event when clicked
                onEvent(ContactEvent.ShowDialog)
            }) {
                Icon(
                    imageVector = Icons.Default.Add,
                    contentDescription = "Add contact"
                )
            }
        }
    ) { _ ->
        LazyColumn(
            contentPadding = PaddingValues(16.dp),
            modifier = Modifier.fillMaxSize(),
            verticalArrangement = Arrangement.spacedBy(16.dp)
        ) {
            // Row for radio buttons to sort contacts
            item {
                Row(
                    modifier = Modifier
                        .fillMaxWidth()
                        .horizontalScroll(rememberScrollState()),
                    verticalAlignment = Alignment.CenterVertically
                ) {
                    ContactSortType.entries.forEach { contactSortType ->
                        // Row for aligning radio button next to sort type label
                        Row(
                            modifier = Modifier.clickable {
                                // Emit a SortContacts event with the sort type when clicked
                                onEvent(ContactEvent.SortContacts(contactSortType))
                            },
                            verticalAlignment = Alignment.CenterVertically
                        ) {
                            RadioButton(
                                selected = state.sortType == contactSortType,
                                onClick = {
                                    // Do the same as the row, emit the event
                                    onEvent(ContactEvent.SortContacts(contactSortType))
                                }
                            )
                            Text(text = contactSortType.name)
                        }
                    }
                }
            }

            // List of contacts
            items(state.contacts) { contact ->
                // Row for the contact
                Row(
                    modifier = Modifier.fillMaxWidth()
                ) {
                    // Column for contact info
                    Column(
                        modifier = Modifier.weight(1f)
                    ) {
                        Text(
                            text = "${contact.firstName} ${contact.lastName}",
                            fontSize = 20.sp
                        )
                        Text(
                            text = contact.phoneNumber,
                            fontSize = 12.sp
                        )
                    }

                    // Icon button for deleting the contact
                    IconButton(onClick = {
                        // Emit a DeleteContact event with the current contact
                        onEvent(ContactEvent.DeleteContact(contact))
                    }) {
                        Icon(
                            imageVector = Icons.Default.Delete,
                            contentDescription = "Delete contact"
                        )
                    }
                }
            }
        }
    }
}
```
- Create an `AddContactDialog` composable:
```kotlin
@Composable
fun AddContactDialog(
    state: ContactState,
    onEvent: (ContactEvent) -> Unit,
    modifier: Modifier = Modifier
) {
    AlertDialog(
        modifier = modifier,
        title = {
          Text(text = "Add Contact")
        },
        onDismissRequest = {
            // Emit a HideDialog event when the user dismisses the alert
            onEvent(ContactEvent.HideDialog)
        },
        confirmButton = {
            TextButton(onClick = {
                // Emit a SaveContact event when the alert is confirmed
                onEvent(ContactEvent.SaveContact)
            }) {
                Text(text = "Save")
            }
        },
        text = {
            // Display a column of text fields
            Column(
                verticalArrangement = Arrangement.spacedBy(8.dp)
            ) {
                // Edit contact's first name
                TextField(
                    value = state.firstName,
                    onValueChange = {
                        // Emit a SetFirstName event with the current field's text
                        onEvent(ContactEvent.SetFirstName(it))
                    },
                    placeholder = {
                        Text(text = "First Name")
                    }
                )

                // Edit contact's last name
                TextField(
                    value = state.lastName,
                    onValueChange = {
                        // Emit a SetLastName event with the current field's text
                        onEvent(ContactEvent.SetLastName(it))
                    },
                    placeholder = {
                        Text(text = "Last Name")
                    }
                )

                // Edit contact's phone number
                TextField(
                    value = state.phoneNumber,
                    onValueChange = {
                        // Emit a SetPhoneNumber event with the current field's text
                        onEvent(ContactEvent.SetPhoneNumber(it))
                    },
                    placeholder = {
                        Text(text = "Phone Number")
                    }
                )
            }
        },
    )
}
```
- Now put the `AddContactDialog` in the `ContactScreen`:
```kotlin
Scaffold(
	// ...
) { _ ->
	// Only show the dialog if state is true
	if(state.showAddContactDialog) {
		AddContactDialog(state = state, onEvent = onEvent)
	}

	LazyColumn(
		// ...
	) {
		// ...
	}
}
```
- Finally, put the `ContactScreen` in the `MainActivity`:
```kotlin
class MainActivity : ComponentActivity() {
    // Create the database
    // Note: Bypassing the recommended dagger dependency injection for the database for quick example here
    private val db by lazy {
        Room.databaseBuilder(
            applicationContext,
            ContactDatabase::class.java,
            "contacts.db"
        ).build()
    }

    // Connect to the ContactViewModel
    private val contactViewModel by viewModels<ContactViewModel>(
        // Create a factory for the ContactDao
        // Note: Dagger would do this for you
        factoryProducer = {
            object : ViewModelProvider.Factory {
                override fun <T : ViewModel> create(modelClass: Class<T>): T {
                    return ContactViewModel(db.contactDao) as T
                }
            }
        }
    )

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            Surface(
                modifier = Modifier.fillMaxSize(),
                color = MaterialTheme.colorScheme.background
            ) {
                // Collect the state
                val state by contactViewModel.state.collectAsState()

                // Render the ContactScreen with state connected
                ContactScreen(state = state, onEvent = contactViewModel::onEvent)
            }
        }
    }
}
```
- Tip: You can inspect your database with the Database Inspector
	- Android Studio -> View -> Tool Windows -> App Inspection
		- Select the device and package
			- Select Database Inspector tab