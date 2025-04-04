# Documentation for `COUSR01C.cbl`

## Metadata
| **File Name** | **Application** | **Type** | **Function** |
|---------------|-----------------|----------|--------------|
| `COUSR01C.cbl` | CardDemo | CICS COBOL Program | Add a new Regular/Admin user to `USRSEC` file |

---

## Overview

This COBOL program is designed to interact with a CICS environment to add a new user (Regular/Admin) to the `USRSEC` file. It handles user input validation, screen navigation, and file operations. The program uses CICS commands for screen handling and file operations, ensuring transactional integrity and user feedback.

---

## Sections

### **Identification Division**
- **Program ID**: `COUSR01C`
- **Author**: AWS
- **Purpose**: Defines the program's identity and metadata.

---

### **Environment Division**
- Specifies the configuration for the program's runtime environment.

---

### **Data Division**

#### **Working-Storage Section**
This section defines variables used throughout the program. Key variables include:

| **Variable Name** | **Type** | **Purpose** |
|--------------------|----------|-------------|
| `WS-PGMNAME`       | PIC X(08) | Stores the program name (`COUSR01C`). |
| `WS-TRANID`        | PIC X(04) | Stores the transaction ID (`CU01`). |
| `WS-MESSAGE`       | PIC X(80) | Holds error or informational messages. |
| `WS-USRSEC-FILE`   | PIC X(08) | Name of the user security file (`USRSEC`). |
| `WS-ERR-FLG`       | PIC X(01) | Error flag (`Y` for error, `N` for no error). |
| `WS-RESP-CD`       | PIC S9(09) COMP | Stores CICS response codes. |
| `WS-REAS-CD`       | PIC S9(09) COMP | Stores CICS reason codes. |

#### **Linkage Section**
Defines the `DFHCOMMAREA` for passing data between programs. It uses dynamic sizing based on `EIBCALEN`.

---

### **Procedure Division**

#### **Main Logic**
The main logic (`MAIN-PARA`) handles the following:
1. **Initialization**:
   - Sets error flag to `OFF`.
   - Clears messages.
2. **Screen Navigation**:
   - If `EIBCALEN` is zero, navigates to a previous screen (`COSGN00C`).
   - Otherwise, processes the `DFHCOMMAREA` and handles user input.
3. **Key Handling**:
   - Evaluates user actions (`EIBAID`) such as `ENTER`, `PF3`, `PF4`, or invalid keys.
4. **CICS Return**:
   - Returns control to the transaction ID (`WS-TRANID`) with the updated `COMMAREA`.

#### **Process-Enter-Key**
Validates user input fields:
- Ensures mandatory fields (`First Name`, `Last Name`, `User ID`, `Password`, `User Type`) are not empty.
- If validation passes, writes user data to the `USRSEC` file.

#### **Return-to-Prev-Screen**
Handles navigation to the previous screen (`COSGN00C`) using `XCTL`.

#### **Send-UsrAdd-Screen**
Sends the `COUSR1A` map to the user interface with updated error or informational messages.

#### **Receive-UsrAdd-Screen**
Receives user input from the `COUSR1A` map.

#### **Populate-Header-Info**
Populates header information for the screen, including:
- Current date and time.
- Transaction and program names.

#### **Write-User-Sec-File**
Writes user data to the `USRSEC` file using CICS commands. Handles response codes:
- **Normal**: User added successfully.
- **DupKey/DupRec**: User ID already exists.
- **Other**: Generic error.

#### **Clear-Current-Screen**
Clears all fields and redisplays the screen.

#### **Initialize-All-Fields**
Resets all fields to their default values (spaces or `-1`).

---

## Insights

### **Key Features**
- **CICS Integration**: The program uses CICS commands (`SEND`, `RECEIVE`, `WRITE`, `RETURN`, `XCTL`) for transactional operations and screen handling.
- **Dynamic Screen Handling**: Supports navigation between screens and dynamic user input validation.
- **Error Management**: Implements robust error handling with flags and user feedback messages.
- **File Operations**: Writes user data to the `USRSEC` file with key-based access.

### **Validation Logic**
The program ensures that all mandatory fields are populated before proceeding with file operations. This prevents incomplete or invalid data from being written to the `USRSEC` file.

### **Reusability**
The modular structure (e.g., `PROCESS-ENTER-KEY`, `SEND-USRADD-SCREEN`, `WRITE-USER-SEC-FILE`) promotes reusability and maintainability.

### **Licensing**
The program is licensed under the Apache License 2.0, ensuring open-source compliance and usage guidelines.

---

## Dependencies
The program includes several copybooks:
- `COCOM01Y`, `COUSR01`, `COTTL01Y`, `CSDAT01Y`, `CSMSG01Y`, `CSUSR01Y`: Likely contain data structures and constants.
- `DFHAID`, `DFHBMSCA`: CICS-related copybooks for screen handling and transaction management.

---

## Error Handling
| **Error Condition** | **Action Taken** |
|----------------------|------------------|
| Empty mandatory fields | Displays error message and redisplays the screen. |
| Duplicate User ID | Displays "User ID already exists" message. |
| Other file write errors | Displays "Unable to Add User" message. |

---

## Transaction Flow
1. User navigates to the `COUSR1A` screen.
2. Inputs are validated.
3. If valid, user data is written to the `USRSEC` file.
4. Feedback is provided to the user (success or error messages).
5. Navigation to previous or next screens is handled based on user actions.
