# COACTUPC.cbl: Account Update Processing Program

## Overview
The `COACTUPC.cbl` program is a COBOL-based application designed to handle account updates in a business system. It is part of the business logic layer and interacts with customer and account master files to retrieve, validate, and update account and customer data. The program also manages user interactions through a CICS (Customer Information Control System) interface, allowing users to view, edit, and confirm changes to account and customer details.

The program's primary objectives are:
- To retrieve account and customer data based on user input.
- To validate the input data for correctness and completeness.
- To allow users to make changes to account and customer details.
- To update the master files with the validated changes.
- To handle errors and provide feedback to the user.

The program contributes to the overall functionality of the system by ensuring accurate and consistent updates to account and customer data, which are critical for maintaining the integrity of the business processes.

<!-- general-rule-start -->
## Business Rules:
The program implements several business rules and processes to manage account updates effectively. Below is a detailed explanation of the key functionalities and rules:

### 1. **Initialization and Context Setup**
   - **Purpose**: Prepare the program for execution by initializing variables and setting up the context.
   - **Details**:
     - The program initializes working storage variables, including flags and data structures.
     - It checks if data is passed via the CICS communication area (`DFHCOMMAREA`). If no data is passed, it initializes the program's context and sets the `ACUP-DETAILS-NOT-FETCHED` flag to indicate that no account details have been retrieved yet.

### 2. **User Input Handling**
   - **Purpose**: Receive and validate user input from the CICS interface.
   - **Details**:
     - The program receives user input using the `EXEC CICS RECEIVE` command.
     - It validates the input fields for correctness:
       - **Account ID**: Must be an 11-digit non-zero numeric value.
       - **Active Status**: Must be either 'Y' (Yes) or 'N' (No).
       - **Credit Limit, Cash Credit Limit, Current Balance, Current Cycle Credit/Debit**: Must be valid signed numeric values with two decimal places.
       - **Dates (Open, Expiry, Reissue)**: Must follow the `CCYY-MM-DD` format.
       - **Social Security Number (SSN)**: Must follow the `XXX-XX-XXXX` format with specific rules for each part.
       - **FICO Score**: Must be a numeric value between 300 and 850.
       - **Names and Address Fields**: Must contain only valid alphanumeric characters.
       - **Phone Numbers**: Must follow the North American format `(XXX)XXX-XXXX`.
       - **State and ZIP Code**: Must be valid combinations based on USPS data.

### 3. **Data Retrieval**
   - **Purpose**: Fetch account and customer data from the master files based on the provided Account ID.
   - **Details**:
     - The program reads the `CARD-XREF` file using the Account ID to retrieve the associated Customer ID and Card Number.
     - It then reads the `ACCOUNT` and `CUSTOMER` master files using the retrieved IDs to fetch the account and customer details.
     - If any data is not found, appropriate error messages are displayed to the user.

### 4. **Data Comparison**
   - **Purpose**: Compare the old and new data to detect changes.
   - **Details**:
     - The program compares each field of the old and new data structures (`ACUP-OLD-DETAILS` and `ACUP-NEW-DETAILS`).
     - If no changes are detected, the program informs the user and does not proceed with the update.

### 5. **Data Validation**
   - **Purpose**: Ensure that the new data entered by the user is valid and meets business rules.
   - **Details**:
     - The program performs field-level validations, such as checking numeric values, date formats, and character restrictions.
     - Cross-field validations are also performed, such as ensuring that the State and ZIP Code combination is valid.

### 6. **Data Update**
   - **Purpose**: Update the master files with the validated changes.
   - **Details**:
     - The program locks the account and customer records for update using the `EXEC CICS READ` command with the `UPDATE` option.
     - It prepares the update records by populating the `ACCT-UPDATE-RECORD` and `CUST-UPDATE-RECORD` structures with the new data.
     - It writes the updated records back to the master files using the `EXEC CICS REWRITE` command.
     - If the update fails, the program rolls back the changes using the `EXEC CICS SYNCPOINT ROLLBACK` command.

### 7. **Error Handling**
   - **Purpose**: Handle errors gracefully and provide feedback to the user.
   - **Details**:
     - The program sets appropriate error flags and messages based on the type of error encountered (e.g., invalid input, record not found, update failure).
     - It displays error messages on the CICS interface to inform the user of the issue.

### 8. **User Interaction**
   - **Purpose**: Guide the user through the account update process.
   - **Details**:
     - The program displays prompts and messages to the user based on the current state of the process.
     - It allows the user to confirm changes before committing them to the master files.
     - It provides feedback on the success or failure of the update operation.

### 9. **Screen Management**
   - **Purpose**: Manage the CICS interface for user interaction.
   - **Details**:
     - The program initializes the screen fields and attributes.
     - It sets up the screen variables based on the current context (e.g., initial values, original values, updated values).
     - It sends the screen to the user using the `EXEC CICS SEND` command.

