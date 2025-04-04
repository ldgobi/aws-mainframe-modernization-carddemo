# COACTVWC.cbl: Account View Request Processing

## Overview

The `COACTVWC.cbl` file is a COBOL program designed to handle and process account view requests within a business application. It operates in the business logic layer and interacts with CICS (Customer Information Control System) to manage transactions, retrieve data from various files, and display account and customer information. The program is responsible for validating user inputs, retrieving account and customer data, and presenting the information in a structured format. It also handles error scenarios and provides appropriate feedback to the user.

The main objectives of this program are:
1. **Input Validation**: Ensure that the account number provided by the user is valid and meets specific criteria.
2. **Data Retrieval**: Access account, customer, and cross-reference files to gather relevant information.
3. **Error Handling**: Manage errors during file access or input validation and provide meaningful error messages.
4. **Screen Interaction**: Display account and customer details on the screen and prompt the user for further actions.

This program contributes to the overall functionality of the system by enabling users to view detailed account and customer information, which is essential for customer service and account management processes.

<!-- general-rule-start -->
## Business Rules:

### Input Validation
1. **Account Number Validation**:
   - The account number must be a non-zero, 11-digit numeric value.
   - If the account number is missing, blank, or contains invalid characters, the program sets an error flag and displays an appropriate error message.
   - Valid account numbers are stored in the `CDEMO-ACCT-ID` field for further processing.

2. **PF Key Validation**:
   - The program checks for valid PF keys (e.g., PF03 for exiting the screen).
   - Invalid PF keys are flagged, and the user is prompted to re-enter valid input.

### Data Retrieval
1. **Cross-Reference File (`CXACAIX`)**:
   - The program reads the cross-reference file using the account ID as the key.
   - If the account is found, the associated customer ID and card number are retrieved and stored.
   - If the account is not found, an error message is constructed, indicating the account is missing in the cross-reference file.

2. **Account Master File (`ACCTDAT`)**:
   - The program reads the account master file using the account ID as the key.
   - If the account is found, details such as account status, balance, credit limits, and dates are retrieved.
   - If the account is not found, an error message is constructed, indicating the account is missing in the master file.

3. **Customer Master File (`CUSTDAT`)**:
   - The program reads the customer master file using the customer ID as the key.
   - If the customer is found, details such as name, address, phone numbers, and government-issued ID are retrieved.
   - If the customer is not found, an error message is constructed, indicating the customer is missing in the master file.

### Screen Interaction
1. **Screen Initialization**:
   - The program initializes screen variables and sets up titles, transaction IDs, and program names.
   - Current date and time are displayed on the screen.

2. **Screen Setup**:
   - Account and customer details are populated into screen fields (`CACTVWAO`).
   - Error messages and informational prompts are displayed based on the context.

3. **Screen Attributes**:
   - Fields are protected or unprotected based on the context (e.g., invalid account filter).
   - Cursor positioning and color settings are applied to highlight errors or prompt user input.

4. **Screen Display**:
   - The program sends the screen map to the user using CICS commands.
   - The user is prompted to enter or update the account ID for display.

### Error Handling
1. **File Access Errors**:
   - If a file read operation fails, the program constructs a detailed error message, including the file name, operation name, response codes, and reason codes.
   - Errors are displayed to the user, and the program exits gracefully.

2. **Unexpected Scenarios**:
   - If unexpected data scenarios occur, the program sets an error message and abends with a specific code (`9999`).

### Data Mapping
1. **Account Data Mapping**:
   - Fields such as account status, balance, credit limits, and dates are mapped to screen fields (`CACTVWAO`).

2. **Customer Data Mapping**:
   - Fields such as name, address, phone numbers, and government-issued ID are mapped to screen fields (`CACTVWAO`).
   - The customer's SSN is formatted as `XXX-XX-XXXX` before being displayed.

### Transaction Flow
1. **Initial Setup**:
   - The program initializes working storage variables and clears error messages.
   - Passed data is stored in the commarea for context.

2. **Input Processing**:
   - The program receives user input from the screen and validates it.
   - If errors are found, the user is prompted to correct the input.

3. **Data Retrieval**:
   - The program retrieves data from the cross-reference, account master, and customer master files.
   - Retrieved data is mapped to screen fields for display.

4. **Screen Display**:
   - The program sends the screen map to the user, displaying account and customer details or error messages.

5. **Exit Handling**:
   - If the user presses PF03, the program exits and returns control to the calling program or main menu.

### Constants and Literals
1. **Program Identifiers**:
   - `COACTVWC` is the program name.
   - `CAVW` is the transaction ID.

2. **File Names**:
   - `ACCTDAT` for account master file.
   - `CARDDAT` for card file.
   - `CUSTDAT` for customer master file.
   - `CXACAIX` for cross-reference file.

3. **Screen Identifiers**:
   - `CACTVWA` is the screen map name.
   - `COACTVW` is the mapset name.

