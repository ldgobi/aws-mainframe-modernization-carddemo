# Documentation for `COUSR00C.CBL`

## Metadata
- **File Name**: `COUSR00C.cbl`
- **Application**: CardDemo
- **Type**: CICS COBOL Program
- **Function**: List all users from the `USRSEC` file.

---

## Overview

The `COUSR00C` program is a CICS COBOL application designed to list users from the `USRSEC` file. It provides functionality for navigating through user records, handling user selection, and managing pagination (forward and backward). The program interacts with CICS services for file operations and screen handling.

---

## Key Features

1. **User Listing**: Retrieves user records from the `USRSEC` file and displays them on the screen.
2. **Pagination**: Supports navigation through user records using PF7 (previous page) and PF8 (next page) keys.
3. **User Selection**: Allows users to select specific records for further processing.
4. **Error Handling**: Manages errors during file operations and user interactions.
5. **Screen Management**: Sends and receives screens using CICS commands.

---

## Data Structures

### **Working Storage Section**
The working storage section defines variables used throughout the program.

| **Variable Name**       | **Type**         | **Purpose**                                                                 |
|--------------------------|------------------|-----------------------------------------------------------------------------|
| `WS-PGMNAME`            | PIC X(08)       | Program name (`COUSR00C`).                                                 |
| `WS-TRANID`             | PIC X(04)       | Transaction ID (`CU00`).                                                   |
| `WS-MESSAGE`            | PIC X(80)       | Message buffer for error or status messages.                               |
| `WS-USRSEC-FILE`        | PIC X(08)       | File name (`USRSEC`).                                                      |
| `WS-ERR-FLG`            | PIC X(01)       | Error flag (`Y` for error, `N` for no error).                              |
| `WS-USER-SEC-EOF`       | PIC X(01)       | End-of-file flag for `USRSEC` file.                                        |
| `WS-SEND-ERASE-FLG`     | PIC X(01)       | Flag to determine whether to erase the screen before sending.              |
| `WS-RESP-CD`            | PIC S9(09) COMP | Response code from CICS commands.                                          |
| `WS-REAS-CD`            | PIC S9(09) COMP | Reason code from CICS commands.                                            |
| `WS-REC-COUNT`          | PIC S9(04) COMP | Record count for user data.                                                |
| `WS-IDX`                | PIC S9(04) COMP | Index for user data processing.                                            |
| `WS-PAGE-NUM`           | PIC S9(04) COMP | Current page number for pagination.                                        |

### **User Data**
Defines the structure for user records.

| **Field Name** | **Type**   | **Purpose**                     |
|----------------|------------|---------------------------------|
| `USER-SEL`     | PIC X(01)  | Selection flag for the user.    |
| `USER-ID`      | PIC X(08)  | User ID.                       |
| `USER-NAME`    | PIC X(25)  | User name.                     |
| `USER-TYPE`    | PIC X(08)  | User type.                     |

---

## Logic and Functionality

### **Main Logic**
The `MAIN-PARA` section initializes flags and variables, handles screen navigation, and processes user input.

1. **Initialization**:
   - Sets error flags and end-of-file flags.
   - Clears message buffers.

2. **Screen Navigation**:
   - If `EIBCALEN` is zero, returns to the previous screen.
   - Otherwise, processes user input and navigates screens based on function keys.

3. **CICS Return**:
   - Returns control to the transaction ID (`CU00`) with the communication area.

---

### **Key Procedures**

#### **PROCESS-ENTER-KEY**
Handles user selection and validates input. Based on the selection flag (`U` or `D`), it transfers control to other programs (`COUSR02C` or `COUSR03C`).

#### **PROCESS-PF7-KEY**
Navigates to the previous page of user records. If already at the top of the page, displays a message.

#### **PROCESS-PF8-KEY**
Navigates to the next page of user records. If already at the bottom of the page, displays a message.

#### **PROCESS-PAGE-FORWARD**
Reads the next set of user records from the `USRSEC` file and populates the user data structure.

#### **PROCESS-PAGE-BACKWARD**
Reads the previous set of user records from the `USRSEC` file and populates the user data structure.

#### **SEND-USRLST-SCREEN**
Sends the user list screen to the terminal. Supports erasing the screen before sending.

#### **RECEIVE-USRLST-SCREEN**
Receives user input from the terminal.

#### **POPULATE-USER-DATA**
Populates user data fields based on the current record index.

#### **INITIALIZE-USER-DATA**
Clears user data fields for the current record index.

#### **STARTBR-USER-SEC-FILE**
Starts browsing the `USRSEC` file using the `SEC-USR-ID` key.

#### **READNEXT-USER-SEC-FILE**
Reads the next record from the `USRSEC` file.

#### **READPREV-USER-SEC-FILE**
Reads the previous record from the `USRSEC` file.

#### **ENDBR-USER-SEC-FILE**
Ends browsing the `USRSEC` file.

---

## Insights

1. **CICS Integration**:
   - The program heavily relies on CICS commands (`STARTBR`, `READNEXT`, `READPREV`, `SEND`, `RECEIVE`, `XCTL`) for file and screen operations.

2. **Pagination Logic**:
   - The program implements robust pagination logic, ensuring smooth navigation through user records.

3. **Error Handling**:
   - Error flags (`WS-ERR-FLG`) and response codes (`WS-RESP-CD`, `WS-REAS-CD`) are used to manage errors during file operations and user interactions.

4. **Dynamic Screen Management**:
   - The program dynamically sends and receives screens based on user input and navigation actions.

5. **User Selection**:
   - Supports user selection for further processing, transferring control to other programs based on the selection.

6. **Scalability**:
   - The program supports up to 10 user records per page, making it suitable for large datasets with efficient pagination.

---

## References

- **CICS Commands**: Used for file operations and screen handling.
- **COPY Statements**: Includes external definitions for data structures and constants (`COCOM01Y`, `COUSR00`, etc.).
- **Error Messages**: Provides user-friendly messages for navigation and error scenarios.