### 10. **Audit and Logging**
   - **Purpose**: Maintain a record of changes for auditing purposes.
   - **Details**:
     - The program stores the old and new data in the communication area for potential logging or auditing.
     - It ensures that any changes made to the master files are consistent and traceable.

### 11. **Abend Handling**
   - **Purpose**: Handle unexpected program termination.
   - **Details**:
     - The program includes an `ABEND-ROUTINE` to handle unexpected errors.
     - It sends an error message to the user and terminates the program gracefully.

### 12. **Constants and Literals**
   - **Purpose**: Define reusable constants and literals for the program.
   - **Details**:
     - The program defines constants for file names, transaction IDs, and error messages.
     - These constants are used throughout the program to ensure consistency and maintainability.
<!-- general-rule-end -->
## Dependencies

The `COACTUPC.cbl` program relies on several dependencies to perform its functions effectively. These dependencies include files, copybooks, datasets, and CICS commands that facilitate data retrieval, validation, and updates. Below is a detailed table summarizing the dependencies, their descriptions, types, references, and relevance for modernization:

| Dependency | Description | Type | Reference | Relevance for Modernization |
|-------------|-----------|------|------------|------------------------------|
| `CARD-XREF` File | A dataset used to retrieve customer and card information based on the account ID. | Dataset | `CARDAIX`, `CXACAIX` | Critical for linking account data to customer and card records. May need migration to a relational database or API-based system. |
| `ACCOUNT` File | The master file containing account details such as balance, credit limits, and dates. | Dataset | `ACCTDAT` | Essential for storing account data. Modernization may involve transitioning to a database or cloud-based storage. |
| `CUSTOMER` File | The master file containing customer details such as name, address, SSN, and contact information. | Dataset | `CUSTDAT` | Key for customer data management. Modernization may involve integration with CRM systems or databases. |
| CICS Commands | Commands for interacting with datasets, handling transactions, and managing user screens. | API | `EXEC CICS` | Integral for COBOL-CICS applications. Modernization may involve replacing CICS with RESTful APIs or microservices. |
| `CSSETATY` Copybook | A reusable copybook for setting screen attributes dynamically. | Copybook | `CSSETATY` | Useful for screen management. Modernization may involve replacing screen-based interactions with web-based UI frameworks. |
| `CSUTLDPY` Copybook | Provides common date utility routines for validating and formatting dates. | Copybook | `CSUTLDPY` | Important for date handling. Modernization may involve using standard date libraries in modern programming languages. |
| `CSSTRPFY` Copybook | Handles PFKey storage and mapping for user interactions. | Copybook | `CSSTRPFY` | Facilitates user navigation. Modernization may involve replacing PFKeys with modern UI elements like buttons or dropdowns. |
| `DFHBMSCA` Copybook | IBM-supplied copybook for screen attributes and management. | Copybook | `DFHBMSCA` | Essential for CICS screen handling. Modernization may involve transitioning to web-based interfaces. |
| `DFHAID` Copybook | IBM-supplied copybook for handling AID (Attention Identifier) keys. | Copybook | `DFHAID` | Important for managing user inputs. Modernization may involve replacing AID keys with event-driven programming. |
| `CSMSG01Y` Copybook | Contains common messages used across the application. | Copybook | `CSMSG01Y` | Useful for error handling and user feedback. Modernization may involve centralizing messages in a configuration file or database. |
| `CSMSG02Y` Copybook | Handles abend (abnormal end) variables and routines. | Copybook | `CSMSG02Y` | Critical for error handling. Modernization may involve implementing structured exception handling in modern languages. |
| `CSUSR01Y` Copybook | Manages signed-on user data for context tracking. | Copybook | `CSUSR01Y` | Important for user session management. Modernization may involve integrating with authentication services like OAuth. |
| `CVACT01Y` Copybook | Defines the layout for account records. | Copybook | `CVACT01Y` | Essential for account data structure. Modernization may involve mapping these fields to database tables. |
| `CVACT03Y` Copybook | Defines the layout for card cross-reference records. | Copybook | `CVACT03Y` | Key for linking account and card data. Modernization may involve transitioning to relational database relationships. |
| `CVCUS01Y` Copybook | Defines the layout for customer records. | Copybook | `CVCUS01Y` | Essential for customer data structure. Modernization may involve mapping these fields to database tables. |
| `COCOM01Y` Copybook | Application commarea copybook for managing shared data between programs. | Copybook | `COCOM01Y` | Important for inter-program communication. Modernization may involve replacing commarea with API-based data exchange. |
| `COACTUP` Copybook | Defines the screen layout for the account update interface. | Copybook | `COACTUP` | Critical for user interaction. Modernization may involve replacing screen layouts with web-based forms. |
| `CSLKPCDY` Copybook | Contains lookup data for North American phone area codes. | Copybook | `CSLKPCDY` | Useful for phone number validation. Modernization may involve integrating with external APIs for phone validation. |
| `CSUTLDWY` Copybook | Provides generic date edit variables for `CCYYMMDD` format. | Copybook | `CSUTLDWY` | Important for date validation. Modernization may involve using standard date libraries. |
| `CSDAT01Y` Copybook | Handles current date and time variables. | Copybook | `CSDAT01Y` | Useful for date/time management. Modernization may involve using system-level date/time APIs. |
| `COMEN01C` Program | Menu program for navigation. | Program | `COMEN01C` | Facilitates user navigation. Modernization may involve replacing menu-based navigation with web-based navigation. |
| `COCRDUPC` Program | Card update program for managing card details. | Program | `COCRDUPC` | Supports card management. Modernization may involve integrating card updates into a unified system. |
| `COCRDLIC` Program | Card listing program for viewing card details. | Program | `COCRDLIC` | Facilitates card data retrieval. Modernization may involve transitioning to a database query system. |
| `COCRDSLC` Program | Card detail program for viewing specific card information. | Program | `COCRDSLC` | Supports detailed card views. Modernization may involve integrating with web-based dashboards. |
| `DFHRESP` Constants | IBM-supplied constants for CICS response codes. | Constants | `DFHRESP` | Essential for error handling. Modernization may involve replacing these constants with structured exception handling. |