### Error Messages
1. **General Errors**:
   - "Account number must be a non-zero 11-digit number."
   - "Did not find this account in account card xref file."
   - "Did not find this account in account master file."
   - "Did not find associated customer in master file."

2. **File Errors**:
   - "File Error: READ on [file name] returned RESP [response code], RESP2 [reason code]."

3. **Unexpected Scenarios**:
   - "Unexpected data scenario."

### Debugging and Development
1. **Plain Text Exit**:
   - Used for debugging purposes to display error messages in plain text.
   - Not intended for production use.

2. **Long Text Exit**:
   - Displays detailed error messages for debugging.
   - Should not be used in regular operations.

### Abend Handling
1. **Abend Routine**:
   - Handles unexpected program termination.
   - Sends an abend message and terminates the program with code `9999`.

### Summary of Key Fields
1. **Account Fields**:
   - `ACCT-ID`, `ACCT-STATUS`, `ACCT-BALANCE`, `ACCT-CREDIT-LIMIT`, `ACCT-OPEN-DATE`, etc.

2. **Customer Fields**:
   - `CUST-ID`, `CUST-NAME`, `CUST-ADDRESS`, `CUST-PHONE`, `CUST-SSN`, etc.

3. **Screen Fields**:
   - `CACTVWAO` for output fields.
   - `CACTVWAI` for input fields.

By following these detailed business rules and logic, the program can be replicated or modernized in any programming language while maintaining its functionality and purpose.
<!-- general-rule-end -->
# COACTVWC.cbl: Account View Request Processing

## Dependencies

The `COACTVWC.cbl` program relies on several dependencies, including files, copybooks, and CICS APIs, to perform its operations. These dependencies are critical for the program's functionality, as they provide the necessary data structures, external data sources, and system-level operations. Below is a detailed breakdown of the dependencies, their descriptions, types, references, and their relevance for modernization.

| Dependency | Description | Type | Reference | Relevance for Modernization |
|-------------|-----------|------|------------|------------------------------|
| `CXACAIX` | Cross-reference file used to map account IDs to customer IDs and card numbers. Accessed via an alternate index on the account ID. | File | `CXACAIX` | Essential for retrieving customer and card details based on account ID. Modernization may involve migrating this file to a relational database or API. |
| `ACCTDAT` | Account master file containing account details such as status, balance, credit limits, and dates. | File | `ACCTDAT` | Critical for retrieving account-specific information. Modernization may involve converting this file to a database table or service. |
| `CUSTDAT` | Customer master file containing customer details such as name, address, phone numbers, and government-issued ID. | File | `CUSTDAT` | Vital for retrieving customer-specific information. Modernization may involve transitioning this file to a database or API. |
| `COACTVW` | BMS (Basic Mapping Support) mapset used for screen interaction. Defines the layout and attributes of the screen displayed to the user. | Mapset | `COACTVW` | Required for user interface rendering. Modernization may involve replacing this with a modern UI framework or web-based interface. |
| `DFHBMSCA` | IBM-supplied copybook for BMS control area. Provides structures for managing screen interactions in CICS. | Copybook | `DFHBMSCA` | Necessary for handling screen input and output. Modernization may involve replacing this with a modern UI handling mechanism. |
| `DFHAID` | IBM-supplied copybook for attention identifier (AID) keys. Used to identify user actions such as pressing PF keys. | Copybook | `DFHAID` | Essential for detecting user actions. Modernization may involve mapping these actions to modern UI events. |
| `CSDAT01Y` | Copybook for handling current date and time. Provides structures for date and time manipulation. | Copybook | `CSDAT01Y` | Important for displaying and processing date and time information. Modernization may involve using standard date/time libraries in modern languages. |
| `CSMSG01Y` | Copybook for common messages. Contains predefined messages used across the application. | Copybook | `CSMSG01Y` | Useful for standardizing error and informational messages. Modernization may involve centralizing messages in a configuration file or database. |
| `CSMSG02Y` | Copybook for abend (abnormal end) variables. Used for handling unexpected program terminations. | Copybook | `CSMSG02Y` | Important for error handling and debugging. Modernization may involve implementing structured exception handling. |
| `CSUSR01Y` | Copybook for signed-on user data. Provides structures for managing user session information. | Copybook | `CSUSR01Y` | Necessary for user authentication and session management. Modernization may involve integrating with modern authentication systems. |
| `CVACT01Y` | Copybook for account record layout. Defines the structure of account data in the master file. | Copybook | `CVACT01Y` | Essential for reading and writing account data. Modernization may involve mapping this structure to a database schema. |
| `CVACT02Y` | Copybook for customer record layout. Defines the structure of customer data in the master file. | Copybook | `CVACT02Y` | Critical for reading and writing customer data. Modernization may involve mapping this structure to a database schema. |
| `CVACT03Y` | Copybook for card cross-reference layout. Defines the structure of cross-reference data. | Copybook | `CVACT03Y` | Important for linking accounts, customers, and cards. Modernization may involve transitioning this to a relational database or API. |
| `CVCUS01Y` | Copybook for customer layout. Provides additional structures for customer data. | Copybook | `CVCUS01Y` | Necessary for handling detailed customer information. Modernization may involve consolidating this data into a unified customer profile. |
| `CICS APIs` | CICS commands such as `READ`, `SEND`, `RECEIVE`, and `RETURN` are used for file access, screen interaction, and transaction management. | API | IBM CICS | Core to the program's operation. Modernization may involve replacing these APIs with RESTful services or other middleware. |
| `COACTVW` | BMS copybook for the screen layout. Defines the input and output fields for the user interface. | Copybook | `COACTVW` | Required for screen rendering. Modernization may involve transitioning to a web-based or graphical user interface. |
| `COCOM01Y` | Application commarea copybook. Used for passing data between programs. | Copybook | `COCOM01Y` | Essential for inter-program communication. Modernization may involve replacing this with modern inter-process communication mechanisms. |
| `CSSTRPFY` | Common code for storing PF keys. Handles mapping and validation of PF keys. | Copybook | `CSSTRPFY` | Important for managing user input actions. Modernization may involve mapping these actions to modern UI events. |

