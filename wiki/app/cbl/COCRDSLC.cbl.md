# Documentation for `COCRDSLC.cbl`

## Metadata
- **File Name**: `COCRDSLC.cbl`
- **Layer**: Business Logic
- **Function**: Accept and process credit card detail requests
- **Copyright**: Amazon.com, Inc. or its affiliates
- **License**: Apache License, Version 2.0

---

## Overview

The program `COCRDSLC.cbl` is designed to handle credit card detail requests. It includes functionality for input validation, data retrieval, and user interaction via CICS screens. The program processes account and card numbers, validates inputs, retrieves card data from files, and displays results or error messages to the user.

---

## Sections

### Identification Division
- **Program ID**: `COCRDSLC`
- **Date Written**: April 2022
- **Date Compiled**: Today

### Environment Division
- **Input-Output Section**: Defines the environment for file handling and CICS operations.

### Data Division
#### Working-Storage Section
This section contains variables for:
- **CICS Processing Variables**: Response codes, transaction IDs.
- **Input Flags**: Validation flags for account and card numbers.
- **Output Variables**: Card details such as account ID, CVV, card number, embossed name, expiration date, and status.
- **Error Messages**: Constructs error messages for file handling and input validation.
- **Screen Messages**: Messages displayed to the user based on input and processing results.
- **Literals and Constants**: Predefined values for program, transaction, and file names.

#### Linkage Section
Defines the `DFHCOMMAREA` for communication between CICS programs.

---

## Procedure Division

### Main Logic (`0000-MAIN`)
1. **Initialization**:
   - Handles abends using `EXEC CICS HANDLE ABEND`.
   - Initializes working storage and commarea variables.
   - Clears error messages.

2. **Input Handling**:
   - Stores passed data or initializes commarea if no data is provided.
   - Maps PF keys and validates AID (Attention Identifier).

3. **Input Evaluation**:
   - Processes user inputs based on the context (e.g., PF keys, re-entering program).
   - Validates account and card numbers.
   - Reads data from files and sends appropriate screens.

4. **Error Handling**:
   - Displays error messages if input validation fails.

5. **Return Logic**:
   - Returns control to the calling program or menu with updated commarea.

---

### Subroutines

#### `1000-SEND-MAP`
- Initializes screen variables and attributes.
- Sends the screen to the user.

#### `2000-PROCESS-INPUTS`
- Receives user inputs from the screen.
- Validates inputs and sets flags for errors or valid data.

#### `9000-READ-DATA`
- Reads card data from files based on account and card numbers.

#### `9100-GETCARD-BYACCTCARD`
- Reads card data using the primary key (card number).

#### `9150-GETCARD-BYACCT`
- Reads card data using an alternate index (account ID).

#### `SEND-LONG-TEXT`
- Sends long text messages for debugging purposes.

#### `SEND-PLAIN-TEXT`
- Sends plain text messages for debugging purposes.

#### `ABEND-ROUTINE`
- Handles unexpected abends and sends error messages.

---

## Key Features

### Input Validation
- Validates account numbers (must be 11 digits, numeric, and non-zero).
- Validates card numbers (must be 16 digits and numeric).
- Handles blank or invalid inputs with appropriate error messages.

### Data Retrieval
- Reads card data from files using CICS `READ` commands.
- Supports primary and alternate index access.

### User Interaction
- Displays screens with account and card details.
- Provides error messages and prompts for user input.

### Error Handling
- Constructs detailed error messages for file handling and input validation.
- Handles unexpected abends gracefully.

---

## Insights

### Strengths
- **Robust Input Validation**: Ensures data integrity by validating account and card numbers.
- **CICS Integration**: Leverages CICS commands for screen handling and file operations.
- **Modular Design**: Organized into subroutines for better maintainability and readability.

### Potential Improvements
- **Error Logging**: Implement detailed logging for debugging and monitoring.
- **Dynamic Screen Updates**: Enhance user experience by dynamically updating screen attributes based on context.
- **Performance Optimization**: Optimize file access operations for large datasets.

### Dependencies
- **Copybooks**: Includes several copybooks for common variables, screen layouts, and dataset structures.
- **CICS Environment**: Requires a CICS environment for execution.

---

## Tables

### Flags and Their Values

| **Flag Name**               | **Description**                     | **Values**                  |
|-----------------------------|-------------------------------------|----------------------------|
| `WS-INPUT-FLAG`             | Input validation status            | `0` (OK), `1` (Error), LOW-VALUES (Pending) |
| `WS-EDIT-ACCT-FLAG`         | Account filter validation          | `0` (Not OK), `1` (Valid), SPACE (Blank) |
| `WS-EDIT-CARD-FLAG`         | Card filter validation             | `0` (Not OK), `1` (Valid), SPACE (Blank) |
| `WS-RETURN-FLAG`            | Return message status              | LOW-VALUES (Off), `1` (On) |
| `WS-PFK-FLAG`               | PF key validation                  | `0` (Valid), `1` (Invalid) |

### Literals and Constants

| **Literal Name**            | **Value**                          | **Description**             |
|-----------------------------|-------------------------------------|-----------------------------|
| `LIT-THISPGM`               | `COCRDSLC`                         | Current program name        |
| `LIT-THISTRANID`            | `CCDL`                             | Current transaction ID      |
| `LIT-CARDFILENAME`          | `CARDDAT`                          | Card data file name         |
| `LIT-CARDFILENAME-ACCT-PATH`| `CARDAIX`                          | Alternate index file name   |

---

## Error Messages

### File Handling Errors
| **Error Component** | **Description**                     |
|---------------------|-------------------------------------|
| `ERROR-OPNAME`      | Operation causing the error        |
| `ERROR-FILE`        | File involved in the error         |
| `ERROR-RESP`        | Response code                     |
| `ERROR-RESP2`       | Secondary response code            |

### Input Validation Errors
| **Error Message**                          | **Condition**                                   |
|-------------------------------------------|-----------------------------------------------|
| `Account number must be a non-zero 11 digit number` | Invalid account number format or value        |
| `Card number if supplied must be a 16 digit number` | Invalid card number format or value           |
| `No input received`                       | Both account and card numbers are blank       |

---

## Debugging Utilities
- **Long Text Messages**: Used for debugging purposes, not recommended for production.
- **Plain Text Messages**: Sends plain text error messages for debugging.

---