### Relevance for Modernization
The dependencies listed above are integral to the program's functionality. Modernization efforts should focus on:
- Migrating datasets to relational databases or cloud-based storage solutions.
- Replacing CICS-based interactions with RESTful APIs or microservices.
- Transitioning screen-based interfaces to web-based UI frameworks.
- Centralizing messages and configurations for easier maintenance.
- Integrating authentication and validation services for enhanced security and functionality.
- Utilizing modern programming libraries for date, time, and data validation.
## Detailed Rules

<!-- rule-start -->
1. ## Rule / Function / Method - Account ID Validation
   - **Detailed Description of the Rule**:  
     This rule validates the Account ID provided by the user. The Account ID must be an 11-digit numeric value that is non-zero. It ensures that the Account ID is correctly formatted and meets the business requirements for identifying accounts in the system.

   - **What it proposes to do**:  
     Validate the Account ID to ensure it is numeric, has exactly 11 digits, and is not zero. If the validation fails, an error message is displayed to the user.

   ## Rule Status:  
   - Relevant for Modernization

   ## Algorithm:
   - **Step 1**: Check if the Account ID is provided.  
     - If the Account ID is empty (contains spaces or low-values), set an error flag and display the message: "Account Number must be supplied."
   - **Step 2**: Check if the Account ID is numeric.  
     - If the Account ID contains non-numeric characters, set an error flag and display the message: "Account Number must be a numeric value."
   - **Step 3**: Check the length of the Account ID.  
     - If the Account ID is not exactly 11 digits long, set an error flag and display the message: "Account Number must be an 11-digit number."
   - **Step 4**: Check if the Account ID is non-zero.  
     - If the Account ID is zero, set an error flag and display the message: "Account Number must not be zero."
   - **Step 5**: If all validations pass, mark the Account ID as valid and proceed with further processing.

   **Mathematical Formula for Validation**:  
   - Length Check: `Length(Account ID) == 11`  
   - Numeric Check: `Account ID IS NUMERIC`  
   - Non-Zero Check: `Account ID != 0`
<!-- rule-end -->

<!-- rule-start -->
2. ## Rule / Function / Method - Credit Limit Validation
   - **Detailed Description of the Rule**:  
     This rule validates the Credit Limit entered by the user. The Credit Limit must be a signed numeric value with up to two decimal places. It ensures that the Credit Limit is correctly formatted and within acceptable ranges.

   - **What it proposes to do**:  
     Validate the Credit Limit to ensure it is a valid signed numeric value and meets the business requirements for account limits.

   ## Rule Status:  
   - Relevant for Modernization

   ## Algorithm:
   - **Step 1**: Check if the Credit Limit is provided.  
     - If the Credit Limit is empty (contains spaces or low-values), set an error flag and display the message: "Credit Limit must be supplied."
   - **Step 2**: Validate the format of the Credit Limit.  
     - Use a function to test if the Credit Limit is a valid signed numeric value with up to two decimal places.  
     - If the validation fails, set an error flag and display the message: "Credit Limit is not valid."
   - **Step 3**: If the Credit Limit is valid, convert it to a numeric value for further processing.  
     - Use a function to convert the string representation of the Credit Limit to a numeric value.
   - **Step 4**: Store the validated Credit Limit for further processing.

   **Mathematical Formula for Validation**:  
   - Format Check: `Credit Limit matches pattern [+/-]999,999,999.99`  
   - Conversion: `Numeric(Credit Limit)`