### General Explanation
The dependencies listed above are integral to the program's functionality. They include data files for storing account and customer information, copybooks for defining data structures and common logic, and CICS APIs for transaction and screen management. During modernization, these dependencies may need to be replaced or restructured to align with modern technologies such as relational databases, RESTful APIs, and web-based user interfaces. Understanding these dependencies is crucial for ensuring a smooth transition to a modernized system.
# COACTVWC.cbl: Account View Request Processing

## Detailed Rules

<!-- rule-start -->
### 1. Rule / Function / Method - Input Validation
- **Detailed Description**:  
  This rule validates the user-provided account number and ensures it meets specific criteria. The account number must be a non-zero, 11-digit numeric value. If the input is invalid, the program sets error flags and displays appropriate error messages to the user.

- **What it Proposes to Do**:  
  Ensure that the account number provided by the user is valid and ready for further processing. Invalid inputs are flagged, and the user is prompted to correct them.

- **Rule Status**:  
  Relevant for Modernization

- **Algorithm**:
  1. **Initialize Input Flags**:
     - Set `INPUT-OK` to `TRUE` to assume valid input initially.
     - Set `FLG-ACCTFILTER-ISVALID` to `TRUE` to assume the account filter is valid.

  2. **Replace Invalid Characters**:
     - If the account number (`ACCTSIDI`) is equal to `'*'` or contains spaces, replace it with `LOW-VALUES` (binary zeros).

  3. **Validate Account Number**:
     - **Step 1**: Check if the account number is missing:
       - If `CC-ACCT-ID` is equal to `LOW-VALUES` or spaces:
         - Set `INPUT-ERROR` to `TRUE`.
         - Set `FLG-ACCTFILTER-BLANK` to `TRUE`.
         - Display the message: "Account number not provided."
         - Set the account number to `ZEROES` for further processing.
         - Exit the validation process.
     - **Step 2**: Check if the account number is numeric:
       - If `CC-ACCT-ID` is not numeric or equal to `ZEROES`:
         - Set `INPUT-ERROR` to `TRUE`.
         - Set `FLG-ACCTFILTER-NOT-OK` to `TRUE`.
         - Display the message: "Account number must be a non-zero 11-digit number."
         - Set the account number to `ZEROES` for further processing.
         - Exit the validation process.
     - **Step 3**: Check the length of the account number:
       - Ensure the account number is exactly 11 digits long.
       - If not, set `INPUT-ERROR` to `TRUE` and display the appropriate error message.

  4. **Mark Valid Input**:
     - If all checks pass, move the account number to `CDEMO-ACCT-ID`.
     - Set `FLG-ACCTFILTER-ISVALID` to `TRUE`.

  5. **Error Handling**:
     - If `INPUT-ERROR` is set, display the error message stored in `WS-RETURN-MSG` and prompt the user to re-enter the account number.

<!-- rule-end -->

<!-- rule-start -->
### 2. Rule / Function / Method - Data Retrieval from Cross-Reference File
- **Detailed Description**:  
  This rule retrieves customer ID and card number from the cross-reference file (`CXACAIX`) using the account ID as the key. If the account is not found, an error message is constructed.

- **What it Proposes to Do**:  
  Link the account ID to the corresponding customer ID and card number for further processing.

- **Rule Status**:  
  Relevant for Modernization

