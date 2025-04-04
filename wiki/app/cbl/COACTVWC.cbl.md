# Documentation for `COACTVWC.cbl`

## Metadata
- **File Name**: `COACTVWC.cbl`
- **Layer**: Business Logic
- **Function**: Accept and process Account View request
- **Copyright**: Amazon.com, Inc. or its affiliates
- **License**: Apache License, Version 2.0

---

## Overview

The COBOL program `COACTVWC` is designed to handle account view requests in a CICS environment. It processes user inputs, validates them, retrieves account and customer data, and displays the results. The program includes error handling, screen initialization, and message construction functionalities.

---

## Sections

### Identification Division
- **Program ID**: `COACTVWC`
- **Date Written**: May 2022
- **Date Compiled**: Today

### Environment Division
- **Input-Output Section**: Defines the environment for file handling and CICS operations.

### Data Division
#### Working-Storage Section
This section contains variables used for:
- **CICS Processing**:
  - `WS-RESP-CD` and `WS-REAS-CD`: Response codes for CICS operations.
  - `WS-TRANID`: Transaction ID.
- **Input Flags**:
  - `WS-INPUT-FLAG`: Flags for input validation.
  - `WS-PFK-FLAG`: Flags for PF key validation.
  - `WS-EDIT-ACCT-FLAG` and `WS-EDIT-CUST-FLAG`: Flags for account and customer filters.
- **File Handling**:
  - `WS-XREF-RID`: Cross-reference record identifiers.
  - `WS-FILE-READ-FLAGS`: Flags for file read operations.
  - `WS-FILE-ERROR-MESSAGE`: Error message construction for file operations.
- **Output Messages**:
  - `WS-LONG-MSG`, `WS-INFO-MSG`, and `WS-RETURN-MSG`: Messages for user interaction.
- **Literals and Constants**:
  - Various literals for program, transaction, and file names.

#### Linkage Section
Defines the `DFHCOMMAREA` for passing data between programs.

---

### Procedure Division
#### Main Logic (`0000-MAIN`)
1. **Initialization**:
   - Handles abends using `EXEC CICS HANDLE ABEND`.
   - Initializes working storage and commarea.
   - Clears error messages.
2. **Input Handling**:
   - Processes passed data and remaps PF keys.
   - Validates AID keys and sets flags accordingly.
3. **Decision Making**:
   - Uses `EVALUATE` to determine actions based on inputs.
   - Handles different scenarios like exiting, re-entering, or processing inputs.
4. **Error Handling**:
   - Displays error messages and returns to the caller.

#### Screen Operations
1. **Send Map (`1000-SEND-MAP`)**:
   - Initializes screen variables and attributes.
   - Sends the screen map to the user.
2. **Receive Map (`2100-RECEIVE-MAP`)**:
   - Receives user inputs from the screen.
3. **Edit Inputs (`2200-EDIT-MAP-INPUTS`)**:
   - Validates individual and cross-field inputs.
   - Handles errors and sets appropriate flags.

#### Data Retrieval
1. **Read Account (`9000-READ-ACCT`)**:
   - Reads account data from the master file.
   - Retrieves associated customer data.
2. **Get Card XREF (`9200-GETCARDXREF-BYACCT`)**:
   - Reads the card cross-reference file using alternate index.
3. **Get Account Data (`9300-GETACCTDATA-BYACCT`)**:
   - Reads account master file.
4. **Get Customer Data (`9400-GETCUSTDATA-BYCUST`)**:
   - Reads customer master file.

#### Error and Debugging
1. **Plain Text Exit (`SEND-PLAIN-TEXT`)**:
   - Sends plain text messages for debugging purposes.
2. **Long Text Exit (`SEND-LONG-TEXT`)**:
   - Sends detailed error messages for debugging.

#### Abend Routine
Handles unexpected abends by sending error messages and terminating the program.

---

## Insights

### Key Features
- **CICS Integration**: The program leverages CICS commands for transaction handling, file reads, and screen interactions.
- **Error Handling**: Comprehensive error handling for file operations and user inputs.
- **Screen Management**: Dynamic screen initialization and attribute setup based on user context.
- **Data Validation**: Validates account and customer data with detailed error messages.

### Dependencies
- **Copybooks**:
  - `CVCRD01Y`, `COCOM01Y`, `DFHBMSCA`, `DFHAID`, `COTTL01Y`, `COACTVW`, `CSDAT01Y`, `CSMSG01Y`, `CSMSG02Y`, `CSUSR01Y`, `CVACT01Y`, `CVACT02Y`, `CVACT03Y`, `CVCUS01Y`.
- **Files**:
  - `ACCTDAT`, `CARDDAT`, `CUSTDAT`, `CARDAIX`, `CXACAIX`.

### Error Scenarios
- **File Not Found**: Handles missing files with detailed error messages.
- **Invalid Inputs**: Flags invalid account numbers and missing search criteria.
- **Unexpected Data**: Provides fallback mechanisms for unanticipated scenarios.

### Performance Considerations
- **CICS Commands**: Ensure efficient use of CICS commands to minimize transaction overhead.
- **Error Logging**: Use debugging routines sparingly in production environments.

---

## Tables

### Flags and Their Values

| **Flag Name**               | **88-Level Condition**         | **Value**       |
|-----------------------------|--------------------------------|-----------------|
| `WS-INPUT-FLAG`             | `INPUT-OK`                    | `'0'`           |
|                             | `INPUT-ERROR`                 | `'1'`           |
|                             | `INPUT-PENDING`               | `LOW-VALUES`    |
| `WS-PFK-FLAG`               | `PFK-VALID`                   | `'0'`           |
|                             | `PFK-INVALID`                 | `'1'`           |
|                             | `INPUT-PENDING`               | `LOW-VALUES`    |
| `WS-EDIT-ACCT-FLAG`         | `FLG-ACCTFILTER-NOT-OK`       | `'0'`           |
|                             | `FLG-ACCTFILTER-ISVALID`      | `'1'`           |
|                             | `FLG-ACCTFILTER-BLANK`        | `' '`           |
| `WS-EDIT-CUST-FLAG`         | `FLG-CUSTFILTER-NOT-OK`       | `'0'`           |
|                             | `FLG-CUSTFILTER-ISVALID`      | `'1'`           |
|                             | `FLG-CUSTFILTER-BLANK`        | `' '`           |

### Literals

| **Literal Name**            | **Value**                     |
|-----------------------------|-------------------------------|
| `LIT-THISPGM`               | `'COACTVWC'`                 |
| `LIT-THISTRANID`            | `'CAVW'`                     |
| `LIT-THISMAPSET`            | `'COACTVW '`                 |
| `LIT-THISMAP`               | `'CACTVWA'`                 |
| `LIT-ACCTFILENAME`          | `'ACCTDAT '`                 |
| `LIT-CARDFILENAME`          | `'CARDDAT '`                 |
| `LIT-CUSTFILENAME`          | `'CUSTDAT '`                 |

---

## Notes
- The program is versioned as `CardDemo_v1.0-15-g27d6c6f-68` with a timestamp of `2022-07-19 23:12:32 CDT`.
- Debugging routines (`SEND-PLAIN-TEXT` and `SEND-LONG-TEXT`) should not be used in production.