<!-- rule-end -->

<!-- rule-start -->
3. ## Rule / Function / Method - Date Validation (CCYY-MM-DD Format)
   - **Detailed Description of the Rule**:  
     This rule validates dates entered by the user, such as Open Date, Expiry Date, and Reissue Date. The dates must follow the `CCYY-MM-DD` format and represent valid calendar dates.

   - **What it proposes to do**:  
     Ensure that dates are correctly formatted and valid according to the Gregorian calendar.

   ## Rule Status:  
   - Relevant for Modernization

   ## Algorithm:
   - **Step 1**: Check if the date is provided.  
     - If the date is empty (contains spaces or low-values), set an error flag and display the message: "Date must be supplied."
   - **Step 2**: Split the date into components (Year, Month, Day).  
     - Extract the year (`CCYY`), month (`MM`), and day (`DD`) from the date string.
   - **Step 3**: Validate the year.  
     - Ensure the year is a four-digit numeric value.
   - **Step 4**: Validate the month.  
     - Ensure the month is a numeric value between 1 and 12.
   - **Step 5**: Validate the day.  
     - Ensure the day is a numeric value between 1 and the maximum number of days in the given month and year.  
     - Consider leap years for February:  
       - If the year is divisible by 4 and not divisible by 100, or divisible by 400, February has 29 days. Otherwise, it has 28 days.
   - **Step 6**: If all validations pass, mark the date as valid and proceed with further processing.

   **Mathematical Formula for Leap Year Check**:  
   - Leap Year: `(Year % 4 == 0 AND Year % 100 != 0) OR (Year % 400 == 0)`  
   - Maximum Days in Month:  
     - January, March, May, July, August, October, December: 31 days  
     - April, June, September, November: 30 days  
     - February: 28 or 29 days (depending on leap year)
<!-- rule-end -->

<!-- rule-start -->
4. ## Rule / Function / Method - Social Security Number (SSN) Validation
   - **Detailed Description of the Rule**:  
     This rule validates the Social Security Number (SSN) entered by the user. The SSN must follow the format `XXX-XX-XXXX` and adhere to specific rules for each part.

   - **What it proposes to do**:  
     Ensure that the SSN is correctly formatted and valid according to the rules defined by the Social Security Administration.

   ## Rule Status:  
   - Relevant for Modernization

   ## Algorithm:
   - **Step 1**: Split the SSN into three parts:  
     - Part 1 (`XXX`): The first three digits.  
     - Part 2 (`XX`): The next two digits.  
     - Part 3 (`XXXX`): The last four digits.
   - **Step 2**: Validate Part 1:  
     - Ensure it is a three-digit numeric value.  
     - Ensure it is not `000`, `666`, or between `900` and `999`.
   - **Step 3**: Validate Part 2:  
     - Ensure it is a two-digit numeric value.  
     - Ensure it is between `01` and `99`.
   - **Step 4**: Validate Part 3:  
     - Ensure it is a four-digit numeric value.  
     - Ensure it is between `0001` and `9999`.
   - **Step 5**: If all validations pass, mark the SSN as valid and proceed with further processing.

   **Mathematical Formula for Validation**:  
   - Part 1: `SSN-Part1 IS NUMERIC AND NOT IN {000, 666, 900-999}`  
   - Part 2: `SSN-Part2 IS NUMERIC AND BETWEEN 01 AND 99`  
   - Part 3: `SSN-Part3 IS NUMERIC AND BETWEEN 0001 AND 9999`
<!-- rule-end -->

<!-- rule-start -->
5. ## Rule / Function / Method - Phone Number Validation
   - **Detailed Description of the Rule**:  
     This rule validates phone numbers entered by the user. The phone numbers must follow the North American format `(XXX)XXX-XXXX` and adhere to specific rules for area codes, prefixes, and line numbers.

   - **What it proposes to do**:  
     Ensure that phone numbers are correctly formatted and valid according to North American standards.

   ## Rule Status:  
   - Relevant for Modernization

   ## Algorithm:
   - **Step 1**: Split the phone number into three parts:  
     - Area Code (`XXX`): The first three digits.  
     - Prefix (`XXX`): The next three digits.  
     - Line Number (`XXXX`): The last four digits.
   - **Step 2**: Validate the Area Code:  
     - Ensure it is a three-digit numeric value.  
     - Ensure it is not zero and matches valid North American area codes.
   - **Step 3**: Validate the Prefix:  
     - Ensure it is a three-digit numeric value.  
     - Ensure it is not zero.
   - **Step 4**: Validate the Line Number:  
     - Ensure it is a four-digit numeric value.  
     - Ensure it is not zero.
   - **Step 5**: If all validations pass, mark the phone number as valid and proceed with further processing.

   **Mathematical Formula for Validation**:  
   - Area Code: `Area Code IS NUMERIC AND IN VALID AREA CODE LIST`  
   - Prefix: `Prefix IS NUMERIC AND != 0`  
   - Line Number: `Line Number IS NUMERIC AND != 0`
