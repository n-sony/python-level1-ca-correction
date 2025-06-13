# Python level1 CA correction

## Part 1: Code Comprehension & Python Concepts (5 pts)

### 1. Code Analysis (3 pts)

#### 1.1 Original Code with Comments:

```python

# Defines a new class named User.
class User:
    # This is the constructor method, called when a new User object is created.
    # It initializes each instance with a username and an email.
    def __init__(self, username, email):
        # Assigns the provided username to the instance's 'username' attribute.
        self.username = username
        # Assigns the provided email to the instance's 'email' attribute.
        self.email = email

    # This is an instance method that belongs to each object created from the User class.
    def greet(self):
        # Returns a formatted string that includes the instance's username and email.
        return f"Hello {self.username}, your email is {self.email}"

# Creates a list containing two instances of the User class.
users = [User("sony", "sony@mail.com"), User("nina", "nina@mail.com")]

# Iterates through each user object in the 'users' list.
for user in users:
    # Calls the greet() method for the current user object and prints the returned string.
    print(user.greet())
```

#### 1.2. Extended User Class:

Here is the extended User class with the requested features and tests for the
new methods.

```python

import uuid # Not required but important

class User:
    """
    A class to represent a user with added functionalities like
    user counting, creation from a string, email validation, and a contact property.
    """

    # Class variable to track the number of user instances created.
    user_count = 0

    def __init__(self, username, email):
        self.username = username
        # Validate email upon initialization.
        if not User.validate_email(email):
            raise ValueError("Invalid email format provided.")
        self.email = email
        # Increment the user_count class variable each time a new user is instantiated.
        User.user_count += 1

    def greet(self):
        return f"Hello {self.username}, your email is {self.email}"

    @classmethod
    def from_string(cls, user_string):
        """
        Creates a User instance from a string in the format 'username,email'.
        'cls' refers to the class itself (User), not an instance.
        """
        username, email = user_string.split(',')
        return cls(username.strip(), email.strip())

    @staticmethod
    def validate_email(email):
        """
        Validates if an email string contains the '@' symbol.
        This method doesn't depend on the class or instance state.
        """
        return "@" in email

    @property
    def contact(self):
        """
        A property that returns the user's full contact information.
        Accessed like an attribute (user.contact), not a method (user.contact()).
        """
        return f"{self.username} <{self.email}>"

# --- Testing all new methods ---
print(f"Initial user count: {User.user_count}") # Expected: 0

# Test the constructor and user_count
user1 = User("john_doe", "john.doe@example.com")
user2 = User("jane_doe", "jane.doe@example.com")
print(f"User count after creating two users: {User.user_count}") # Expected: 2

# Test the classmethod from_string()
user_from_string = User.from_string("test_user, test@mail.com")
print(f"User created from string: {user_from_string.greet()}")
print(f"User count after creating from string: {User.user_count}") # Expected: 3

# Test the staticmethod validate_email()
print(f"Is 'test@test.com' a valid email? {User.validate_email('test@test.com')}") # Expected: True
print(f"Is 'test.com' a valid email? {User.validate_email('test.com')}") # Expected: False

# Test the property method 'contact'
print(f"User1's contact info: {user1.contact}") # Expected: john_doe <john.doe@example.com>
print(f"User2's contact info: {user2.contact}") # Expected: jane_doe <jane.doe@example.com>
```

Explanation of Method Types:

**Instance Method**: The most common type of method. It takes the instance
(self) as the first argument. It can access and modify both instance attributes
(like self.username) and class attributes (like User.user_count). It is only
usable when the class is instantiated

**Class Method**: Marked with the `@classmethod` decorator.
It takes the class (cls) as the first argument. It can't access instance-specific
attributes but can access and modify class-level attributes. It is often used
for factory methods that create instances of the class using alternative ways
(e.g., from_string).

**Static Method**: Marked with the `@staticmethod` decorator.
It does not take self or
cls as an implicit first argument. It is essentially a regular function namespaced
within the class. It cannot modify instance or class state and is used for utility
functions that are logically related to the class but don't depend on its state.

### 2. List Copying & Immutability (2 pts)

#### A. What is the output of the following code?

The output is:

`[1, 2, 3, 4]`

Explanation: The line `b = a` does not create a new list.
It makes the variable `b` refer to the exact same list object as `a`.
Therefore, when `b.append(4)` is called, the original list that both `a`
and `b` point to is modified.

#### B. Fix the behavior to avoid modifying a when changing b.

To fix this, you must create a copy of the list.