- **Algorithm**:
  1. **Prepare for File Access**:
     - Move the account ID (`CDEMO-ACCT-ID`) to `WS-CARD-RID-ACCT-ID`.

  2. **Read Cross-Reference File**:
     - Use the `READ` command to access the file `CXACAIX` with the account ID as the key.
     - Specify the key length and the record length for the operation.

  3. **Evaluate Response Code**:
     - **Step 1**: If the response code is `NORMAL`:
       - Move the retrieved customer ID (`XREF-CUST-ID`) to `CDEMO-CUST-ID`.
       - Move the retrieved card number (`XREF-CARD-NUM`) to `CDEMO-CARD-NUM`.
     - **Step 2**: If the response code is `NOTFND`:
       - Set `INPUT-ERROR` to `TRUE`.
       - Set `FLG-ACCTFILTER-NOT-OK` to `TRUE`.
       - Construct an error message: "Account [account ID] not found in Cross-Reference file."
     - **Step 3**: If the response code is `OTHER`:
       - Set `INPUT-ERROR` to `TRUE`.
       - Set `FLG-ACCTFILTER-NOT-OK` to `TRUE`.
       - Construct a detailed error message using the operation name (`READ`), file name (`CXACAIX`), and response codes.

  4. **Exit Process**:
     - If an error occurs, exit the retrieval process and display the error message.

<!-- rule-end -->

<!-- rule-start -->
### 3. Rule / Function / Method - Data Retrieval from Account Master File
- **Detailed Description**:  
  This rule retrieves account details such as status, balance, credit limits, and dates from the account master file (`ACCTDAT`) using the account ID as the key.

- **What it Proposes to Do**:  
  Provide detailed account information for display and further processing.

- **Rule Status**:  
  Relevant for Modernization

- **Algorithm**:
  1. **Prepare for File Access**:
     - Move the account ID (`CDEMO-ACCT-ID`) to `WS-CARD-RID-ACCT-ID`.

  2. **Read Account Master File**:
     - Use the `READ` command to access the file `ACCTDAT` with the account ID as the key.
     - Specify the key length and the record length for the operation.

  3. **Evaluate Response Code**:
     - **Step 1**: If the response code is `NORMAL`:
       - Set `FOUND-ACCT-IN-MASTER` to `TRUE`.
       - Retrieve and store account details:
         - Account status (`ACCT-ACTIVE-STATUS`).
         - Current balance (`ACCT-CURR-BAL`).
         - Credit limits (`ACCT-CREDIT-LIMIT`, `ACCT-CASH-CREDIT-LIMIT`).
         - Cycle credit and debit amounts (`ACCT-CURR-CYC-CREDIT`, `ACCT-CURR-CYC-DEBIT`).
         - Open date (`ACCT-OPEN-DATE`), expiration date (`ACCT-EXPIRAION-DATE`), and reissue date (`ACCT-REISSUE-DATE`).
         - Group ID (`ACCT-GROUP-ID`).
     - **Step 2**: If the response code is `NOTFND`:
       - Set `INPUT-ERROR` to `TRUE`.
       - Set `FLG-ACCTFILTER-NOT-OK` to `TRUE`.
       - Construct an error message: "Account [account ID] not found in Account Master file."
     - **Step 3**: If the response code is `OTHER`:
       - Set `INPUT-ERROR` to `TRUE`.
       - Set `FLG-ACCTFILTER-NOT-OK` to `TRUE`.
       - Construct a detailed error message using the operation name (`READ`), file name (`ACCTDAT`), and response codes.

  4. **Exit Process**:
     - If an error occurs, exit the retrieval process and display the error message.

<!-- rule-end -->

<!-- rule-start -->
### 4. Rule / Function / Method - Data Retrieval from Customer Master File
- **Detailed Description**:  
  This rule retrieves customer details such as name, address, phone numbers, and government-issued ID from the customer master file (`CUSTDAT`) using the customer ID as the key.

- **What it Proposes to Do**:  
  Provide detailed customer information for display and further processing.

- **Rule Status**:  
  Relevant for Modernization

- **Algorithm**:
  1. **Prepare for File Access**:
     - Move the customer ID (`CDEMO-CUST-ID`) to `WS-CARD-RID-CUST-ID`.

  2. **Read Customer Master File**:
     - Use the `READ` command to access the file `CUSTDAT` with the customer ID as the key.
     - Specify the key length and the record length for the operation.

  3. **Evaluate Response Code**:
     - **Step 1**: If the response code is `NORMAL`:
       - Set `FOUND-CUST-IN-MASTER` to `TRUE`.
       - Retrieve and store customer details:
         - Customer ID (`CUST-ID`).
         - SSN (`CUST-SSN`) formatted as `XXX-XX-XXXX`.
         - FICO credit score (`CUST-FICO-CREDIT-SCORE`).
         - Date of birth (`CUST-DOB-YYYY-MM-DD`).
         - Name (`CUST-FIRST-NAME`, `CUST-MIDDLE-NAME`, `CUST-LAST-NAME`).
         - Address (`CUST-ADDR-LINE-1`, `CUST-ADDR-LINE-2`, `CUST-ADDR-LINE-3`, `CUST-ADDR-STATE-CD`, `CUST-ADDR-ZIP`, `CUST-ADDR-COUNTRY-CD`).
         - Phone numbers (`CUST-PHONE-NUM-1`, `CUST-PHONE-NUM-2`).
         - Government-issued ID (`CUST-GOVT-ISSUED-ID`).
         - EFT account ID (`CUST-EFT-ACCOUNT-ID`).
         - Primary cardholder indicator (`CUST-PRI-CARD-HOLDER-IND`).
     - **Step 2**: If the response code is `NOTFND`:
       - Set `INPUT-ERROR` to `TRUE`.
       - Set `FLG-CUSTFILTER-NOT-OK` to `TRUE`.
       - Construct an error message: "Customer [customer ID] not found in Customer Master file."
     - **Step 3**: If the response code is `OTHER`:
       - Set `INPUT-ERROR` to `TRUE`.
       - Set `FLG-CUSTFILTER-NOT-OK` to `TRUE`.
       - Construct a detailed error message using the operation name (`READ`), file name (`CUSTDAT`), and response codes.

  4. **Exit Process**:
     - If an error occurs, exit the retrieval process and display the error message.