<!-- rule-end -->
## Data Structure

The application uses a structured data model to manage account and customer information. Below is a detailed description of the data structure, including field names, data types, sizes, and additional notes based on the provided data dictionary.

| Field ID | Field Name | Data Type | Field Size | Description | Mandatory | Default Value | Additional Notes | Relevant for Modernization | Information Type |
|-------------|---------------|---------------|------------------|-----------|-------------|--------------|------------------|-----------------------------|--------------------|
| `ACCT-UPDATE-ID` | Account ID | Numeric | 11 | Unique identifier for the account. | Yes | None | Must be an 11-digit non-zero numeric value. | Yes | Primary Key |
| `ACCT-UPDATE-ACTIVE-STATUS` | Active Status | Alphanumeric | 1 | Indicates whether the account is active ('Y' or 'N'). | Yes | None | Must be 'Y' or 'N'. | Yes | Status |
| `ACCT-UPDATE-CURR-BAL` | Current Balance | Signed Numeric | 12 (10 digits + 2 decimals) | Current balance of the account. | Yes | 0.00 | Must be a valid signed numeric value. | Yes | Financial |
| `ACCT-UPDATE-CREDIT-LIMIT` | Credit Limit | Signed Numeric | 12 (10 digits + 2 decimals) | Maximum credit limit for the account. | Yes | 0.00 | Must be a valid signed numeric value. | Yes | Financial |
| `ACCT-UPDATE-CASH-CREDIT-LIMIT` | Cash Credit Limit | Signed Numeric | 12 (10 digits + 2 decimals) | Maximum cash credit limit for the account. | Yes | 0.00 | Must be a valid signed numeric value. | Yes | Financial |
| `ACCT-UPDATE-OPEN-DATE` | Open Date | Alphanumeric | 10 | Date when the account was opened (format: CCYY-MM-DD). | Yes | None | Must be a valid date. | Yes | Date |
| `ACCT-UPDATE-EXPIRAION-DATE` | Expiry Date | Alphanumeric | 10 | Expiration date of the account (format: CCYY-MM-DD). | Yes | None | Must be a valid date. | Yes | Date |
| `ACCT-UPDATE-REISSUE-DATE` | Reissue Date | Alphanumeric | 10 | Date when the account was reissued (format: CCYY-MM-DD). | Yes | None | Must be a valid date. | Yes | Date |
| `ACCT-UPDATE-CURR-CYC-CREDIT` | Current Cycle Credit | Signed Numeric | 12 (10 digits + 2 decimals) | Credit transactions in the current cycle. | Yes | 0.00 | Must be a valid signed numeric value. | Yes | Financial |
| `ACCT-UPDATE-CURR-CYC-DEBIT` | Current Cycle Debit | Signed Numeric | 12 (10 digits + 2 decimals) | Debit transactions in the current cycle. | Yes | 0.00 | Must be a valid signed numeric value. | Yes | Financial |
| `ACCT-UPDATE-GROUP-ID` | Group ID | Alphanumeric | 10 | Identifier for the account group. | No | None | Used for grouping accounts. | Yes | Grouping |
| `CUST-UPDATE-ID` | Customer ID | Numeric | 9 | Unique identifier for the customer. | Yes | None | Must be a 9-digit numeric value. | Yes | Primary Key |
| `CUST-UPDATE-FIRST-NAME` | First Name | Alphanumeric | 25 | Customer's first name. | Yes | None | Must contain only alphabets and spaces. | Yes | Personal |
| `CUST-UPDATE-MIDDLE-NAME` | Middle Name | Alphanumeric | 25 | Customer's middle name. | No | None | Optional field. | Yes | Personal |
| `CUST-UPDATE-LAST-NAME` | Last Name | Alphanumeric | 25 | Customer's last name. | Yes | None | Must contain only alphabets and spaces. | Yes | Personal |
| `CUST-UPDATE-ADDR-LINE-1` | Address Line 1 | Alphanumeric | 50 | First line of the customer's address. | Yes | None | Must be supplied. | Yes | Address |
| `CUST-UPDATE-ADDR-LINE-2` | Address Line 2 | Alphanumeric | 50 | Second line of the customer's address. | No | None | Optional field. | Yes | Address |
| `CUST-UPDATE-ADDR-LINE-3` | City | Alphanumeric | 50 | City of the customer's address. | Yes | None | Must contain only alphabets and spaces. | Yes | Address |
| `CUST-UPDATE-ADDR-STATE-CD` | State Code | Alphanumeric | 2 | State code of the customer's address. | Yes | None | Must be a valid US state code. | Yes | Address |
| `CUST-UPDATE-ADDR-COUNTRY-CD` | Country Code | Alphanumeric | 3 | Country code of the customer's address. | Yes | None | Must be a valid country code. | Yes | Address |
| `CUST-UPDATE-ADDR-ZIP` | ZIP Code | Alphanumeric | 10 | ZIP code of the customer's address. | Yes | None | Must be numeric and valid for the state. | Yes | Address |
| `CUST-UPDATE-PHONE-NUM-1` | Phone Number 1 | Alphanumeric | 15 | Primary phone number of the customer (format: (XXX)XXX-XXXX). | Yes | None | Must follow North American phone number format. | Yes | Contact |
| `CUST-UPDATE-PHONE-NUM-2` | Phone Number 2 | Alphanumeric | 15 | Secondary phone number of the customer (format: (XXX)XXX-XXXX). | No | None | Optional field. | Yes | Contact |
| `CUST-UPDATE-SSN` | Social Security Number | Numeric | 9 | Customer's SSN (format: XXX-XX-XXXX). | Yes | None | Must follow SSN validation rules. | Yes | Personal |
| `CUST-UPDATE-GOVT-ISSUED-ID` | Government Issued ID | Alphanumeric | 20 | Government-issued ID for the customer. | No | None | Optional field. | Yes | Identification |
| `CUST-UPDATE-DOB-YYYY-MM-DD` | Date of Birth | Alphanumeric | 10 | Customer's date of birth (format: CCYY-MM-DD). | Yes | None | Must be a valid date. | Yes | Personal |
| `CUST-UPDATE-EFT-ACCOUNT-ID` | EFT Account ID | Alphanumeric | 10 | Electronic Funds Transfer account ID. | Yes | None | Must be numeric. | Yes | Financial |
| `CUST-UPDATE-PRI-CARD-IND` | Primary Card Holder Indicator | Alphanumeric | 1 | Indicates if the customer is the primary cardholder ('Y' or 'N'). | Yes | None | Must be 'Y' or 'N'. | Yes | Status |
| `CUST-UPDATE-FICO-CREDIT-SCORE` | FICO Credit Score | Numeric | 3 | Customer's FICO credit score. | Yes | None | Must be between 300 and 850. | Yes | Financial |