```python

a = [1, 2, 3]

b = a.copy()

b.append(4)

print(f"a = {a}") # Output: a = [1, 2, 3]
print(f"b = {b}") # Output: b = [1, 2, 3, 4]

```

#### C. Explain the difference between shallow copy and deep copy.

Shallow Copy: A shallow copy creates a new object but inserts references
into it to the objects found in the original.
If the list contains other mutable objects (like other lists),
modifying those nested objects will affect both the original and the copy.
`copy.copy()`, `list.copy()`, and slicing `[:]`
create shallow copies.

**When/Why to use**: Use a shallow copy when you have a list of immutable items
(like numbers or strings) or when you want changes to nested objects
to be reflected in both the original and the copy. It is faster and uses
less memory than a deep copy.

**Deep Copy**: A deep copy creates a new object and recursively creates copies
of all the objects found in the original. This means that the copy is completely
independent of the original, even if it contains nested mutable objects.
The `copy.deepcopy()` function is used for this.

**When/Why to use**: Use a deep copy when you need a completely independent clone
of an object, including all nested objects.
This prevents accidental modifications to the original data structure when the
copy is changed. It is essential for complex, nested data structures.

## Part 2: Python Concepts & Debugging (5 pts)

### A. Generators (3 pts)

#### A.1. What is a generator?

**A generator** is a special type of iterator, created by a function that uses the
yield keyword. It allows you to declare a function that behaves like an iterator,
i.e., it can be used in a for loop.

**How it differs from a regular function**: A regular function computes a value,
returns it, and exits. A generator function, however, pauses its execution
at the yield statement, returns the yielded value, and saves its local state.
When it's called again (e.g., in the next step of a loop),
it resumes execution right after the yield statement.

**Runtime behavior**: At runtime, when a generator function is called,
it returns a generator object but does not start execution.
The code inside the function only runs when the iterator's next() method is called.
Each call to next() runs the function until the next yield is encountered.

#### A.2. How does yield behave differently from return?

`yield` produces a value from the generator and pauses execution,
preserving the function's state. `return` exits the function completely,
and any subsequent calls will not resume from where it left off.

When the generator is exhausted or hits a return statement,
it raises a StopIteration exception to signal the end.

#### A.3. Compare a generator function to a generator expression.

Generator Function vs. List Comprehension:

```python

# Generator function that counts down from n
def countdown_generator(n):
    while n > 0:
        yield n
        n -= 1

# Equivalent list comprehension
n = 5
countdown_list = [i for i in range(n, 0, -1)]
```

Advantage and Drawback of Generators:

**Advantage**: Memory Efficiency. Generators produce items one at a time and only when required.
They don't store the entire sequence in memory, making them ideal for working
with very large datasets or infinite sequences.

**Drawback**: One-time Use. A generator can only be iterated over once.
After all its values have been yielded, it is exhausted and cannot be reused or reset.
You would need to create a new generator object to iterate again.

#### A.4. State Management

Here is the modified generator that accepts a step size via .send().

```python

def countdown_with_step(n):
    """A generator that counts down, allowing the step to be changed with send()."""
    step = 1  # Default step
    while n > 0:
        # yield n, then wait for a value to be sent.
        # The sent value will be assigned to 'step'.
        # If next() is called, received_step will be None.
        received_step = yield n
        if received_step is not None:
            step = received_step
        n -= step

# Example usage
gen = countdown_with_step(10)

print(next(gen))  # Starts at 10, yields 10

# Send a new step size of 2. The generator subtracts 2 and yields 8.
print(gen.send(2)) # subtracts 2, yields 8

# Send a new step size of 3. The generator subtracts 3 and yields 5.
print(gen.send(3)) # subtracts 3, yields 5

# Using next() again will use the last step size (3)
print(next(gen)) # subtracts 3, yields 2
```

**How `.send()` changes state**: The `.send()` method resumes the
generator's execution, just like `next()`, but it also "sends"
a value back into the generator. This value becomes the result of
the yield expression. This allows the generator's internal
state (like the step variable) to be modified from the outside during iteration.

### B. Debug the Decorator (2 pts)

Bugs and Fixes:

The bug is in the line `return func(args)`.
When `*args ` is used to capture arguments,
they are packed into a tuple (e.g., `(3, 4)`).
Passing this tuple args directly to add is incorrect.
The add function expects two separate arguments (`x` and `y`),
not a single tuple. The fix is to unpack the tuple using the `*` operator
when calling the function: `return func(*args, **kwargs)`.

Corrected Code:

```python
def log_call(func):
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        # BUG FIX: Unpack args and kwargs when calling the original function
        result = func(*args, **kwargs)
        # Added to print the result as requested
        print(f"{func.__name__} returned: {result}")
        return result
    return wrapper

@log_call
def add(x, y):
    return x + y

# This line now works correctly
add(3, 4)
```

Explanation:

**What is a decorator?**: A decorator is a design pattern in Python that allows a
user to add new functionality to an existing object (like a function or class)
without modifying its structure. It is a function that takes another function as
an argument, adds some functionality, and returns another function.

**How this decorator works**: The `@log_call` syntax is syntactic sugar
for `add = log_call(add)`. The `log_call` function takes the
add function as its func argument and returns the wrapper function. From now on,
whenever `add(3, 4)` is called, it is actually the `wrapper(3, 4)`
function that executes. The wrapper first prints a logging message
and then uses `*args` and `**kwargs` to correctly pass all arguments
it received to the original add function, captures its result,
prints it, and returns it.

## Part 3: Mini Project & Debugging (15 pts)

### 3.1 Projects (10 pts)

Here are the solutions for the mandatory Project B (Password Manager)
and Project A (Contact Book).

#### Project A: Contact Book with File I/O and Classes

```python

import json

class Contact:
    """
    Represents a single contact with a name, phone, and email.
    """
    def __init__(self, name, phone, email):
        self.name = name
        self.phone = phone
        self.email = email

    def __str__(self):
        """
        Provides a user-friendly string representation of the contact.
        """
        return f"Contact: {self.name}, Phone: {self.phone}, Email: {self.email}"

    def to_dict(self):
        """Converts the contact object to a dictionary for JSON serialization."""
        return {'name': self.name, 'phone': self.phone, 'email': self.email}

    @classmethod
    def from_dict(cls, data):
        """
        Creates a Contact instance from a dictionary.
        """
        return cls(data['name'], data['phone'], data['email'])

class ContactBook:
    def __init__(self, filename="contacts.json"):
        self.filename = filename
        self.contacts = self.load_contacts()

    def add_contact(self, contact):
        """Adds a new contact to the book and saves."""
        self.contacts.append(contact)
        self.save_contacts()
        print(f"Contact '{contact.name}' added.")

    def remove_contact(self, name):
        """Removes a contact by name and saves."""
        contact_to_remove = None
        for contact in self.contacts:
            if contact.name.lower() == name.lower():
                contact_to_remove = contact
                break

        if contact_to_remove:
            self.contacts.remove(contact_to_remove)
            self.save_contacts()
            print(f"Contact '{name}' removed.")
        else:
            print(f"Contact '{name}' not found.")

    def save_contacts(self):
        """
        Saves the list of contacts to a JSON file.
        """
        with open(self.filename, 'w') as f:
            json.dump([contact.to_dict() for contact in self.contacts], f, indent=4)

    def load_contacts(self):
        """
        Loads contacts from a JSON file.
        """
        try:
            with open(self.filename, 'r') as f:
                data = json.load(f)
                return [Contact.from_dict(item) for item in data]
        except (FileNotFoundError, json.JSONDecodeError):
            return []

    def display_contacts(self):
        if not self.contacts:
            print("Contact book is empty.")
        else:
            for contact in self.contacts:
                print(contact)

# --- Example Usage ---
if __name__ == '__main__':
    book = ContactBook()

    print("--- Initial Contacts ---")
    book.display_contacts()

    print("\n--- Adding Contacts ---")
    book.add_contact(Contact("Alice", "123-456-7890", "alice@email.com"))
    book.add_contact(Contact("Bob", "987-654-3210", "bob@email.com"))

    print("\n--- Current Contacts ---")
    book.display_contacts()

    print("\n--- Removing a Contact ---")
    book.remove_contact("Alice")

    print("\n--- Final Contacts ---")
    book.display_contacts()
```

#### Project B: Password Manager (Mandatory)