<!-- rule-end -->
# COACTVWC.cbl: Account View Request Processing

## Data Structure

The data structure of the application is defined using COBOL data definitions and copybooks. These structures are used to store and manipulate data related to accounts, customers, and cross-references. Below is a detailed breakdown of the fields, their attributes, and their relevance for modernization.

| Field ID | Field Name | Data Type | Field Size | Description | Mandatory | Default Value | Additional Notes | Relevant for Modernization | Information Type |
|-------------|---------------|---------------|------------------|-----------|-------------|--------------|------------------|-----------------------------|--------------------|
| `WS-CARD-RID-CARDNUM` | Card Number | Alphanumeric | 16 | Unique identifier for a card associated with an account. | Yes | Spaces | Used for linking accounts to cards. | Yes | Account/Card Data |
| `WS-CARD-RID-CUST-ID` | Customer ID | Numeric | 9 | Unique identifier for a customer. | Yes | Zeros | Links accounts to customers. | Yes | Customer Data |
| `WS-CARD-RID-CUST-ID-X` | Customer ID (Redefinition) | Alphanumeric | 9 | Alternate representation of the customer ID. | Yes | Spaces | Used for file access operations. | Yes | Customer Data |
| `WS-CARD-RID-ACCT-ID` | Account ID | Numeric | 11 | Unique identifier for an account. | Yes | Zeros | Primary key for account-related operations. | Yes | Account Data |
| `WS-CARD-RID-ACCT-ID-X` | Account ID (Redefinition) | Alphanumeric | 11 | Alternate representation of the account ID. | Yes | Spaces | Used for file access operations. | Yes | Account Data |
| `ACCT-ACTIVE-STATUS` | Account Status | Alphanumeric | 1 | Indicates whether the account is active or inactive. | Yes | Spaces | Used for account validation and display. | Yes | Account Data |
| `ACCT-CURR-BAL` | Current Balance | Numeric | 9(9)V99 | Current balance of the account. | Yes | Zeros | Displayed to the user. | Yes | Financial Data |
| `ACCT-CREDIT-LIMIT` | Credit Limit | Numeric | 9(9)V99 | Maximum credit limit for the account. | Yes | Zeros | Used for financial calculations. | Yes | Financial Data |
| `ACCT-CASH-CREDIT-LIMIT` | Cash Credit Limit | Numeric | 9(9)V99 | Maximum cash credit limit for the account. | Yes | Zeros | Used for financial calculations. | Yes | Financial Data |
| `ACCT-CURR-CYC-CREDIT` | Current Cycle Credit | Numeric | 9(9)V99 | Credit amount for the current cycle. | Yes | Zeros | Used for cycle-based calculations. | Yes | Financial Data |
| `ACCT-CURR-CYC-DEBIT` | Current Cycle Debit | Numeric | 9(9)V99 | Debit amount for the current cycle. | Yes | Zeros | Used for cycle-based calculations. | Yes | Financial Data |
| `ACCT-OPEN-DATE` | Open Date | Date | 8 | Date when the account was opened. | Yes | Spaces | Displayed to the user. | Yes | Account Data |
| `ACCT-EXPIRAION-DATE` | Expiration Date | Date | 8 | Date when the account expires. | Yes | Spaces | Displayed to the user. | Yes | Account Data |
| `ACCT-REISSUE-DATE` | Reissue Date | Date | 8 | Date when the account was reissued. | Yes | Spaces | Displayed to the user. | Yes | Account Data |
| `ACCT-GROUP-ID` | Group ID | Alphanumeric | 10 | Identifier for the account group. | No | Spaces | Used for grouping accounts. | Yes | Account Data |
| `CUST-ID` | Customer ID | Numeric | 9 | Unique identifier for a customer. | Yes | Zeros | Links accounts to customers. | Yes | Customer Data |
| `CUST-SSN` | Social Security Number | Numeric | 9 | Customer's SSN formatted as `XXX-XX-XXXX`. | Yes | Zeros | Displayed to the user. | Yes | Customer Data |
| `CUST-FICO-CREDIT-SCORE` | FICO Credit Score | Numeric | 3 | Customer's FICO credit score. | Yes | Zeros | Used for credit evaluation. | Yes | Financial Data |
| `CUST-DOB-YYYY-MM-DD` | Date of Birth | Date | 10 | Customer's date of birth. | Yes | Spaces | Displayed to the user. | Yes | Customer Data |
| `CUST-FIRST-NAME` | First Name | Alphanumeric | 20 | Customer's first name. | Yes | Spaces | Displayed to the user. | Yes | Customer Data |
| `CUST-MIDDLE-NAME` | Middle Name | Alphanumeric | 20 | Customer's middle name. | No | Spaces | Displayed to the user. | Yes | Customer Data |
| `CUST-LAST-NAME` | Last Name | Alphanumeric | 20 | Customer's last name. | Yes | Spaces | Displayed to the user. | Yes | Customer Data |
| `CUST-ADDR-LINE-1` | Address Line 1 | Alphanumeric | 50 | First line of the customer's address. | Yes | Spaces | Displayed to the user. | Yes | Customer Data |
| `CUST-ADDR-LINE-2` | Address Line 2 | Alphanumeric | 50 | Second line of the customer's address. | No | Spaces | Displayed to the user. | Yes | Customer Data |
| `CUST-ADDR-LINE-3` | City | Alphanumeric | 30 | Customer's city. | Yes | Spaces | Displayed to the user. | Yes | Customer Data |
| `CUST-ADDR-STATE-CD` | State Code | Alphanumeric | 2 | Customer's state code. | Yes | Spaces | Displayed to the user. | Yes | Customer Data |
| `CUST-ADDR-ZIP` | ZIP Code | Numeric | 5 | Customer's ZIP code. | Yes | Zeros | Displayed to the user. | Yes | Customer Data |
| `CUST-ADDR-COUNTRY-CD` | Country Code | Alphanumeric | 3 | Customer's country code. | Yes | Spaces | Displayed to the user. | Yes | Customer Data |
| `CUST-PHONE-NUM-1` | Phone Number 1 | Numeric | 10 | Customer's primary phone number. | Yes | Zeros | Displayed to the user. | Yes | Customer Data |
| `CUST-PHONE-NUM-2` | Phone Number 2 | Numeric | 10 | Customer's secondary phone number. | No | Zeros | Displayed to the user. | Yes | Customer Data |
| `CUST-GOVT-ISSUED-ID` | Government-Issued ID | Alphanumeric | 20 | Customer's government-issued ID. | Yes | Spaces | Displayed to the user. | Yes | Customer Data |
| `CUST-EFT-ACCOUNT-ID` | EFT Account ID | Alphanumeric | 20 | Customer's electronic funds transfer account ID. | No | Spaces | Used for financial transactions. | Yes | Financial Data |
| `CUST-PRI-CARD-HOLDER-IND` | Primary Cardholder Indicator | Alphanumeric | 1 | Indicates if the customer is the primary cardholder. | Yes | Spaces | Used for account validation. | Yes | Customer Data |