### Notes:
- **Mandatory Fields**: Fields marked as mandatory must be supplied by the user or system during data entry or updates.
- **Default Values**: Fields with default values are initialized to these values if not explicitly provided.
- **Relevant for Modernization**: Fields relevant for modernization may require mapping to database tables, integration with APIs, or validation using modern libraries.
- **Information Type**: Categorizes fields into types such as Primary Key, Financial, Address, Personal, Contact, etc., to help organize data for modernization efforts.
## Items Relevant for Modernization

In the context of modernization, several functions and methods within the `COACTUPC.cbl` program are critical for transitioning to a more modern architecture. These items are essential for ensuring the program's functionality is preserved while leveraging modern technologies, such as relational databases, RESTful APIs, and web-based interfaces. Below is a list of the most relevant functions and methods for modernization:

### 1. **Account ID Validation**
   - **Relevance**: Ensures the integrity of account identification, which is critical for linking account data to customer and card records.
   - **Modernization Approach**: Replace COBOL-based validation logic with centralized validation services or database constraints. Use modern libraries for numeric and format validation.

### 2. **Credit Limit Validation**
   - **Relevance**: Validates financial limits, which are essential for account management and compliance.
   - **Modernization Approach**: Transition to database-level constraints or API-based validation services. Use modern libraries for handling signed numeric values and financial calculations.

### 3. **Date Validation (CCYY-MM-DD Format)**
   - **Relevance**: Ensures accurate handling of dates, which are critical for account lifecycle management.
   - **Modernization Approach**: Replace COBOL date validation routines with standard date libraries in modern programming languages (e.g., Java, Python). Integrate with calendar APIs for enhanced functionality.

### 4. **Social Security Number (SSN) Validation**
   - **Relevance**: Validates sensitive personal information, ensuring compliance with data protection regulations.
   - **Modernization Approach**: Use specialized libraries or APIs for SSN validation. Implement encryption and masking techniques for secure storage and transmission.

### 5. **Phone Number Validation**
   - **Relevance**: Ensures accurate contact information, which is vital for customer communication and service.
   - **Modernization Approach**: Integrate with external APIs for phone number validation and formatting (e.g., Twilio, Google Phone Number Library). Replace COBOL-based validation logic with modern libraries.

### 6. **Data Retrieval from Master Files**
   - **Relevance**: Fetches account and customer data from legacy datasets, which is essential for processing updates.
   - **Modernization Approach**: Migrate datasets to relational databases or cloud-based storage solutions. Replace CICS `READ` commands with database queries or API calls.