```python
import os
import json
import hashlib
import uuid
from cryptography.fernet import Fernet #

# --- Configuration ---
USER_DATA_FILE = "user_accounts.json"

# --- Helper Functions ---
def hash_password(password):
    """Hashes the master password using SHA256."""
    return hashlib.sha256(password.encode()).hexdigest()

def generate_key_from_master(master_password):
    """Generates a Fernet key from the master password.
    NOTE: In a real app, use a key derivation function like PBKDF2.
    This is a simplified approach for the exam.
    """
    hashed = hashlib.sha256(master_password.encode()).digest()
    return Fernet.generate_key() # In a real scenario, this key would be derived from the password and a salt. For this example, we generate a new key for simplicity of demonstration, though in a real system you'd want to deterministically derive it.

class PasswordManager:
    def __init__(self):
        self.users = self._load_user_data()

    def _load_user_data(self):
        """Loads user account metadata from the JSON file."""
        try:
            with open(USER_DATA_FILE, 'r') as f:
                return json.load(f)
        except (FileNotFoundError, json.JSONDecodeError):
            return {}

    def _save_user_data(self):
        """Saves user account metadata."""
        with open(USER_DATA_FILE, 'w') as f:
            json.dump(self.users, f, indent=4)

    def register_user(self, email, master_password):
        """Registers a new user."""
        if email in self.users:
            print("Error: User with this email already exists.")
            return

        hashed_master_password = hash_password(master_password)
        # Create a unique folder name for the vault
        vault_folder = str(uuid.uuid4())

        self.users[email] = {
            "master_password_hash": hashed_master_password,
            "vault_folder": vault_folder
        }

        # Create the vault folder on the file system
        os.makedirs(vault_folder, exist_ok=True)
        # Create the credential files
        with open(os.path.join(vault_folder, "websites.txt"), 'w') as f: pass
        with open(os.path.join(vault_folder, "desktop_software.txt"), 'w') as f: pass

        self._save_user_data()
        print(f"User '{email}' registered successfully. Vault created at '{vault_folder}'.")

    def login_user(self, email, master_password):
        """Authenticates a user and returns their vault info if successful."""
        user_info = self.users.get(email)
        if user_info and user_info["master_password_hash"] == hash_password(master_password):
            print(f"Authentication successful for '{email}'.")
            return user_info["vault_folder"]
        else:
            print("Authentication failed. Incorrect email or password.")
            return None

    def save_credential(self, vault_folder, master_password, device_type, name, username, password):
        """Saves an encrypted credential to the correct file."""
        if device_type not in ["website", "desktop"]:
            print("Invalid device type. Choose 'website' or 'desktop'.")
            return

        # For demonstration, generating a key. In a real system, you derive this from the master password.
        key = Fernet.generate_key() # Simplified for exam
        fernet = Fernet(key)

        # Encrypt username and password
        enc_username = fernet.encrypt(username.encode()).decode()
        enc_password = fernet.encrypt(password.encode()).decode()

        # In a real app, you would need to store the `key` securely, perhaps encrypted with the master password itself.
        # This part is complex and often requires a dedicated key management system.
        # For this exam, we'll store it alongside for decryption demonstration, which is NOT SECURE.
        credential_line = f"{name}|{enc_username}|{enc_password}|{key.decode()}\n"

        # Determine the correct file
        filename = "websites.txt" if device_type == "website" else "desktop_software.txt"
        filepath = os.path.join(vault_folder, filename)

        # Use context management for secure file I/O
        with open(filepath, 'a') as f:
            f.write(credential_line)
        print(f"Credential for '{name}' saved successfully.")


# --- Example Usage ---
if __name__ == '__main__':
    pm = PasswordManager()

    # 1. Register a new user
    pm.register_user("test@user.com", "MyStrongPassword123")

    # 2. Login
    vault = pm.login_user("test@user.com", "MyStrongPassword123")

    # 3. Save a credential if login was successful
    if vault:
        # User must authenticate before saving
        pm.save_credential(
            vault,
            "MyStrongPassword123",
            "website",
            "google.com",
            "my_google_user",
            "my_google_password"
        )
        pm.save_credential(
            vault,
            "MyStrongPassword123",
            "desktop",
            "Photoshop",
            "my_adobe_user",
            "my_adobe_password"
        )
```

### 3.2 Debug the Code (5 pts)

Here are the explanations and corrections for the three bugs.
Corrected Code:

