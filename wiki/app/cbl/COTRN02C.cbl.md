# Documentation for `COTRN02C.CBL`

## Overview

The `COTRN02C.CBL` program is a **CICS COBOL application** designed to add a new transaction to the `TRANSACT` file. It is part of the **CardDemo** application and interacts with multiple datasets and screens to validate, process, and store transaction data. The program ensures data integrity through extensive validation and error handling mechanisms.

---

## Metadata

| **Attribute**       | **Value**                     |
|----------------------|-------------------------------|
| **Program Name**     | `COTRN02C`                   |
| **Application**      | `CardDemo`                   |
| **Type**             | CICS COBOL Program           |
| **Function**         | Add a new transaction to the `TRANSACT` file |
| **Author**           | AWS                          |
| **License**          | Apache License, Version 2.0  |

---

## Key Features

1. **Transaction Management**:
   - Adds new transactions to the `TRANSACT` file.
   - Generates unique transaction IDs.
   - Copies data from the last transaction for reuse.

2. **Validation**:
   - Validates input fields such as account ID, card number, transaction amount, and dates.
   - Ensures numeric and format compliance for critical fields.

3. **Error Handling**:
   - Provides detailed error messages for invalid inputs.
   - Handles file read/write errors and CICS response codes.

4. **CICS Integration**:
   - Uses CICS commands for file operations (`READ`, `WRITE`, `STARTBR`, `ENDBR`).
   - Manages screen interactions with `SEND` and `RECEIVE` commands.

5. **Screen Interaction**:
   - Displays transaction addition screens (`TRNADD`).
   - Handles user inputs and navigates between screens.

---

## Data Structures

### Working-Storage Section

| **Variable**          | **Description**                              | **Picture Clause**       | **Default Value** |
|------------------------|----------------------------------------------|---------------------------|-------------------|
| `WS-PGMNAME`           | Program name                                | `PIC X(08)`               | `'COTRN02C'`      |
| `WS-TRANID`            | Transaction ID                              | `PIC X(04)`               | `'CT02'`          |
| `WS-MESSAGE`           | Error or informational message              | `PIC X(80)`               | Spaces            |
| `WS-TRANSACT-FILE`     | Dataset name for transactions               | `PIC X(08)`               | `'TRANSACT'`      |
| `WS-ERR-FLG`           | Error flag                                  | `PIC X(01)`               | `'N'`             |
| `WS-TRAN-AMT`          | Transaction amount                          | `PIC +99999999.99`        | -                 |
| `WS-TRAN-DATE`         | Transaction date                            | `PIC X(08)`               | `'00/00/00'`      |
| `WS-ACCT-ID-N`         | Numeric account ID                          | `PIC 9(11)`               | `0`               |
| `WS-CARD-NUM-N`        | Numeric card number                         | `PIC 9(16)`               | `0`               |
| `WS-DATE-FORMAT`       | Date format                                 | `PIC X(10)`               | `'YYYY-MM-DD'`    |

### Linkage Section

| **Variable**          | **Description**                              | **Picture Clause**       |
|------------------------|----------------------------------------------|---------------------------|
| `DFHCOMMAREA`          | Communication area for CICS                 | `PIC X(01)` (variable length) |

---

## Program Logic

### Main Flow (`MAIN-PARA`)

1. **Initialization**:
   - Resets error flags and clears messages.
   - Checks if `EIBCALEN` (CICS communication area length) is zero to determine if the program is re-entered.

2. **Screen Handling**:
   - If re-entered, processes user inputs and navigates screens based on function keys (`ENTER`, `PF3`, `PF4`, `PF5`).

3. **Transaction Processing**:
   - Validates input fields.
   - Adds a new transaction if inputs are valid.
   - Returns control to the previous screen or program.

4. **CICS Return**:
   - Returns control to CICS with the transaction ID and communication area.

---

## Key Procedures

### `PROCESS-ENTER-KEY`
- Validates input fields.
- Adds a transaction if the user confirms (`Y`).
- Displays error messages for invalid inputs.

### `VALIDATE-INPUT-KEY-FIELDS`
- Validates account ID and card number:
  - Ensures numeric values.
  - Checks for existence in the `CXACAIX` and `CCXREF` files.

### `VALIDATE-INPUT-DATA-FIELDS`
- Validates transaction details:
  - Ensures non-empty and correctly formatted fields (e.g., dates, amounts).
  - Converts and formats numeric fields.

### `ADD-TRANSACTION`
- Generates a new transaction ID.
- Writes the transaction record to the `TRANSACT` file.
- Displays a success message upon completion.

### `COPY-LAST-TRAN-DATA`
- Retrieves the last transaction from the `TRANSACT` file.
- Copies its data into the current transaction fields.

### `RETURN-TO-PREV-SCREEN`
- Navigates back to the previous screen or program.

### `SEND-TRNADD-SCREEN`
- Sends the transaction addition screen to the user.

### `RECEIVE-TRNADD-SCREEN`
- Receives user inputs from the transaction addition screen.

---

## File Operations

| **Operation**          | **Dataset**         | **Description**                                                                 |
|-------------------------|---------------------|---------------------------------------------------------------------------------|
| `READ-CXACAIX-FILE`     | `CXACAIX`          | Reads account cross-reference data.                                            |
| `READ-CCXREF-FILE`      | `CCXREF`           | Reads card cross-reference data.                                               |
| `STARTBR-TRANSACT-FILE` | `TRANSACT`         | Starts a browse operation on the transaction file.                             |
| `READPREV-TRANSACT-FILE`| `TRANSACT`         | Reads the previous record in the transaction file.                             |
| `WRITE-TRANSACT-FILE`   | `TRANSACT`         | Writes a new transaction record to the file.                                   |
| `ENDBR-TRANSACT-FILE`   | `TRANSACT`         | Ends the browse operation on the transaction file.                             |

---

## Error Handling

| **Error Condition**                  | **Message**                                      | **Action**                          |
|--------------------------------------|--------------------------------------------------|-------------------------------------|
| Invalid account ID                   | `Account ID must be Numeric...`                 | Displays error and resets field.    |
| Invalid card number                  | `Card Number must be Numeric...`                | Displays error and resets field.    |
| Empty required fields                | Specific messages for each field (e.g., `Type CD can NOT be empty...`) | Displays error and resets field.    |
| Invalid date format                  | `Orig Date should be in format YYYY-MM-DD`      | Displays error and resets field.    |
| File not found (`NOTFND`)            | Specific messages for each file (e.g., `Account ID NOT found...`) | Displays error and resets field.    |
| Duplicate transaction ID             | `Tran ID already exist...`                      | Displays error and resets field.    |
| General file operation failure       | `Unable to Add Transaction...`                  | Displays error and resets field.    |

---

## Insights

1. **CICS Integration**:
   - The program heavily relies on CICS commands for file and screen operations, making it suitable for online transaction processing systems.

2. **Validation and Error Handling**:
   - Comprehensive validation ensures data integrity and user input correctness.
   - Error messages are user-friendly and guide the user to correct inputs.

3. **Reusability**:
   - The program reuses data from the last transaction, improving user efficiency.

4. **Scalability**:
   - The modular design with separate procedures for validation, file operations, and screen handling makes the program maintainable and scalable.

5. **Licensing**:
   - The program is licensed under the Apache License, Version 2.0, allowing for modification and redistribution under specified conditions.