### 7. **Data Update to Master Files**
   - **Relevance**: Updates account and customer data in legacy datasets, ensuring data consistency and integrity.
   - **Modernization Approach**: Transition to database transactions or API-based updates. Implement rollback mechanisms using modern transaction management systems.

### 8. **Screen Management**
   - **Relevance**: Manages user interactions through the CICS interface, which is critical for guiding users through the update process.
   - **Modernization Approach**: Replace screen-based interactions with web-based UI frameworks (e.g., React, Angular). Use RESTful APIs to handle data exchange between the UI and backend.

### 9. **Error Handling**
   - **Relevance**: Provides feedback to users and handles unexpected errors gracefully.
   - **Modernization Approach**: Implement structured exception handling in modern programming languages. Centralize error messages in configuration files or databases for easier maintenance.

### 10. **Abend Handling**
   - **Relevance**: Manages abnormal program termination, ensuring the system remains stable.
   - **Modernization Approach**: Replace COBOL abend routines with modern logging and monitoring tools (e.g., ELK Stack, Splunk). Use structured exception handling to manage errors.

### 11. **Field-Level Validations**
   - **Relevance**: Validates individual fields for correctness and completeness, ensuring data integrity.
   - **Modernization Approach**: Use modern validation libraries or frameworks (e.g., Hibernate Validator, Python Cerberus). Implement centralized validation services for consistency.

### 12. **Cross-Field Validations**
   - **Relevance**: Ensures relationships between fields are valid (e.g., State and ZIP Code combinations).
   - **Modernization Approach**: Integrate with external validation APIs (e.g., USPS ZIP Code API). Replace COBOL logic with modern rule engines or validation frameworks.

### 13. **Data Comparison**
   - **Relevance**: Detects changes between old and new data, ensuring updates are meaningful.
   - **Modernization Approach**: Use modern data comparison libraries or frameworks. Implement change tracking mechanisms in databases or APIs.

### 14. **Audit and Logging**
   - **Relevance**: Maintains a record of changes for auditing purposes, ensuring compliance and traceability.
   - **Modernization Approach**: Implement centralized logging systems (e.g., Logstash, Fluentd). Use database triggers or event-driven architectures for audit trails.

### 15. **Constants and Literals**
   - **Relevance**: Defines reusable constants and literals for the program, ensuring consistency.
   - **Modernization Approach**: Centralize constants in configuration files or environment variables. Use modern dependency injection frameworks for managing literals.

### 16. **PFKey Management**
   - **Relevance**: Handles user navigation through PFKeys, which is critical for user experience.
   - **Modernization Approach**: Replace PFKeys with modern UI elements like buttons, dropdowns, or navigation bars. Use event-driven programming for handling user actions.

### 17. **Communication Area Management**
   - **Relevance**: Manages shared data between programs, ensuring seamless data exchange.
   - **Modernization Approach**: Replace commarea with API-based data exchange or message queues (e.g., RabbitMQ, Kafka). Use JSON or XML for structured data transmission.

### 18. **Lookup Data Integration**
   - **Relevance**: Provides reference data for validations (e.g., North American phone area codes).
   - **Modernization Approach**: Integrate with external APIs or databases for dynamic lookup data. Replace static copybooks with centralized data repositories.

### 19. **Date Utility Routines**
   - **Relevance**: Provides common date handling functions, ensuring consistency across the application.
   - **Modernization Approach**: Use standard date libraries in modern programming languages. Replace COBOL routines with built-in date handling features.

### 20. **Dataset Layouts**
   - **Relevance**: Defines the structure of account and customer records, ensuring data consistency.
   - **Modernization Approach**: Map dataset layouts to relational database schemas. Use ORM (Object-Relational Mapping) tools for managing data structures.

By focusing on these items, modernization efforts can ensure the program's functionality is preserved while leveraging modern technologies for improved performance, scalability, and maintainability.
## Items Irrelevant for Modernization

In the context of modernization, certain functions and methods within the `COACTUPC.cbl` program are either outdated or redundant in modern architectures. These items can be ignored or replaced with more efficient alternatives during the modernization process. Below is a list of functions and methods deemed irrelevant for modernization:

### 1. **PFKey Mapping**
   - **Reason**: PFKeys (Program Function Keys) are specific to legacy CICS interfaces and are not relevant in modern web-based or mobile interfaces.
   - **Modernization Alternative**: Replace PFKeys with modern UI elements such as buttons, dropdown menus, or navigation bars.

### 2. **Screen Attribute Management**
   - **Reason**: The management of screen attributes (e.g., protection, cursor positioning) is specific to CICS-based terminal interfaces and does not apply to modern web or graphical user interfaces.
   - **Modernization Alternative**: Transition to web-based UI frameworks (e.g., React, Angular) that handle screen attributes dynamically.

