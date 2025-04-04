# Documentation for `COTRN01C.cbl`

## Metadata
| **File Name** | **Application** | **Type** | **Function** |
|---------------|-----------------|----------|--------------|
| `COTRN01C.cbl` | CardDemo | CICS COBOL Program | View a Transaction from `TRANSACT` file |

---

## Overview

The program `COTRN01C` is a CICS COBOL application designed to retrieve and display transaction details from the `TRANSACT` file. It interacts with CICS services for screen handling, file access, and program control. The program is structured into multiple sections, including data declarations, logic for handling user inputs, and file operations.

---

## Sections

### **1. Identification Division**
Defines the program name (`COTRN01C`) and author (`AWS`).

---

### **2. Environment Division**
Specifies the configuration for the program's runtime environment.

---

### **3. Data Division**

#### **Working-Storage Section**
Contains variables used for program execution. Key variables include:

| **Variable Name**       | **Description**                          | **Type**         | **Initial Value** |
|--------------------------|------------------------------------------|------------------|--------------------|
| `WS-PGMNAME`             | Program name                            | PIC X(08)        | `'COTRN01C'`      |
| `WS-TRANID`              | Transaction ID                          | PIC X(04)        | `'CT01'`          |
| `WS-MESSAGE`             | Error or informational message          | PIC X(80)        | Spaces            |
| `WS-TRANSACT-FILE`       | File name for transaction data          | PIC X(08)        | `'TRANSACT'`      |
| `WS-ERR-FLG`             | Error flag                              | PIC X(01)        | `'N'`             |
| `WS-RESP-CD`             | Response code from CICS operations      | PIC S9(09) COMP  | Zeros             |
| `WS-TRAN-AMT`            | Transaction amount                      | PIC +99999999.99 | None              |
| `WS-TRAN-DATE`           | Transaction date                        | PIC X(08)        | `'00/00/00'`      |

#### **Linkage Section**
Defines the communication area (`DFHCOMMAREA`) for passing data between programs.

---

### **4. Procedure Division**

#### **Main Logic**
The main logic handles program initialization, user input processing, and screen navigation. Key operations include:

1. **Initialization**:
   - Sets error flags (`ERR-FLG-OFF`) and user modification flags (`USR-MODIFIED-NO`).
   - Clears messages (`WS-MESSAGE`).

2. **Input Handling**:
   - Checks if `EIBCALEN` (length of communication area) is zero.
   - Processes user actions based on `EIBAID` (attention identifier).

3. **Screen Navigation**:
   - Sends and receives screens (`SEND-TRNVIEW-SCREEN`, `RECEIVE-TRNVIEW-SCREEN`).
   - Handles navigation to previous or next screens (`RETURN-TO-PREV-SCREEN`).

4. **Transaction Processing**:
   - Reads transaction data from the `TRANSACT` file (`READ-TRANSACT-FILE`).
   - Populates screen fields with transaction details.

#### **Key Subroutines**

| **Subroutine Name**       | **Description**                                                                 |
|----------------------------|---------------------------------------------------------------------------------|
| `PROCESS-ENTER-KEY`        | Validates transaction ID and processes user input.                              |
| `RETURN-TO-PREV-SCREEN`    | Navigates to the previous screen or program.                                    |
| `SEND-TRNVIEW-SCREEN`      | Sends the transaction view screen to the user.                                  |
| `RECEIVE-TRNVIEW-SCREEN`   | Receives user input from the transaction view screen.                           |
| `POPULATE-HEADER-INFO`     | Populates header information for the screen.                                    |
| `READ-TRANSACT-FILE`       | Reads transaction data from the `TRANSACT` file using CICS file services.       |
| `CLEAR-CURRENT-SCREEN`     | Clears the current screen and reinitializes fields.                             |
| `INITIALIZE-ALL-FIELDS`    | Resets all fields to their default values.                                      |

---

## Insights

### **CICS Integration**
The program heavily relies on CICS services for screen handling (`SEND`, `RECEIVE`), file operations (`READ`), and program control (`XCTL`, `RETURN`). This makes it suitable for interactive applications in a mainframe environment.

### **Error Handling**
Error flags (`WS-ERR-FLG`) and response codes (`WS-RESP-CD`) are used to manage errors during file operations and user interactions. Specific error messages are displayed to guide the user.

### **Modular Design**
The program is divided into logical subroutines, making it easier to maintain and extend. Each subroutine handles a specific aspect of the program's functionality.

### **Data Structure Usage**
The program uses COBOL data structures to manage transaction details, screen fields, and communication areas. These structures are populated dynamically based on user input and file data.

### **Screen Navigation**
The program supports navigation between screens and programs, allowing users to view transaction details or return to previous screens.

### **Transaction File Access**
The `READ-TRANSACT-FILE` subroutine demonstrates how the program retrieves transaction data using CICS file services. It includes error handling for cases where the transaction ID is not found.

---

## Key Variables and Flags

| **Variable/Flag**         | **Purpose**                              |
|----------------------------|------------------------------------------|
| `ERR-FLG`                  | Indicates whether an error occurred.     |
| `USR-MODIFIED`             | Tracks if the user modified the screen.  |
| `NEXT-PAGE-FLG`            | Determines if there is a next page.      |
| `TRAN-ID`                  | Stores the transaction ID.               |
| `TRAN-AMT`                 | Stores the transaction amount.           |

---

## External Dependencies

### **COPY Statements**
The program includes several `COPY` statements to import external definitions:
- `COCOM01Y`
- `COTRN01`
- `COTTL01Y`
- `CSDAT01Y`
- `CSMSG01Y`
- `CVTRA05Y`
- `DFHAID`
- `DFHBMSCA`

These dependencies provide predefined structures and constants used throughout the program.

---

## Licensing
The program is licensed under the Apache License, Version 2.0. For details, visit [http://www.apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0).
