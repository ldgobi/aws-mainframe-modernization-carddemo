# Documentation for `COCRDLIC.cbl`

## Metadata
- **File Name**: `COCRDLIC.cbl`
- **Layer**: Business Logic
- **Function**: List Credit Cards
  - **Behavior**:
    - Lists all credit cards if no context is passed and the user is an admin.
    - Lists only credit cards associated with the account in the COMMAREA if the user is not an admin.

---

## Overview

The program `COCRDLIC.cbl` is designed to manage and display credit card information based on user context and permissions. It interacts with CICS (Customer Information Control System) for transaction management and screen handling. The program supports filtering, pagination, and navigation through credit card records.

---

## Sections

### Identification Division
- **Program ID**: `COCRDLIC`
- **Date Written**: April 2022
- **Date Compiled**: Today

### Environment Division
- **Input-Output Section**: Defines interaction with CICS and datasets.

### Data Division
#### Working-Storage Section
Contains variables for:
- **CICS Processing**: Response codes, transaction IDs.
- **Input Validation**: Flags for account and card filters.
- **Output Construction**: Messages, screen data, and attributes.
- **File Handling**: Variables for reading and filtering records.
- **Constants**: Program-specific literals and screen layout identifiers.

#### Linkage Section
Defines the `DFHCOMMAREA` for passing data between programs.

---

## Key Features

### Input Handling
- **Account Filter**:
  - Must be an 11-digit numeric value.
  - Validates input and sets appropriate flags.
- **Card Filter**:
  - Must be a 16-digit numeric value.
  - Validates input and sets appropriate flags.

### Output Handling
- Constructs screen messages and attributes based on user actions and program state.
- Displays credit card records in a paginated format.

### Navigation
- **PF Keys**:
  - `PF3`: Exit to the main menu.
  - `PF7`: Page up.
  - `PF8`: Page down.
  - `Enter`: View or update selected records.

### Record Filtering
- Filters records based on account and card filters.
- Supports pagination with `READNEXT` and `READPREV` operations.

### Error Handling
- Displays error messages for invalid inputs or system errors.
- Handles end-of-file conditions gracefully.

---

## Procedures

### Main Procedure (`0000-MAIN`)
- Initializes working storage and COMMAREA.
- Handles user context and navigation.
- Processes user inputs and determines actions based on PF keys.

### Screen Initialization (`1100-SCREEN-INIT`)
- Sets up screen titles, transaction details, and current date/time.

### Screen Array Initialization (`1200-SCREEN-ARRAY-INIT`)
- Populates screen rows with credit card data.

### Input Validation (`2200-EDIT-INPUTS`)
- Validates account and card filters.
- Ensures only one record is selected for view or update.

### Record Reading
- **Forward (`9000-READ-FORWARD`)**:
  - Reads records sequentially and populates the screen.
  - Handles pagination and end-of-file conditions.
- **Backward (`9100-READ-BACKWARDS`)**:
  - Reads records in reverse for page-up functionality.

### Filtering (`9500-FILTER-RECORDS`)
- Excludes records that do not match the account or card filters.

---

## Insights

### Program Design
- The program is modular, with clear separation of concerns for input handling, output construction, and navigation.
- Extensive use of flags and conditions ensures robust validation and error handling.

### CICS Integration
- Utilizes CICS commands (`SEND`, `RECEIVE`, `STARTBR`, `READNEXT`, `READPREV`, `ENDBR`) for transaction and screen management.
- COMMAREA is used for passing context between programs.

### User Experience
- Supports dynamic navigation and filtering of credit card records.
- Provides informative error messages and guidance for user actions.

### Scalability
- Pagination and filtering mechanisms allow the program to handle large datasets efficiently.

---

## Tables

### Flags and Their Meanings

| **Flag**                     | **Description**                              |
|-------------------------------|----------------------------------------------|
| `INPUT-OK`                   | Input validation passed.                     |
| `INPUT-ERROR`                | Input validation failed.                     |
| `FLG-ACCTFILTER-ISVALID`     | Account filter is valid.                     |
| `FLG-ACCTFILTER-NOT-OK`      | Account filter is invalid.                   |
| `FLG-CARDFILTER-ISVALID`     | Card filter is valid.                        |
| `FLG-CARDFILTER-NOT-OK`      | Card filter is invalid.                      |
| `CA-FIRST-PAGE`              | User is on the first page.                   |
| `CA-NEXT-PAGE-EXISTS`        | Next page exists.                            |
| `CA-NEXT-PAGE-NOT-EXISTS`    | No next page exists.                         |

### PF Key Actions

| **PF Key** | **Action**                     |
|------------|--------------------------------|
| `PF3`      | Exit to main menu.            |
| `PF7`      | Page up.                      |
| `PF8`      | Page down.                    |
| `Enter`    | View or update selected record.|

---

## Error Messages

| **Error Message**                          | **Condition**                                      |
|--------------------------------------------|---------------------------------------------------|
| `ACCOUNT FILTER, IF SUPPLIED MUST BE A 11 DIGIT NUMBER` | Invalid account filter input.                     |
| `CARD ID FILTER, IF SUPPLIED MUST BE A 16 DIGIT NUMBER` | Invalid card filter input.                        |
| `NO RECORDS FOUND FOR THIS SEARCH CONDITION.` | No records match the search criteria.             |
| `NO MORE RECORDS TO SHOW`                  | End of file reached during pagination.            |
| `PLEASE SELECT ONLY ONE RECORD TO VIEW OR UPDATE` | Multiple records selected for action.             |
| `INVALID ACTION CODE`                      | Invalid user action detected.                     |

---

## Constants

| **Constant**       | **Value**       | **Description**                     |
|--------------------|-----------------|-------------------------------------|
| `LIT-THISPGM`      | `COCRDLIC`      | Current program name.               |
| `LIT-THISTRANID`   | `CCLI`          | Current transaction ID.             |
| `LIT-THISMAPSET`   | `COCRDLI`       | Current mapset name.                |
| `LIT-THISMAP`      | `CCRDLIA`       | Current map name.                   |
| `LIT-MENUPGM`      | `COMEN01C`      | Main menu program name.             |
| `LIT-MENUMAPSET`   | `COMEN01`       | Main menu mapset name.              |
| `LIT-MENUMAP`      | `COMEN1A`       | Main menu map name.                 |

---

## External Dependencies

### Copybooks
- `CVCRD01Y`: Credit card record layout.
- `COCOM01Y`: Application COMMAREA layout.
- `DFHBMSCA`, `DFHAID`: IBM-supplied copybooks for CICS.
- `COTTL01Y`: Screen titles.
- `COCRDLI`: Credit card list screen layout.
- `CSDAT01Y`: Current date handling.
- `CSMSG01Y`: Common messages.
- `CSUSR01Y`: Signed-on user data.
- `CVACT02Y`: Dataset layout for card records.

### Dataset
- `CARDDAT`: Credit card data file.
- `CARDAIX`: Account index path.

---