```python

import copy
import os

# --- Configuration Section ---
DEFAULT_CONFIG = {"version": 1.0, "settings": ["low_power", "auto_save"]}
LOG_FILE_BASENAME = "app_log_"
MAX_LOG_FILES = 5

# --- Buggy Function 1: CORRECTED ---
def add_user_activity(user_id, activity, user_activities=None):
    """Adds an activity to a user's activity list."""
    # BUG FIX: Use None as the default and initialize a new dictionary if it is None.
    # This prevents the same dictionary from being reused across different function calls.
    if user_activities is None:
        user_activities = {}

    # Original logic remains the same
    if user_id not in user_activities:
        user_activities[user_id] = []
    user_activities[user_id].append(activity)
    print(f"Activity '{activity}' added for user '{user_id}'. Current activities: {user_activities[user_id]}")
    return user_activities

# --- Buggy Function 2: CORRECTED ---
def clone_and_modify_config(base_config, new_setting):
    """Clones a base configuration and adds a new setting."""
    # BUG FIX: Use copy.deepcopy() to create a fully independent copy.
    # copy.copy() was a shallow copy, so the 'settings' list was shared.
    new_config = copy.deepcopy(base_config) #

    # Original logic remains the same
    new_config["settings"].append(new_setting)
    new_config["version"] = base_config.get("version", 1.0) + 0.1
    print(f"New config created: {new_config}")
    return new_config

# --- Buggy Function 3: CORRECTED ---
def process_log_files(log_directory, num_files_to_process):
    """Simulates processing a series of log files."""
    processed_lines = 0
    for i in range(num_files_to_process):
        filename = f"{log_directory}/{LOG_FILE_BASENAME}{i}.txt"
        try:
            # Create/prepare the file
            with open(filename, 'a+') as f_check:
                if f_check.tell() == 0:
                    f_check.write(f"Log entry for file {i}\n")

            # BUG FIX: Use a 'with' statement to ensure the file handler is automatically closed.
            # This prevents a file handle leak.
            with open(filename, 'r') as file_handler:
                first_line = file_handler.readline()
                if first_line:
                    print(f"Processing '{filename}': {first_line.strip()}")
                    processed_lines += 1
        except IOError as e:
            print(f"Error accessing file {filename}: {e}")

    print(f"Total lines processed: {processed_lines}")
    return processed_lines

# --- Main Execution (for demonstration) ---
def main():
    print("--- Starting Bug Hunt Demonstration ---")

    # Bug 1 Demonstration: Mutable Default Argument
    print("\n--- Testing User Activity Logger (Corrected) ---")
    activities1 = add_user_activity("user123", "login")
    add_user_activity("user123", "view_page", activities1)
    activities2 = add_user_activity("user456", "logout") # This will now work correctly

    # Bug 2 Demonstration: Shallow Copy
    print("\n--- Testing Configuration Cloner (Corrected) ---")
    original_config = DEFAULT_CONFIG
    print(f"Original default config: {original_config}")
    config_clone1 = clone_and_modify_config(original_config, "high_performance")
    print(f"After clone 1, original config: {original_config}") # No longer modified
    config_clone2 = clone_and_modify_config(original_config, "dark_mode") #
    print(f"After clone 2, original config: {original_config}") # Still unchanged
    if "high_performance" not in original_config["settings"]: #
        print("SUCCESS: Original config was NOT modified!")

    # Bug 3 Demonstration: File Handle Leakage
    print("\n--- Testing Log File Processor (Corrected) ---")
    dummy_log_dir = "temp_log_dir"
    if not os.path.exists(dummy_log_dir):
        os.makedirs(dummy_log_dir)
    process_log_files(dummy_log_dir, MAX_LOG_FILES)
    print("SUCCESS: Files are now closed properly using 'with' statement.") #

    # Cleanup
    for i in range(MAX_LOG_FILES):
        os.remove(f"{dummy_log_dir}/{LOG_FILE_BASENAME}{i}.txt")
    os.rmdir(dummy_log_dir)

if __name__ == "__main__":
    main()
```

## Part 4: Algorithm (5 pts)

Group Anagrams
Here is the implementation of the group_anagrams function using a dictionary.

```python

from collections import defaultdict

def group_anagrams(words):
    """
    Groups a list of words into sets of anagrams.


    Args:
        words: A list of strings.

    Returns:
        A list of lists, where each inner list contains words that are anagrams of each other.
    """

    # Use a defaultdict to automatically handle the creation of new list entries.
    # The key will be the sorted string (the canonical representation of an anagram group),
    # and the value will be the list of words that match that key.
    anagram_groups = defaultdict(list)

    # Iterate through each word in the input list.
    for word in words:
        # Sort the characters of the word to create a unique key for its anagram group.
        # For example, 'listen', 'silent', and 'enlist' all become 'eilnst'.
        sorted_word = "".join(sorted(word))

        # Append the original word to the list associated with its sorted key.
        anagram_groups[sorted_word].append(word)

    # The values() of the dictionary are the lists of anagrams.
    # Convert them to a list and return.
    return list(anagram_groups.values())

# --- Example Usage ---
input_words = ["listen", "silent", "enlist", "rat", "tar", "art"] #
output = group_anagrams(input_words)
print(f"Input: {input_words}")
print(f"Output: {output}")
# Expected Output: [['listen', 'silent', 'enlist'], ['rat', 'tar', 'art']]
```
