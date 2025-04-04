# Documentation for `COUSR02C.CBL`

## Overview

The `COUSR02C.CBL` program is a **CICS COBOL application** designed to update user information in the `USRSEC` file. It is part of the **CardDemo** application and interacts with CICS resources to manage user data. The program handles user input validation, updates user records, and provides feedback to the user interface.

---

## Metadata

| **Attribute**       | **Value**                     |
|----------------------|-------------------------------|
| **Program Name**     | `COUSR02C`                   |
| **Application**      | `CardDemo`                   |
| **Type**             | CICS COBOL Program           |
| **Function**         | Update a user in the `USRSEC` file |
| **Author**           | AWS                          |
| **License**          | Apache License 2.0           |

---

## Key Features

1. **User Update Functionality**:
   - Validates user input fields (e.g., User ID, First Name, Last Name, Password, User Type).
   - Reads and updates user records in the `USRSEC` file.
   - Provides error messages for invalid or missing input.

2. **CICS Integration**:
   - Uses CICS commands for file operations (`READ`, `REWRITE`) and screen handling (`SEND`, `RECEIVE`).
   - Manages program transitions using `EXEC CICS XCTL`.

3. **Error Handling**:
   - Implements error flags (`WS-ERR-FLG`) and provides user-friendly error messages.
   - Handles CICS response codes (`RESP` and `RESP2`) for file operations.

4. **Screen Management**:
   - Sends and receives data to/from the `COUSR2A` map.
   - Populates header information (e.g., current date and time) for the user interface.

---

## Data Structures

### Working-Storage Section

| **Variable**            | **Description**                                                                 |
|--------------------------|---------------------------------------------------------------------------------|
| `WS-PGMNAME`            | Program name (`COUSR02C`).                                                      |
| `WS-TRANID`             | Transaction ID (`CU02`).                                                        |
| `WS-MESSAGE`            | Message buffer for user feedback.                                               |
| `WS-USRSEC-FILE`        | File name for the user security file (`USRSEC`).                                |
| `WS-ERR-FLG`            | Error flag (`Y` for error, `N` for no error).                                   |
| `WS-RESP-CD`            | CICS response code for operations.                                              |
| `WS-REAS-CD`            | CICS reason code for operations.                                                |
| `WS-USR-MODIFIED`       | Indicates if the user record was modified (`Y` for yes, `N` for no).            |

### Linkage Section

| **Variable**            | **Description**                                                                 |
|--------------------------|---------------------------------------------------------------------------------|
| `DFHCOMMAREA`           | Communication area for passing data between programs.                           |

---

## Program Logic

### Main Flow (`MAIN-PARA`)

1. **Initialization**:
   - Resets error flags and clears messages.
   - Checks if `EIBCALEN` (length of communication area) is zero. If so, transitions to the previous screen.

2. **Processing**:
   - If the program is not re-entered, initializes fields and processes the selected user.
   - Handles user input based on the function key pressed (`EIBAID`):
     - `DFHENTER`: Processes the "Enter" key.
     - `DFHPF3`: Updates user information and transitions to the previous screen.
     - `DFHPF4`: Clears the current screen.
     - `DFHPF5`: Updates user information.
     - `DFHPF12`: Transitions to the administration screen.

3. **CICS Return**:
   - Returns control to CICS with the transaction ID and communication area.

---

### Key Subroutines

#### `PROCESS-ENTER-KEY`
- Validates the `User ID` field.
- Reads the user record from the `USRSEC` file.
- Populates the screen with user details if no errors occur.

#### `UPDATE-USER-INFO`
- Validates all input fields (e.g., `User ID`, `First Name`, `Last Name`, `Password`, `User Type`).
- Compares input values with existing values in the `USRSEC` file.
- Updates the file if any changes are detected.

#### `RETURN-TO-PREV-SCREEN`
- Transitions to the previous screen or a default program (`COSGN00C`).

#### `SEND-USRUPD-SCREEN`
- Sends the `COUSR2A` map to the user interface with updated data and messages.

#### `RECEIVE-USRUPD-SCREEN`
- Receives user input from the `COUSR2A` map.

#### `POPULATE-HEADER-INFO`
- Populates header fields (e.g., program name, transaction ID, current date and time) for the user interface.

#### `READ-USER-SEC-FILE`
- Reads a user record from the `USRSEC` file using the `User ID` as the key.
- Handles errors such as "User ID not found."

#### `UPDATE-USER-SEC-FILE`
- Updates a user record in the `USRSEC` file.
- Provides feedback to the user upon successful or failed updates.

#### `CLEAR-CURRENT-SCREEN`
- Resets all input fields and clears the screen.

#### `INITIALIZE-ALL-FIELDS`
- Initializes all fields in the input area to default values (e.g., spaces, `-1`).

---

## Insights

1. **CICS Integration**:
   - The program heavily relies on CICS commands for file and screen operations, making it suitable for online transaction processing.

2. **Error Handling**:
   - Comprehensive error handling ensures robust user interaction and data integrity.

3. **Modular Design**:
   - The use of subroutines (e.g., `PROCESS-ENTER-KEY`, `UPDATE-USER-INFO`) promotes code reusability and maintainability.

4. **User Feedback**:
   - The program provides clear and actionable feedback to users, improving the user experience.

5. **Validation**:
   - Input validation is a critical aspect of the program, ensuring that only valid data is processed and stored.

6. **Scalability**:
   - The use of a communication area (`DFHCOMMAREA`) and modular design makes the program adaptable for integration with other components of the `CardDemo` application.
