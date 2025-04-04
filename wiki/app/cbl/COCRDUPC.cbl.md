# Documentation for COBOL Program: COCRDUPC.cbl

## Metadata
- **File Name**: COCRDUPC.cbl
- **Layer**: Business Logic
- **Function**: Accept and process credit card detail requests
- **Copyright**: Amazon.com, Inc. or its affiliates
- **License**: Apache License, Version 2.0

---

## Overview
The program `COCRDUPC.cbl` is designed to handle credit card detail requests, including validation, updates, and user interaction through CICS screens. It processes user inputs, validates data, fetches associated card details, and updates card records in the database.

---

## Sections and Key Components

### Identification Division
- **Program ID**: `COCRDUPC`
- **Date Written**: April 2022
- **Date Compiled**: Today

### Environment Division
- **Input-Output Section**: Defines the interaction with external files and CICS.

### Data Division
#### Working-Storage Section
This section contains variables for:
1. **CICS Processing Variables**:
   - `WS-RESP-CD`, `WS-REAS-CD`: Response codes for CICS operations.
   - `WS-TRANID`, `WS-UCTRANS`: Transaction identifiers.

2. **Input Flags**:
   - Flags for validating account, card, name, status, expiration month/year.

3. **Output Variables**:
   - Variables for constructing output messages and screen attributes.

4. **File Handling**:
   - `WS-CARD-RID`: Record identifiers for card data.
   - `WS-FILE-ERROR-MESSAGE`: Error message construction for file operations.

5. **Screen Messages**:
   - Predefined messages for user prompts, confirmations, and errors.

6. **Literals and Constants**:
   - Program-specific literals such as transaction IDs, map names, and file names.

#### Linkage Section
Defines the communication area (`DFHCOMMAREA`) for passing data between programs.

---

### Procedure Division
#### Main Logic (`0000-MAIN`)
1. **Initialization**:
   - Handles abend scenarios.
   - Initializes working storage and commarea variables.
   - Clears error messages.

2. **Input Handling**:
   - Processes user inputs and validates them.
   - Maps PF keys and checks AID (Attention Identifier).

3. **Decision Making**:
   - Evaluates user actions (e.g., exit, update, fetch details).
   - Executes corresponding logic based on user inputs.

4. **Screen Interaction**:
   - Sends screens to the user for input or confirmation.
   - Sets up screen variables, attributes, and messages.

#### Input Processing (`1000-PROCESS-INPUTS`)
- Receives and validates user inputs from the screen.
- Edits account, card, name, status, and expiration details.

#### Decision Action (`2000-DECIDE-ACTION`)
- Determines the next steps based on user inputs and program state:
  - Fetch card details.
  - Validate changes.
  - Save updates.

#### Screen Handling (`3000-SEND-MAP`)
- Initializes screen variables and attributes.
- Sends the screen to the user.

#### Data Handling (`9000-READ-DATA`)
- Reads card data from the file based on account and card number.
- Maps the fetched data to working storage variables.

#### Update Processing (`9200-WRITE-PROCESSING`)
- Locks the record for update.
- Validates changes and rewrites the record in the file.

#### Error Handling (`ABEND-ROUTINE`)
- Handles unexpected errors and sends abend messages.

---

## Insights

### Key Features
1. **Validation**:
   - Comprehensive validation of user inputs, including numeric checks, length constraints, and logical conditions.

2. **CICS Integration**:
   - Utilizes CICS commands for screen interaction, file operations, and transaction management.

3. **Error Handling**:
   - Constructs detailed error messages for file operations and user input errors.

4. **Dynamic Screen Management**:
   - Adjusts screen attributes and messages based on program state and user actions.

5. **Data Integrity**:
   - Ensures data consistency by checking for changes before updates.

### Dependencies
- **Copybooks**:
  - Common copybooks for screen layouts, messages, and dataset structures.
  - Examples: `COCRDUP`, `CSMSG01Y`, `CVACT02Y`.