### 3. **Abend Routine**
   - **Reason**: The abend routine is specific to COBOL-CICS environments for handling abnormal program termination. Modern systems use structured exception handling and logging frameworks.
   - **Modernization Alternative**: Replace abend routines with centralized logging and monitoring tools (e.g., ELK Stack, Splunk) and structured exception handling mechanisms.

### 4. **CICS-Specific Commands**
   - **Reason**: Commands such as `EXEC CICS HANDLE ABEND`, `EXEC CICS RETURN`, and `EXEC CICS XCTL` are specific to the CICS environment and are not relevant in modern architectures.
   - **Modernization Alternative**: Replace CICS commands with RESTful APIs, microservices, or event-driven architectures for data exchange and process management.

### 5. **Copybooks for Screen Layouts**
   - **Reason**: Copybooks like `COACTUP` define screen layouts for CICS interfaces, which are irrelevant in modern web-based systems.
   - **Modernization Alternative**: Replace screen layouts with HTML/CSS templates or modern UI frameworks.

### 6. **Static Lookup Data**
   - **Reason**: Copybooks like `CSLKPCDY` contain static lookup data (e.g., North American phone area codes), which are outdated and inflexible.
   - **Modernization Alternative**: Integrate with dynamic external APIs or centralized databases for real-time lookup data.

### 7. **Hardcoded Literals**
   - **Reason**: Hardcoded literals for transaction IDs, program names, and file paths are inflexible and difficult to maintain.
   - **Modernization Alternative**: Centralize literals in configuration files or environment variables for easier management and scalability.

### 8. **Commarea Management**
   - **Reason**: The use of `DFHCOMMAREA` for inter-program communication is specific to COBOL-CICS environments and is not relevant in modern systems.
   - **Modernization Alternative**: Replace commarea with API-based data exchange or message queues (e.g., RabbitMQ, Kafka).

### 9. **INSPECT Statements for Data Conversion**
   - **Reason**: INSPECT statements are used for converting data formats (e.g., alphanumeric to numeric), which are cumbersome and outdated.
   - **Modernization Alternative**: Use modern programming libraries for data conversion and validation.

### 10. **File Error Messages**
   - **Reason**: The construction of file error messages using COBOL string manipulation is outdated and lacks flexibility.
   - **Modernization Alternative**: Use centralized logging frameworks or error-handling libraries for consistent and scalable error reporting.

### 11. **CICS-Specific Screen Initialization**
   - **Reason**: Screen initialization routines (e.g., `3100-SCREEN-INIT`) are specific to CICS terminal interfaces and are irrelevant in modern systems.
   - **Modernization Alternative**: Replace screen initialization with dynamic web-based UI rendering.

### 12. **CICS-Specific Screen Sending**
   - **Reason**: The `EXEC CICS SEND MAP` command is specific to CICS environments and does not apply to modern web-based systems.
   - **Modernization Alternative**: Replace screen sending with RESTful APIs and web-based rendering frameworks.

### 13. **CICS-Specific Cursor Positioning**
   - **Reason**: Cursor positioning logic is specific to CICS terminal interfaces and is irrelevant in modern web-based systems.
   - **Modernization Alternative**: Use modern UI frameworks that handle focus and navigation dynamically.

### 14. **Static Date Utility Routines**
   - **Reason**: Copybooks like `CSUTLDPY` provide static date utility routines, which are inflexible and outdated.
   - **Modernization Alternative**: Replace static routines with standard date libraries in modern programming languages (e.g., Java, Python).

### 15. **Static Screen Titles**
   - **Reason**: Copybooks like `COTTL01Y` define static screen titles, which are irrelevant in modern dynamic web-based systems.
   - **Modernization Alternative**: Use dynamic templates for rendering screen titles based on user context.

### 16. **CICS-Specific Transaction Management**
   - **Reason**: Transaction management using CICS commands is specific to COBOL-CICS environments and is not relevant in modern architectures.
   - **Modernization Alternative**: Replace transaction management with modern database transaction systems or distributed transaction frameworks.

### 17. **CICS-Specific Error Codes**
   - **Reason**: Error codes like `DFHRESP(NORMAL)` and `DFHRESP(NOTFND)` are specific to CICS environments and are irrelevant in modern systems.
   - **Modernization Alternative**: Replace error codes with structured exception handling and standardized HTTP status codes.

### 18. **Static Dataset Layouts**
   - **Reason**: Copybooks like `CVACT01Y` and `CVCUS01Y` define static dataset layouts, which are inflexible and difficult to scale.
   - **Modernization Alternative**: Map dataset layouts to relational database schemas or JSON/XML structures for dynamic data handling.

By identifying these irrelevant items, modernization efforts can focus on replacing outdated components with scalable, flexible, and efficient alternatives that align with modern technologies and practices.
