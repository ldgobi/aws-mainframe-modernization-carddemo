# Documentation for `COUSR03C.CBL`

## Metadata
| **File Name** | **Application** | **Type** | **Function** |
|---------------|-----------------|----------|--------------|
| `COUSR03C.CBL` | CardDemo       | CICS COBOL Program | Delete a user from `USRSEC` file |

## Overview
The `COUSR03C` program is a CICS COBOL application designed to delete a user from the `USRSEC` file. It interacts with the user interface, processes user input, and performs file operations to delete user information securely. The program includes error handling, screen management, and file operations.

---

## Sections

### **1. Identification Division**
Defines the program's metadata:
- **Program ID**: `COUSR03C`
- **Author**: AWS

### **2. Environment Division**
Specifies the configuration for the program's environment.

### **3. Data Division**
#### **Working-Storage Section**
Contains variables used throughout the program:
| **Variable Name**       | **Type**       | **Purpose** |
|--------------------------|----------------|-------------|
| `WS-PGMNAME`            | `PIC X(08)`    | Program name (`COUSR03C`). |
| `WS-TRANID`             | `PIC X(04)`    | Transaction ID (`CU03`). |
| `WS-MESSAGE`            | `PIC X(80)`    | Stores error or informational messages. |
| `WS-USRSEC-FILE`        | `PIC X(08)`    | File name (`USRSEC`). |
| `WS-ERR-FLG`            | `PIC X(01)`    | Error flag (`Y` for error, `N` for no error). |
| `WS-RESP-CD`            | `PIC S9(09) COMP` | Response code for CICS operations. |
| `WS-REAS-CD`            | `PIC S9(09) COMP` | Reason code for CICS operations. |
| `WS-USR-MODIFIED`       | `PIC X(01)`    | Indicates if the user was modified (`Y` or `N`). |

#### **Linkage Section**
Defines the communication area (`DFHCOMMAREA`) for passing data between programs.

---

### **4. Procedure Division**
The main logic of the program is implemented here.

#### **Main-PARA**
- Initializes flags and variables.
- Handles the `DFHCOMMAREA` based on its length (`EIBCALEN`).
- Processes user input and navigates between screens.
- Executes CICS `RETURN` to transfer control back to the transaction.

#### **Key Subroutines**
| **Subroutine**            | **Purpose** |
|----------------------------|-------------|
| `PROCESS-ENTER-KEY`       | Validates user input and reads user information from the `USRSEC` file. |
| `DELETE-USER-INFO`        | Deletes user information from the `USRSEC` file. |
| `RETURN-TO-PREV-SCREEN`   | Navigates back to the previous screen. |
| `SEND-USRDEL-SCREEN`      | Sends the user deletion screen to the terminal. |
| `RECEIVE-USRDEL-SCREEN`   | Receives user input from the deletion screen. |
| `POPULATE-HEADER-INFO`    | Populates header information for the screen. |
| `READ-USER-SEC-FILE`      | Reads user information from the `USRSEC` file. |
| `DELETE-USER-SEC-FILE`    | Deletes user information from the `USRSEC` file. |
| `CLEAR-CURRENT-SCREEN`    | Clears the current screen and reinitializes fields. |
| `INITIALIZE-ALL-FIELDS`   | Resets all fields to their default values. |

---

## Insights

### **Error Handling**
- The program uses the `WS-ERR-FLG` flag to track errors.
- Error messages are stored in `WS-MESSAGE` and displayed to the user.
- CICS response codes (`WS-RESP-CD` and `WS-REAS-CD`) are evaluated to handle file operation errors.

### **File Operations**
- **Read**: Retrieves user information from the `USRSEC` file using the `READ-USER-SEC-FILE` subroutine.
- **Delete**: Removes user information from the `USRSEC` file using the `DELETE-USER-SEC-FILE` subroutine.

### **Screen Management**
- Screens are sent and received using CICS `SEND` and `RECEIVE` commands.
- The program dynamically updates screen content based on user actions and system responses.

### **User Input Validation**
- Ensures the `User ID` is not empty before performing operations.
- Displays appropriate error messages for invalid or missing input.

### **Navigation**
- Supports navigation between screens using function keys (`PF3`, `PF4`, `PF5`, `PF12`).
- Transfers control to other programs when necessary.

### **Reusability**
- The program uses modular subroutines, making it easier to maintain and extend functionality.

### **Licensing**
- The program is licensed under the Apache License, Version 2.0.

---

## Dependencies
The program includes several copybooks:
| **Copybook Name** | **Purpose** |
|--------------------|-------------|
| `COCOM01Y`        | Contains common variables and structures. |
| `COUSR03`         | Defines screen-related structures. |
| `COTTL01Y`        | Title-related information. |
| `CSDAT01Y`        | Date-related structures. |
| `CSMSG01Y`        | Message-related structures. |
| `CSUSR01Y`        | User-related structures. |
| `DFHAID`          | CICS attention identifier definitions. |
| `DFHBMSCA`        | CICS basic mapping support control area. |

---

## Version Information
| **Version** | **Date** | **Time** |
|-------------|----------|----------|
| `CardDemo_v1.0-15-g27d6c6f-68` | `2022-07-19` | `23:12:35 CDT` |