### Potential Enhancements
1. **Modularization**:
   - Further modularize repetitive logic for better maintainability.
2. **Error Logging**:
   - Implement detailed logging for debugging and monitoring.
3. **Performance Optimization**:
   - Optimize file operations and reduce redundant data movements.

---

## Tables

### Flags and Their Values

| **Flag Name**               | **Valid Values**                  | **Description**                          |
|-----------------------------|-----------------------------------|------------------------------------------|
| `INPUT-OK`                 | `'0'`, `'1'`, `LOW-VALUES`       | Indicates input status.                  |
| `FLG-ACCTFILTER-ISVALID`   | `'0'`, `'1'`, `' '`              | Validates account filter.                |
| `FLG-CARDFILTER-ISVALID`   | `'0'`, `'1'`, `' '`              | Validates card filter.                   |
| `FLG-CARDNAME-ISVALID`     | `'0'`, `'1'`, `' '`              | Validates card name.                     |
| `FLG-CARDSTATUS-ISVALID`   | `'0'`, `'1'`, `' '`              | Validates card status.                   |
| `FLG-CARDEXPMON-ISVALID`   | `'0'`, `'1'`, `' '`              | Validates card expiration month.         |
| `FLG-CARDEXPYEAR-ISVALID`  | `'0'`, `'1'`, `' '`              | Validates card expiration year.          |

### Predefined Messages

| **Message Name**            | **Value**                                      | **Purpose**                              |
|-----------------------------|-----------------------------------------------|------------------------------------------|
| `FOUND-CARDS-FOR-ACCOUNT`   | `'Details of selected card shown above'`      | Indicates successful card fetch.         |
| `PROMPT-FOR-SEARCH-KEYS`    | `'Please enter Account and Card Number'`      | Prompts user for search criteria.        |
| `CONFIRM-UPDATE-SUCCESS`    | `'Changes committed to database'`             | Confirms successful update.              |
| `INFORM-FAILURE`            | `'Changes unsuccessful. Please try again'`    | Indicates update failure.                |

---

## Program Flow

### High-Level Flow
1. **Initialization**:
   - Set up working storage and commarea.
2. **Input Validation**:
   - Validate user inputs and fetch card details.
3. **Decision Making**:
   - Determine actions based on user inputs and program state.
4. **Screen Interaction**:
   - Display screens and messages to the user.
5. **Data Update**:
   - Lock records, validate changes, and update the database.
6. **Error Handling**:
   - Handle unexpected errors and abends.

### Detailed Flow
1. **User Entry**:
   - User enters account and card details.
2. **Validation**:
   - Validate inputs for correctness.
3. **Fetch Details**:
   - Retrieve card details from the database.
4. **Update Details**:
   - User modifies card details and confirms changes.
5. **Save Changes**:
   - Update the database with new card details.

---

## Dependencies

### Copybooks Used
| **Copybook Name** | **Purpose**                          |
|-------------------|--------------------------------------|
| `COCRDUP`         | Credit card update screen layout.   |
| `CSMSG01Y`        | Common messages.                    |
| `CVACT02Y`        | Card record layout.                 |

### External Files
| **File Name**      | **Purpose**                          |
|--------------------|--------------------------------------|
| `CARDDAT`          | Stores card data.                   |
| `CARDAIX`          | Stores account index data.          |

---

## Error Scenarios

### Common Errors
| **Error Type**                | **Description**                          |
|-------------------------------|------------------------------------------|
| `File Error`                  | Issues during file read/write operations.|
| `Input Validation Error`      | Invalid user inputs.                     |
| `Record Lock Error`           | Unable to lock record for update.        |
| `Data Change Error`           | Record modified by another user.         |

### Error Messages
| **Message**                   | **Scenario**                             |
|-------------------------------|------------------------------------------|
| `'Account number not provided'` | Missing account number input.           |
| `'Card number not provided'`   | Missing card number input.              |
| `'Card name can only contain alphabets and spaces'` | Invalid card name. |

---