### Notes:
- **Mandatory Fields**: Fields marked as mandatory must be provided for the program to function correctly.
- **Default Values**: Default values are used when no input is provided or when the field is optional.
- **Relevance for Modernization**: Fields relevant for modernization may need to be mapped to database schemas, APIs, or modern data structures.
- **Information Type**: Categorizes fields into account data, customer data, or financial data for better organization during modernization.
# COACTVWC.cbl: Account View Request Processing

## Items Relevant for Modernization

In the context of modernization, the following functions and methods are identified as most relevant. These items are critical for transitioning the application to a modern architecture, such as using relational databases, RESTful APIs, or web-based interfaces. Each item is listed with its relevance and potential modernization approach.

### 1. **Input Validation**
- **Relevance**: High  
  Input validation ensures data integrity and prevents invalid data from entering the system. Modernization can involve replacing COBOL validation logic with centralized validation mechanisms in a modern programming language or framework.
- **Modernization Approach**:
  - Implement validation rules using modern libraries or frameworks (e.g., JavaScript, Python, or Java).
  - Use regular expressions for numeric and length checks.
  - Centralize validation logic in reusable components or services.

### 2. **Data Retrieval from Cross-Reference File**
- **Relevance**: High  
  The cross-reference file (`CXACAIX`) links account IDs to customer IDs and card numbers. Modernization can involve transitioning this file to a relational database or API for better scalability and accessibility.
- **Modernization Approach**:
  - Replace file-based access with database queries (e.g., SQL).
  - Use RESTful APIs to retrieve cross-reference data.
  - Implement caching mechanisms for frequently accessed data.

