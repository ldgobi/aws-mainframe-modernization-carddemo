# Documentation for `CORPT00C.CBL`

## Metadata
- **File Name**: `CORPT00C.cbl`
- **Application**: CardDemo
- **Type**: CICS COBOL Program
- **Function**: This program is designed to print transaction reports by submitting batch jobs from an online interface using an extra partition Transient Data Queue (TDQ).

---

## Overview

### Purpose
The program facilitates the generation and submission of transaction reports (Monthly, Yearly, or Custom) by interacting with CICS screens and submitting jobs to the internal reader (INTRDR). It validates user inputs, constructs job control language (JCL) records, and writes them to a TDQ for processing.

### Key Features
- **Report Types**: Supports Monthly, Yearly, and Custom transaction reports.
- **Date Validation**: Ensures valid start and end dates for custom reports.
- **Job Submission**: Constructs and submits JCL records to the TDQ.
- **CICS Integration**: Handles screen interactions and transitions between programs.

---

## Data Structures

### Working Storage Section
The `WORKING-STORAGE SECTION` contains variables used for program execution. Key variables include:

| **Variable Name**       | **Description**                                                                 | **Picture Clause**       | **Initial Value** |
|--------------------------|---------------------------------------------------------------------------------|---------------------------|-------------------|
| `WS-PGMNAME`            | Program name identifier.                                                        | `PIC X(08)`              | `'CORPT00C'`     |
| `WS-TRANID`             | Transaction ID.                                                                | `PIC X(04)`              | `'CR00'`         |
| `WS-MESSAGE`            | Message buffer for error or status messages.                                   | `PIC X(80)`              | Spaces           |
| `WS-TRANSACT-FILE`      | File name for transaction data.                                                | `PIC X(08)`              | `'TRANSACT'`     |
| `WS-ERR-FLG`            | Error flag with condition names (`ERR-FLG-ON`, `ERR-FLG-OFF`).                 | `PIC X(01)`              | `'N'`            |
| `WS-TRANSACT-EOF`       | End-of-file flag for transaction processing.                                   | `PIC X(01)`              | `'N'`            |
| `WS-SEND-ERASE-FLG`     | Flag to determine whether to erase the screen before sending.                  | `PIC X(01)`              | `'Y'`            |
| `WS-START-DATE`         | Start date structure for reports.                                              | Composite structure       | Spaces           |
| `WS-END-DATE`           | End date structure for reports.                                                | Composite structure       | Spaces           |
| `WS-DATE-FORMAT`        | Date format used for validation.                                               | `PIC X(10)`              | `'YYYY-MM-DD'`   |
| `WS-TRAN-AMT`           | Transaction amount.                                                            | `PIC +99999999.99`       | None             |
| `JCL-RECORD`            | Buffer for JCL records.                                                        | `PIC X(80)`              | Spaces           |

### Job Data
The `JOB-DATA` structure contains predefined JCL templates for job submission.

| **Field Name**          | **Description**                                                                 | **Picture Clause**       | **Initial Value** |
|--------------------------|---------------------------------------------------------------------------------|---------------------------|-------------------|
| `JOB-DATA-1`            | Contains predefined JCL lines for job submission.                               | Composite structure       | Various values   |
| `JOB-LINES`             | Redefines `JOB-DATA-1` as an array for iterative processing.                    | `PIC X(80)`              | None             |

### Linkage Section
The `LINKAGE SECTION` defines the communication area (`DFHCOMMAREA`) for passing data between programs.

| **Variable Name**       | **Description**                                                                 | **Picture Clause**       |
|--------------------------|---------------------------------------------------------------------------------|---------------------------|
| `DFHCOMMAREA`           | Communication area for inter-program data exchange.                             | `PIC X(01)` OCCURS 1 TO 32767 TIMES DEPENDING ON `EIBCALEN` |

---

## Logic

### Procedure Division
The `PROCEDURE DIVISION` contains the main logic for the program. Key paragraphs include:

#### `MAIN-PARA`
- Initializes flags and variables.
- Handles screen interactions based on the `EIBCALEN` value.
- Processes user inputs and navigates between screens.

#### `PROCESS-ENTER-KEY`
- Handles the logic for processing the "Enter" key.
- Determines the report type (Monthly, Yearly, or Custom).
- Validates date inputs for custom reports.
- Constructs and submits JCL records for job processing.

#### `SUBMIT-JOB-TO-INTRDR`
- Validates user confirmation for report submission.
- Iterates through `JOB-LINES` to construct JCL records.
- Writes JCL records to the TDQ (`JOBS`).

#### `WIRTE-JOBSUB-TDQ`
- Writes JCL records to the TDQ using the `WRITEQ TD` CICS command.
- Handles response codes and displays error messages if necessary.

#### `RETURN-TO-PREV-SCREEN`
- Navigates back to the previous screen or program.

#### `SEND-TRNRPT-SCREEN`
- Sends the transaction report screen to the user.
- Handles screen erasure based on the `SEND-ERASE-FLG`.

#### `RECEIVE-TRNRPT-SCREEN`
- Receives user inputs from the transaction report screen.

#### `POPULATE-HEADER-INFO`
- Populates header information for the screen using the current date and time.

#### `INITIALIZE-ALL-FIELDS`
- Resets all fields to their initial values.

---

## Insights

### Key Insights
1. **CICS Integration**: The program heavily relies on CICS commands (`SEND`, `RECEIVE`, `WRITEQ TD`, `RETURN`, `XCTL`) for screen interactions and data processing.
2. **Error Handling**: Flags (`WS-ERR-FLG`) and structured error messages ensure robust validation and user feedback.
3. **Dynamic JCL Construction**: The program dynamically constructs JCL records based on user inputs and predefined templates.
4. **Date Validation**: Custom date validation logic ensures accurate report generation.
5. **Modular Design**: The use of paragraphs (`PROCESS-ENTER-KEY`, `SUBMIT-JOB-TO-INTRDR`, etc.) promotes modularity and readability.

### Dependencies
- **Copybooks**: The program uses several copybooks (`COCOM01Y`, `CORPT00`, `COTTL01Y`, etc.) for shared definitions and structures.
- **External Program Calls**: Calls the `CSUTLDTC` program for date validation.

### Potential Enhancements
- **Error Logging**: Implement detailed logging for errors encountered during TDQ writes or screen interactions.
- **Dynamic Report Types**: Allow for additional report types beyond Monthly, Yearly, and Custom.
- **Improved User Feedback**: Enhance error messages for better user understanding.

---
