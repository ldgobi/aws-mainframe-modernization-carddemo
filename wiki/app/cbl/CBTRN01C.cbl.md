# CBTRN01C Documentation

## Metadata
| **File Name** | **Application** | **Type** | **Function** |
|---------------|-----------------|----------|--------------|
| CBTRN01C.cbl  | CardDemo        | Batch COBOL Program | Post the records from daily transaction file |

## Overview
The `CBTRN01C` program is a batch COBOL application designed to process records from a daily transaction file (`DALYTRAN-FILE`) and perform operations such as validating card numbers, retrieving account information, and handling errors. It interacts with multiple indexed files, including customer, cross-reference, card, account, and transaction files.

## File Definitions
The program uses several files with specific attributes:

| **File Name**       | **Organization** | **Access Mode** | **Record Key**         | **File Status**         |
|----------------------|------------------|-----------------|------------------------|-------------------------|
| `DALYTRAN-FILE`      | Sequential       | Sequential      | N/A                    | `DALYTRAN-STATUS`       |
| `CUSTOMER-FILE`      | Indexed          | Random          | `FD-CUST-ID`           | `CUSTFILE-STATUS`       |
| `XREF-FILE`          | Indexed          | Random          | `FD-XREF-CARD-NUM`     | `XREFFILE-STATUS`       |
| `CARD-FILE`          | Indexed          | Random          | `FD-CARD-NUM`          | `CARDFILE-STATUS`       |
| `ACCOUNT-FILE`       | Indexed          | Random          | `FD-ACCT-ID`           | `ACCTFILE-STATUS`       |
| `TRANSACT-FILE`      | Indexed          | Random          | `FD-TRANS-ID`          | `TRANFILE-STATUS`       |

## Data Structures
### File Section
The program defines the following file structures:

| **File**            | **Record Structure** |
|---------------------|-----------------------|
| `DALYTRAN-FILE`     | `FD-TRAN-RECORD` with fields: `FD-TRAN-ID` (16 chars), `FD-CUST-DATA` (334 chars) |
| `CUSTOMER-FILE`     | `FD-CUSTFILE-REC` with fields: `FD-CUST-ID` (9 digits), `FD-CUST-DATA` (491 chars) |
| `XREF-FILE`         | `FD-XREFFILE-REC` with fields: `FD-XREF-CARD-NUM` (16 chars), `FD-XREF-DATA` (34 chars) |
| `CARD-FILE`         | `FD-CARDFILE-REC` with fields: `FD-CARD-NUM` (16 chars), `FD-CARD-DATA` (134 chars) |
| `ACCOUNT-FILE`      | `FD-ACCTFILE-REC` with fields: `FD-ACCT-ID` (11 digits), `FD-ACCT-DATA` (289 chars) |
| `TRANSACT-FILE`     | `FD-TRANFILE-REC` with fields: `FD-TRANS-ID` (16 chars), `FD-ACCT-DATA` (334 chars) |

### Working-Storage Section
The program defines various working-storage variables for file statuses, binary conversions, and control flags:

| **Variable Name**         | **Description** |
|---------------------------|-----------------|
| `DALYTRAN-STATUS`         | Status of the daily transaction file |
| `CUSTFILE-STATUS`         | Status of the customer file |
| `XREFFILE-STATUS`         | Status of the cross-reference file |
| `CARDFILE-STATUS`         | Status of the card file |
| `ACCTFILE-STATUS`         | Status of the account file |
| `TRANFILE-STATUS`         | Status of the transaction file |
| `END-OF-DAILY-TRANS-FILE` | Flag indicating end of daily transaction file |
| `APPL-RESULT`             | Application result code |
| `WS-XREF-READ-STATUS`     | Status of cross-reference file read operation |
| `WS-ACCT-READ-STATUS`     | Status of account file read operation |

## Logic
### Main Flow
1. **Initialization**:
   - Opens all required files (`DALYTRAN-FILE`, `CUSTOMER-FILE`, `XREF-FILE`, `CARD-FILE`, `ACCOUNT-FILE`, `TRANSACT-FILE`).
   - Displays the start of execution.

2. **Processing Records**:
   - Reads records from `DALYTRAN-FILE` until the end of the file.
   - For each record:
     - Validates the card number using `XREF-FILE`.
     - Retrieves account information from `ACCOUNT-FILE`.
     - Displays error messages for invalid card numbers or missing accounts.

3. **Termination**:
   - Closes all files.
   - Displays the end of execution.

### Error Handling
- Errors during file operations (open, read, close) are handled by:
  - Displaying error messages.
  - Performing `Z-DISPLAY-IO-STATUS` to log file status.
  - Abending the program using `Z-ABEND-PROGRAM`.

### Key Procedures
| **Procedure Name**       | **Description** |
|--------------------------|-----------------|
| `0000-DALYTRAN-OPEN`     | Opens the daily transaction file |
| `0100-CUSTFILE-OPEN`     | Opens the customer file |
| `0200-XREFFILE-OPEN`     | Opens the cross-reference file |
| `0300-CARDFILE-OPEN`     | Opens the card file |
| `0400-ACCTFILE-OPEN`     | Opens the account file |
| `0500-TRANFILE-OPEN`     | Opens the transaction file |
| `1000-DALYTRAN-GET-NEXT` | Reads the next record from the daily transaction file |
| `2000-LOOKUP-XREF`       | Validates the card number using the cross-reference file |
| `3000-READ-ACCOUNT`      | Retrieves account information from the account file |
| `9000-DALYTRAN-CLOSE`    | Closes the daily transaction file |
| `9100-CUSTFILE-CLOSE`    | Closes the customer file |
| `9200-XREFFILE-CLOSE`    | Closes the cross-reference file |
| `9300-CARDFILE-CLOSE`    | Closes the card file |
| `9400-ACCTFILE-CLOSE`    | Closes the account file |
| `9500-TRANFILE-CLOSE`    | Closes the transaction file |
| `Z-DISPLAY-IO-STATUS`    | Displays file status for debugging |
| `Z-ABEND-PROGRAM`        | Abends the program in case of critical errors |

## Insights
- **File Status Codes**: The program uses file status codes (`00`, `10`, `12`) to determine success, end-of-file, or error conditions.
- **Error Recovery**: The program includes robust error handling mechanisms, ensuring that file operations are logged and critical errors result in program termination.
- **Sequential and Indexed Access**: The program demonstrates the use of both sequential and indexed file access modes, showcasing COBOL's capabilities in handling different file types.
- **Control Flags**: Flags like `END-OF-DAILY-TRANS-FILE` and `APPL-RESULT` are used to manage program flow and error conditions effectively.
- **Modular Design**: The program is structured into modular procedures, making it easier to maintain and debug.