### 3. **Data Retrieval from Account Master File**
- **Relevance**: High  
  The account master file (`ACCTDAT`) contains critical account details such as balance, credit limits, and dates. Modernization can involve migrating this file to a database or service-oriented architecture.
- **Modernization Approach**:
  - Map account data to a relational database schema.
  - Use APIs to retrieve account details dynamically.
  - Implement data encryption for sensitive financial information.

### 4. **Data Retrieval from Customer Master File**
- **Relevance**: High  
  The customer master file (`CUSTDAT`) stores customer details such as name, address, and SSN. Modernization can involve transitioning this file to a database or integrating with customer management systems.
- **Modernization Approach**:
  - Replace file-based access with database queries.
  - Use APIs to retrieve customer data.
  - Integrate with CRM (Customer Relationship Management) systems for enhanced functionality.

### 5. **Screen Interaction**
- **Relevance**: High  
  The program uses BMS maps (`COACTVW`) for screen interaction. Modernization can involve replacing these maps with web-based or graphical user interfaces.
- **Modernization Approach**:
  - Develop web-based interfaces using HTML, CSS, and JavaScript.
  - Use modern UI frameworks such as React, Angular, or Vue.js.
  - Implement responsive design for better user experience across devices.

### 6. **Error Handling**
- **Relevance**: High  
  The program constructs detailed error messages for file access and validation errors. Modernization can involve implementing structured exception handling and logging mechanisms.
- **Modernization Approach**:
  - Use try-catch blocks for error handling in modern languages.
  - Implement centralized logging systems (e.g., Log4j, ELK Stack).
  - Provide user-friendly error messages in the UI.

### 7. **Transaction Management**
- **Relevance**: High  
  The program uses CICS commands (`READ`, `SEND`, `RECEIVE`, `RETURN`) for transaction management. Modernization can involve replacing these commands with RESTful APIs or middleware solutions.
- **Modernization Approach**:
  - Use RESTful APIs for transaction management.
  - Implement middleware solutions for data processing and communication.
  - Ensure transactional integrity using modern database systems.

### 8. **Data Mapping**
- **Relevance**: High  
  The program maps data from files to screen fields (`CACTVWAO`). Modernization can involve automating this mapping using modern data-binding techniques.
- **Modernization Approach**:
  - Use data-binding frameworks (e.g., Angular, Vue.js).
  - Implement dynamic data mapping using JSON or XML.
  - Centralize mapping logic in reusable components.

### 9. **Abend Handling**
- **Relevance**: Medium  
  The program handles unexpected terminations using abend routines. Modernization can involve implementing structured exception handling and recovery mechanisms.
- **Modernization Approach**:
  - Use modern exception handling techniques (e.g., try-catch-finally).
  - Implement automated recovery processes.
  - Log abend details for debugging and analysis.

### 10. **Date and Time Handling**
- **Relevance**: Medium  
  The program uses copybooks (`CSDAT01Y`) for date and time manipulation. Modernization can involve using standard date/time libraries in modern languages.
- **Modernization Approach**:
  - Use built-in date/time libraries (e.g., Java's `LocalDateTime`, Python's `datetime`).
  - Implement timezone handling for global applications.
  - Format dates and times dynamically for better user experience.

### 11. **PF Key Management**
- **Relevance**: Medium  
  The program maps PF keys to user actions. Modernization can involve replacing PF key logic with modern UI event handling.
- **Modernization Approach**:
  - Map PF keys to button clicks or other UI events.
  - Use event-driven programming for user actions.
  - Implement keyboard shortcuts for advanced users.

### 12. **Data Encryption**
- **Relevance**: High  
  Sensitive data such as SSN, account numbers, and balances are stored and processed. Modernization can involve implementing encryption mechanisms for data security.
- **Modernization Approach**:
  - Use encryption libraries (e.g., AES, RSA).
  - Implement secure data storage solutions.
  - Ensure compliance with data protection regulations (e.g., GDPR, CCPA).

### Summary
The items listed above are critical for modernizing the `COACTVWC.cbl` program. They ensure the application is scalable, secure, and user-friendly while leveraging modern technologies and architectures. Each item should be carefully analyzed and implemented during the modernization process.
# COACTVWC.cbl: Account View Request Processing

## Items Irrelevant for Modernization

In the context of modernization, certain functions and methods in the `COACTVWC.cbl` program are deemed irrelevant. These items either serve legacy purposes, are redundant in modern architectures, or can be replaced by more efficient mechanisms. Below is a list of such items, along with explanations for why they are irrelevant and can be ignored during modernization.

### 1. **Plain Text Exit (`SEND-PLAIN-TEXT`)**
- **Reason for Irrelevance**:  
  This function is primarily used for debugging purposes and displays error messages in plain text. It is not intended for production use and does not contribute to the core functionality of the program.
- **Why It Can Be Ignored**:  
  Modern debugging tools and logging frameworks provide more robust and flexible mechanisms for error tracking and debugging.
- **Replacement in Modernization**:  
  Use centralized logging systems or debugging tools such as ELK Stack, Log4j, or Splunk.

---

### 2. **Long Text Exit (`SEND-LONG-TEXT`)**
- **Reason for Irrelevance**:  
  Similar to `SEND-PLAIN-TEXT`, this function is used for debugging and displays detailed error messages. It is not part of the regular application workflow.
- **Why It Can Be Ignored**:  
  Modern systems use structured logging and monitoring tools for error analysis, making this function redundant.
- **Replacement in Modernization**:  
  Implement structured logging and monitoring systems for error tracking.

---

### 3. **Abend Routine (`ABEND-ROUTINE`)**
- **Reason for Irrelevance**:  
  This routine handles unexpected program terminations using legacy CICS commands. Modern systems use structured exception handling and recovery mechanisms, which are more efficient and flexible.
- **Why It Can Be Ignored**:  
  Modern programming languages and frameworks provide built-in exception handling mechanisms, eliminating the need for custom abend routines.
- **Replacement in Modernization**:  
  Use try-catch-finally blocks or equivalent exception handling constructs in modern languages.

---

### 4. **PF Key Mapping (`CSSTRPFY`)**
- **Reason for Irrelevance**:  
  This function maps PF keys to user actions, which is specific to legacy terminal-based interfaces. Modern systems use event-driven programming and graphical user interfaces, making PF key mapping obsolete.
- **Why It Can Be Ignored**:  
  Modern UI frameworks handle user actions through event listeners and callbacks, eliminating the need for PF key mapping.
- **Replacement in Modernization**:  
  Replace PF key logic with modern UI event handling mechanisms, such as button clicks or keyboard shortcuts.

---

### 5. **Low-Values and Spaces Handling**
- **Reason for Irrelevance**:  
  The program uses `LOW-VALUES` and `SPACES` to represent missing or invalid data. Modern systems use `null`, `undefined`, or empty strings for similar purposes, which are more intuitive and standardized.
- **Why It Can Be Ignored**:  
  Modern programming languages and databases provide built-in support for handling null or empty values, making this approach redundant.
- **Replacement in Modernization**:  
  Use `null` or empty strings in modern languages and frameworks.

---

### 6. **Hardcoded Literals**
- **Reason for Irrelevance**:  
  The program uses hardcoded literals for program names, transaction IDs, and file names. These literals are inflexible and difficult to maintain in modern systems.
- **Why It Can Be Ignored**:  
  Modern systems use configuration files or environment variables to manage such values dynamically.
- **Replacement in Modernization**:  
  Replace hardcoded literals with dynamic configuration management using tools like YAML, JSON, or environment variables.

---

### 7. **CICS-Specific Commands**
- **Reason for Irrelevance**:  
  Commands such as `EXEC CICS HANDLE ABEND`, `EXEC CICS RETURN`, and `EXEC CICS SEND` are specific to the CICS environment. Modern systems use APIs, middleware, or direct database connections, making these commands obsolete.
- **Why It Can Be Ignored**:  
  Modern architectures do not rely on CICS-specific commands and instead use RESTful APIs or other middleware solutions.
- **Replacement in Modernization**:  
  Replace CICS commands with API calls or database queries.

---

### 8. **Legacy Screen Attributes**
- **Reason for Irrelevance**:  
  The program uses screen attributes such as `DFHBMFSE` and `DFHDFCOL` for field protection and color settings. These attributes are specific to legacy terminal-based interfaces and are irrelevant in modern graphical or web-based interfaces.
- **Why It Can Be Ignored**:  
  Modern UI frameworks provide advanced styling and protection mechanisms, making these attributes redundant.
- **Replacement in Modernization**:  
  Use CSS or modern UI frameworks for styling and field protection.

---

### 9. **Debugging Messages (`CODING-TO-BE-DONE`)**
- **Reason for Irrelevance**:  
  Debugging messages such as "Looks Good... so far" are placeholders and do not contribute to the program's functionality.
- **Why It Can Be Ignored**:  
  Modern debugging tools and practices eliminate the need for such hardcoded messages.
- **Replacement in Modernization**:  
  Use structured debugging tools and logs for tracking program progress.

---

### 10. **Unused Fields**
- **Reason for Irrelevance**:  
  Certain fields in the program, such as `WS-LONG-MSG` and `WS-INFO-MSG`, are used for debugging or informational purposes and do not contribute to the core functionality.
- **Why It Can Be Ignored**:  
  These fields are redundant in modern systems, which use centralized logging and monitoring tools.
- **Replacement in Modernization**:  
  Remove unused fields and replace them with structured logging mechanisms.

---

### Summary
The items listed above are irrelevant for modernization as they are either legacy-specific, redundant, or better handled by modern tools and frameworks. Ignoring these items during modernization will streamline the process and focus efforts on critical functionalities that enhance scalability, security, and user experience.
