# COCRDUPC.cbl: Credit Card Detail Processing Program

## Overview
The `COCRDUPC.cbl` file is a COBOL program designed to handle credit card detail requests within a business logic layer. It facilitates the retrieval, validation, editing, and updating of credit card information stored in a database. The program interacts with a CICS (Customer Information Control System) environment to manage transactions and user interactions through screens. Its primary purpose is to ensure accurate processing of credit card data, including account numbers, card numbers, cardholder names, expiration dates, and active statuses.

The program supports the following functionalities:
- Validating user inputs for credit card details.
- Fetching credit card details based on search criteria.
- Allowing users to edit and update credit card information.
- Handling errors and providing appropriate feedback messages.
- Ensuring data integrity during updates by checking for concurrent modifications.

This program contributes to the overall system by enabling secure and efficient management of credit card data, ensuring compliance with business rules and data validation standards.

<!-- general-rule-start -->
## Business Rules:
### Input Validation
1. **Account Number Validation**:
   - Must be a non-zero, 11-digit numeric value.
   - If the account number is missing, blank, or invalid, the program sets an error flag and prompts the user to provide a valid account number.

2. **Card Number Validation**:
   - Must be a 16-digit numeric value.
   - If the card number is missing, blank, or invalid, the program sets an error flag and prompts the user to provide a valid card number.

3. **Cardholder Name Validation**:
   - Must contain only alphabetic characters and spaces.
   - If the name is missing, blank, or contains invalid characters, the program sets an error flag and prompts the user to provide a valid name.

4. **Card Active Status Validation**:
   - Must be either 'Y' (Yes) or 'N' (No).
   - If the status is missing, blank, or invalid, the program sets an error flag and prompts the user to provide a valid status.

5. **Expiration Date Validation**:
   - Month must be numeric and between 1 and 12.
   - Year must be numeric and between 1950 and 2099.
   - If the expiration date is missing, blank, or invalid, the program sets an error flag and prompts the user to provide a valid date.

### Data Retrieval
1. **Fetching Credit Card Details**:
   - The program reads the credit card database using the account number and card number as keys.
   - If the record is found, the program retrieves details such as CVV code, cardholder name, expiration date, and active status.
   - If the record is not found, the program sets an error flag and displays an appropriate message.

### Data Update
1. **Editing Credit Card Details**:
   - Users can modify cardholder name, expiration date, and active status.
   - The program validates the new inputs before proceeding with the update.

2. **Concurrent Modification Check**:
   - Before updating the record, the program checks if the data has been modified by another user.
   - If concurrent modifications are detected, the program aborts the update and prompts the user to review the latest data.

3. **Updating the Database**:
   - The program locks the record for update and writes the new details to the database.
   - If the update fails, the program sets an error flag and displays an appropriate message.

### User Interaction
1. **Screen Navigation**:
   - The program provides screens for entering search criteria, viewing credit card details, and editing data.
   - Users can navigate between screens using function keys (e.g., F3 to exit, F5 to save changes).

2. **Feedback Messages**:
   - The program displays messages to guide users through the process, such as prompts for missing inputs, confirmation of successful updates, and error notifications.

### Error Handling
1. **File Errors**:
   - If a file operation fails (e.g., read or write), the program constructs an error message with details about the operation, file name, and response codes.

2. **Unexpected Scenarios**:
   - If the program encounters unexpected data or conditions, it triggers an abend (abnormal end) routine to handle the error gracefully.

### Constants and Literals
1. **Program Identifiers**:
   - The program uses predefined literals for transaction IDs, map sets, and map names to ensure consistency in CICS operations.

2. **Validation Ranges**:
   - Constants are defined for valid ranges of months (1-12) and years (1950-2099).

### Workflow
1. **Initialization**:
   - The program initializes working storage variables and clears error messages at the start of each transaction.

2. **Input Processing**:
   - User inputs are received and validated. Invalid inputs are flagged, and appropriate messages are displayed.

3. **Decision Making**:
   - Based on user inputs and program state, the program decides whether to fetch data, edit details, or update the database.

4. **Screen Updates**:
   - The program prepares and sends screens to the user, including search criteria, credit card details, and feedback messages.

5. **Transaction Completion**:
   - The program commits changes to the database and returns control to the calling program or main menu.

### Key Data Fields
1. **Input Fields**:
   - Account ID (`CC-ACCT-ID`)
   - Card Number (`CC-CARD-NUM`)
   - Cardholder Name (`CCUP-NEW-CRDNAME`)
   - Expiration Date (Year: `CCUP-NEW-EXPYEAR`, Month: `CCUP-NEW-EXPMON`, Day: `CCUP-NEW-EXPDAY`)
   - Active Status (`CCUP-NEW-CRDSTCD`)

2. **Output Fields**:
   - Account ID (`CARD-ACCT-ID`)
   - Card Number (`CARD-CARD-NUM`)
   - Cardholder Name (`CARD-NAME-EMBOSSED`)
   - Expiration Date (`CARD-EXPIRAION-DATE`)
   - Active Status (`CARD-STATUS`)

3. **Flags**:
   - Input validation flags (e.g., `FLG-ACCTFILTER-ISVALID`, `FLG-CARDFILTER-NOT-OK`)
   - Return flags (e.g., `WS-RETURN-FLAG-ON`, `WS-RETURN-FLAG-OFF`)

4. **Messages**:
   - Information messages (e.g., `PROMPT-FOR-SEARCH-KEYS`, `CONFIRM-UPDATE-SUCCESS`)
   - Error messages (e.g., `SEARCHED-ACCT-NOT-NUMERIC`, `CARD-STATUS-MUST-BE-YES-NO`)

### CICS Operations
1. **Transaction Handling**:
   - The program uses CICS commands to manage transactions, such as `EXEC CICS HANDLE ABEND`, `EXEC CICS READ`, and `EXEC CICS REWRITE`.

2. **Screen Management**:
   - Screens are sent and received using CICS commands (`EXEC CICS SEND MAP`, `EXEC CICS RECEIVE MAP`).

3. **Synchronization**:
   - The program ensures data consistency by using CICS `SYNCPOINT` commands.

### Summary
The `COCRDUPC.cbl` program is a critical component of the credit card management system, ensuring secure and accurate processing of credit card details. It adheres to strict validation rules, provides user-friendly interactions, and handles errors gracefully to maintain data integrity and system reliability.
<!-- general-rule-end -->
## Dependencies

The `COCRDUPC.cbl` program relies on several dependencies to function effectively within the credit card management system. These dependencies include COBOL copybooks, CICS commands, external files, and predefined literals/constants. Each dependency plays a critical role in ensuring the program's functionality, data integrity, and user interaction. Below is a detailed table outlining the dependencies, their descriptions, types, references, and relevance for modernization.

| Dependency | Description | Type | Reference | Relevance for Modernization |
|-------------|-------------|------|-----------|------------------------------|
| `CVCRD01Y` | COBOL copybook containing definitions for credit card-related data structures. | Copybook | File: `CVCRD01Y` | Essential for defining the structure of credit card records and ensuring compatibility with database operations. |
| `COCOM01Y` | COBOL copybook for application commarea definitions, used for passing data between programs. | Copybook | File: `COCOM01Y` | Critical for maintaining data consistency across program calls and transactions. |
| `DFHBMSCA` | IBM-supplied copybook for CICS Basic Mapping Support (BMS) screen attributes. | Copybook | File: `DFHBMSCA` | Necessary for managing screen attributes and user interface elements in CICS. |
| `DFHAID` | IBM-supplied copybook for handling attention identifiers (AID) in CICS. | Copybook | File: `DFHAID` | Important for managing user inputs and function key mappings in CICS screens. |
| `COTTL01Y` | COBOL copybook for screen titles used in the application. | Copybook | File: `COTTL01Y` | Provides consistent screen titles across the application, enhancing user experience. |
| `COCRDUP` | COBOL copybook for the credit card update screen layout. | Copybook | File: `COCRDUP` | Defines the layout of the credit card update screen, ensuring proper user interaction. |
| `CSDAT01Y` | COBOL copybook for handling current date and time operations. | Copybook | File: `CSDAT01Y` | Ensures accurate date and time handling, which is critical for transaction timestamps and validation. |
| `CSMSG01Y` | COBOL copybook for common messages used across the application. | Copybook | File: `CSMSG01Y` | Provides standardized error and information messages, improving user communication. |
| `CSMSG02Y` | COBOL copybook for abend (abnormal end) variables and messages. | Copybook | File: `CSMSG02Y` | Facilitates error handling and debugging by providing structured abend messages. |
| `CSUSR01Y` | COBOL copybook for signed-on user data. | Copybook | File: `CSUSR01Y` | Ensures user authentication and session management within the application. |
| `CVACT02Y` | COBOL copybook for card record layout definitions. | Copybook | File: `CVACT02Y` | Defines the structure of card records, ensuring compatibility with database operations. |
| `LIT-CARDFILENAME` | Literal defining the name of the credit card database file. | Literal | Value: `CARDDAT` | Essential for identifying the database file used for credit card operations. |
| `LIT-CARDFILENAME-ACCT-PATH` | Literal defining the account path for the credit card database file. | Literal | Value: `CARDAIX` | Important for locating account-specific data in the database. |
| CICS API | Provides commands for transaction management, screen handling, and database operations. | API | IBM CICS | Integral for managing transactions, user interactions, and database access in a mainframe environment. |
| Credit Card Database | External database storing credit card details such as account numbers, card numbers, expiration dates, and statuses. | Database | File: `CARDDAT` | Central to the program's functionality, as all credit card operations depend on this database. |
| `CSSTRPFY` | COBOL copybook for storing PFKey mappings. | Copybook | File: `CSSTRPFY` | Ensures proper mapping and handling of function keys for user navigation. |

### General Explanation
The dependencies listed above are integral to the program's operation. They provide the necessary data structures, screen layouts, and transaction management capabilities required for processing credit card details. The COBOL copybooks define the structure and behavior of various components, while the CICS API enables seamless interaction with the mainframe environment. The credit card database serves as the central repository for all card-related information.

### Relevance for Modernization
Modernizing the `COCRDUPC.cbl` program will require careful consideration of these dependencies:
- **Copybooks**: These should be analyzed and potentially refactored to align with modern data structures and formats.
- **CICS API**: Transitioning to modern APIs or middleware solutions may be necessary to improve scalability and integration with newer systems.
- **Database**: Migrating the credit card database to a modern relational or NoSQL database could enhance performance and accessibility.
- **Screen Layouts**: Updating screen layouts to support web-based or mobile interfaces will improve user experience and accessibility.

By addressing these dependencies during modernization, the program can be transformed into a more robust, scalable, and user-friendly solution.
## Detailed Rules

<!-- rule-start -->
### 1. Rule / Function / Method - Input Validation
- **Detailed Description of the Rule**:  
  This rule validates user inputs for credit card details, ensuring that all required fields meet predefined criteria. It checks for missing, blank, or invalid values and sets appropriate flags to indicate errors. The validation covers account numbers, card numbers, cardholder names, expiration dates, and active statuses.

- **What it proposes to do**:  
  Ensure that user-provided data is accurate and adheres to business rules before proceeding with further processing.

#### Rule Status:  
- **Relevant for Modernization**: Input validation is critical for ensuring data integrity and preventing errors in downstream processes.

#### Algorithm:
1. **Account Number Validation**:
   - Check if the account number is missing, blank, or contains only zeros.
     - If true, set the `INPUT-ERROR` flag to `TRUE` and the `FLG-ACCTFILTER-BLANK` flag to `TRUE`.
     - Display the message: "Account number not provided."
   - Check if the account number is not numeric or does not have exactly 11 digits.
     - If true, set the `INPUT-ERROR` flag to `TRUE` and the `FLG-ACCTFILTER-NOT-OK` flag to `TRUE`.
     - Display the message: "Account number must be a non-zero 11-digit number."
   - If the account number passes both checks:
     - Set the `FLG-ACCTFILTER-ISVALID` flag to `TRUE`.

2. **Card Number Validation**:
   - Check if the card number is missing, blank, or contains only zeros.
     - If true, set the `INPUT-ERROR` flag to `TRUE` and the `FLG-CARDFILTER-BLANK` flag to `TRUE`.
     - Display the message: "Card number not provided."
   - Check if the card number is not numeric or does not have exactly 16 digits.
     - If true, set the `INPUT-ERROR` flag to `TRUE` and the `FLG-CARDFILTER-NOT-OK` flag to `TRUE`.
     - Display the message: "Card number must be a 16-digit number."
   - If the card number passes both checks:
     - Set the `FLG-CARDFILTER-ISVALID` flag to `TRUE`.

3. **Cardholder Name Validation**:
   - Check if the cardholder name is missing, blank, or contains only zeros.
     - If true, set the `INPUT-ERROR` flag to `TRUE` and the `FLG-CARDNAME-BLANK` flag to `TRUE`.
     - Display the message: "Card name not provided."
   - Check if the cardholder name contains invalid characters (non-alphabetic or non-space).
     - Convert all characters to spaces except alphabetic characters and spaces.
     - If the resulting string is empty, set the `INPUT-ERROR` flag to `TRUE` and the `FLG-CARDNAME-NOT-OK` flag to `TRUE`.
     - Display the message: "Card name can only contain alphabets and spaces."
   - If the cardholder name passes both checks:
     - Set the `FLG-CARDNAME-ISVALID` flag to `TRUE`.

4. **Card Active Status Validation**:
   - Check if the active status is missing, blank, or contains only zeros.
     - If true, set the `INPUT-ERROR` flag to `TRUE` and the `FLG-CARDSTATUS-BLANK` flag to `TRUE`.
     - Display the message: "Card Active Status must be Y or N."
   - Check if the active status is not 'Y' or 'N'.
     - If true, set the `INPUT-ERROR` flag to `TRUE` and the `FLG-CARDSTATUS-NOT-OK` flag to `TRUE`.
     - Display the message: "Card Active Status must be Y or N."
   - If the active status passes both checks:
     - Set the `FLG-CARDSTATUS-ISVALID` flag to `TRUE`.

5. **Expiration Date Validation**:
   - **Month Validation**:
     - Check if the month is missing, blank, or contains only zeros.
       - If true, set the `INPUT-ERROR` flag to `TRUE` and the `FLG-CARDEXPMON-BLANK` flag to `TRUE`.
       - Display the message: "Card expiry month must be between 1 and 12."
     - Check if the month is not numeric or not between 1 and 12.
       - If true, set the `INPUT-ERROR` flag to `TRUE` and the `FLG-CARDEXPMON-NOT-OK` flag to `TRUE`.
       - Display the message: "Card expiry month must be between 1 and 12."
     - If the month passes both checks:
       - Set the `FLG-CARDEXPMON-ISVALID` flag to `TRUE`.

   - **Year Validation**:
     - Check if the year is missing, blank, or contains only zeros.
       - If true, set the `INPUT-ERROR` flag to `TRUE` and the `FLG-CARDEXPYEAR-BLANK` flag to `TRUE`.
       - Display the message: "Invalid card expiry year."
     - Check if the year is not numeric or not between 1950 and 2099.
       - If true, set the `INPUT-ERROR` flag to `TRUE` and the `FLG-CARDEXPYEAR-NOT-OK` flag to `TRUE`.
       - Display the message: "Invalid card expiry year."
     - If the year passes both checks:
       - Set the `FLG-CARDEXPYEAR-ISVALID` flag to `TRUE`.

6. **Error Handling**:
   - If any validation fails, the program sets the `INPUT-ERROR` flag to `TRUE` and displays the appropriate error message.
   - If all validations pass, the program proceeds to the next step in the workflow.

<!-- rule-end -->

<!-- rule-start -->
### 2. Rule / Function / Method - Data Retrieval
- **Detailed Description of the Rule**:  
  This rule retrieves credit card details from the database based on the account number and card number provided by the user. It ensures that the data is fetched accurately and handles cases where the record is not found.

- **What it proposes to do**:  
  Fetch credit card details for display and editing purposes.

#### Rule Status:  
- **Relevant for Modernization**: Data retrieval is essential for enabling user interaction and ensuring accurate updates.

#### Algorithm:
1. **Initialize Retrieval Variables**:
   - Clear all fields in the `CCUP-OLD-DETAILS` structure to prepare for data retrieval.

2. **Set Retrieval Keys**:
   - Use the account number (`CC-ACCT-ID`) and card number (`CC-CARD-NUM`) as keys for database lookup.

3. **Read Database**:
   - Execute a CICS `READ` command to fetch the record from the credit card database (`CARDDAT`).
   - Use the keys (`WS-CARD-RID-CARDNUM`) and specify the length of the record.

4. **Evaluate Response**:
   - If the response is `NORMAL`:
     - Set the `FOUND-CARDS-FOR-ACCOUNT` flag to `TRUE`.
     - Populate the `CCUP-OLD-DETAILS` structure with the retrieved data:
       - CVV code (`CARD-CVV-CD`).
       - Cardholder name (`CARD-EMBOSSED-NAME`).
       - Expiration date (split into year, month, and day).
       - Active status (`CARD-ACTIVE-STATUS`).
   - If the response is `NOTFND`:
     - Set the `INPUT-ERROR` flag to `TRUE`.
     - Display the message: "Did not find cards for this search condition."
   - If the response is any other error:
     - Set the `INPUT-ERROR` flag to `TRUE`.
     - Construct an error message with details about the operation, file name, and response codes.

5. **Error Handling**:
   - If the record is not found or an error occurs, the program aborts further processing and displays the appropriate message.

<!-- rule-end -->

<!-- rule-start -->
### 3. Rule / Function / Method - Data Update
- **Detailed Description of the Rule**:  
  This rule updates credit card details in the database based on user-provided inputs. It ensures data integrity by checking for concurrent modifications and validating new inputs before committing changes.

- **What it proposes to do**:  
  Safely update credit card details in the database.

#### Rule Status:  
- **Relevant for Modernization**: Data update is critical for maintaining accurate and up-to-date records.

#### Algorithm:
1. **Lock Record for Update**:
   - Execute a CICS `READ` command with the `UPDATE` option to lock the record for modification.

2. **Check for Concurrent Modifications**:
   - Compare the current database record with the `CCUP-OLD-DETAILS` structure.
   - If any field has changed, set the `DATA-WAS-CHANGED-BEFORE-UPDATE` flag to `TRUE` and abort the update.

3. **Prepare Update Data**:
   - Populate the `CARD-UPDATE-RECORD` structure with new values:
     - Card number (`CCUP-NEW-CARDID`).
     - Account ID (`CC-ACCT-ID-N`).
     - CVV code (`CCUP-NEW-CVV-CD`).
     - Cardholder name (`CCUP-NEW-CRDNAME`).
     - Expiration date (concatenate year, month, and day).
     - Active status (`CCUP-NEW-CRDSTCD`).

4. **Write Updated Record**:
   - Execute a CICS `REWRITE` command to update the record in the database (`CARDDAT`).

5. **Evaluate Response**:
   - If the response is `NORMAL`:
     - Confirm the update and proceed.
   - If the response indicates a failure:
     - Set the `LOCKED-BUT-UPDATE-FAILED` flag to `TRUE`.
     - Display the message: "Update of record failed."

6. **Error Handling**:
   - If the update fails, the program aborts further processing and displays the appropriate message.

<!-- rule-end -->
## Data Structure

The data structure of the application is designed to handle credit card details, user inputs, and system-generated messages. Below is a detailed table describing the fields used in the program, their attributes, and relevance for modernization.

| Field ID | Field Name | Data Type | Field Size | Description | Mandatory | Default Value | Additional Notes | Relevant for Modernization | Information Type |
|-------------|---------------|---------------|------------------|-----------|-------------|--------------|------------------|-----------------------------|--------------------|
| `WS-RESP-CD` | Response Code | Signed Numeric | 9 digits | Stores the response code from CICS operations. | No | Zeros | Used for error handling and decision-making. | Yes | System Response |
| `WS-REAS-CD` | Reason Code | Signed Numeric | 9 digits | Stores the reason code for CICS operations. | No | Zeros | Provides additional context for errors. | Yes | System Response |
| `WS-TRANID` | Transaction ID | Alphanumeric | 4 characters | Identifies the current transaction. | Yes | Spaces | Used to track transaction flow. | Yes | Transaction Metadata |
| `WS-UCTRANS` | User Transaction ID | Alphanumeric | 4 characters | Stores user-specific transaction identifiers. | No | Spaces | May be used for user-specific tracking. | Yes | Transaction Metadata |
| `WS-INPUT-FLAG` | Input Flag | Alphanumeric | 1 character | Indicates the status of user input validation. | Yes | Spaces | Values: '0' (OK), '1' (Error), LOW-VALUES (Pending). | Yes | Validation Status |
| `WS-EDIT-ACCT-FLAG` | Account Filter Flag | Alphanumeric | 1 character | Indicates the validation status of the account number. | Yes | Spaces | Values: '0' (Not OK), '1' (Valid), ' ' (Blank). | Yes | Validation Status |
| `WS-EDIT-CARD-FLAG` | Card Filter Flag | Alphanumeric | 1 character | Indicates the validation status of the card number. | Yes | Spaces | Values: '0' (Not OK), '1' (Valid), ' ' (Blank). | Yes | Validation Status |
| `WS-EDIT-CARDNAME-FLAG` | Card Name Flag | Alphanumeric | 1 character | Indicates the validation status of the cardholder name. | Yes | Spaces | Values: '0' (Not OK), '1' (Valid), ' ' (Blank). | Yes | Validation Status |
| `WS-EDIT-CARDSTATUS-FLAG` | Card Status Flag | Alphanumeric | 1 character | Indicates the validation status of the card active status. | Yes | Spaces | Values: '0' (Not OK), '1' (Valid), ' ' (Blank). | Yes | Validation Status |
| `WS-EDIT-CARDEXPMON-FLAG` | Expiration Month Flag | Alphanumeric | 1 character | Indicates the validation status of the card expiration month. | Yes | Spaces | Values: '0' (Not OK), '1' (Valid), ' ' (Blank). | Yes | Validation Status |
| `WS-EDIT-CARDEXPYEAR-FLAG` | Expiration Year Flag | Alphanumeric | 1 character | Indicates the validation status of the card expiration year. | Yes | Spaces | Values: '0' (Not OK), '1' (Valid), ' ' (Blank). | Yes | Validation Status |
| `CARD-ACCT-ID-X` | Account ID (Alphanumeric) | Alphanumeric | 11 characters | Stores the account ID in alphanumeric format. | Yes | Spaces | Used for display purposes. | Yes | User Input |
| `CARD-ACCT-ID-N` | Account ID (Numeric) | Numeric | 11 digits | Stores the account ID in numeric format. | Yes | Zeros | Used for database operations. | Yes | User Input |
| `CARD-CARD-NUM-X` | Card Number (Alphanumeric) | Alphanumeric | 16 characters | Stores the card number in alphanumeric format. | Yes | Spaces | Used for display purposes. | Yes | User Input |
| `CARD-CARD-NUM-N` | Card Number (Numeric) | Numeric | 16 digits | Stores the card number in numeric format. | Yes | Zeros | Used for database operations. | Yes | User Input |
| `CARD-NAME-EMBOSSED-X` | Cardholder Name | Alphanumeric | 50 characters | Stores the name of the cardholder. | Yes | Spaces | Used for display and database operations. | Yes | User Input |
| `CARD-STATUS-X` | Card Active Status | Alphanumeric | 1 character | Indicates whether the card is active ('Y') or inactive ('N'). | Yes | Spaces | Used for display and database operations. | Yes | User Input |
| `CARD-EXPIRAION-DATE-X` | Expiration Date (Alphanumeric) | Alphanumeric | 10 characters | Stores the card expiration date in alphanumeric format. | Yes | Spaces | Format: YYYY-MM-DD. | Yes | User Input |
| `CARD-EXPIRAION-DATE-N` | Expiration Date (Numeric) | Numeric | 10 digits | Stores the card expiration date in numeric format. | Yes | Zeros | Used for database operations. | Yes | User Input |
| `CARD-EXPIRY-YEAR` | Expiration Year | Alphanumeric | 4 characters | Stores the year of expiration. | Yes | Spaces | Extracted from `CARD-EXPIRAION-DATE-X`. | Yes | User Input |
| `CARD-EXPIRY-MONTH` | Expiration Month | Alphanumeric | 2 characters | Stores the month of expiration. | Yes | Spaces | Extracted from `CARD-EXPIRAION-DATE-X`. | Yes | User Input |
| `CARD-EXPIRY-DAY` | Expiration Day | Alphanumeric | 2 characters | Stores the day of expiration. | Yes | Spaces | Extracted from `CARD-EXPIRAION-DATE-X`. | Yes | User Input |
| `WS-LONG-MSG` | Long Message | Alphanumeric | 500 characters | Stores detailed messages for user feedback. | No | Spaces | Used for error handling and information display. | Yes | System Message |
| `WS-INFO-MSG` | Information Message | Alphanumeric | 40 characters | Stores short messages for user feedback. | No | Spaces | Used for guiding user actions. | Yes | System Message |
| `WS-RETURN-MSG` | Return Message | Alphanumeric | 75 characters | Stores messages related to transaction outcomes. | No | Spaces | Used for error handling and transaction feedback. | Yes | System Message |
| `CCUP-OLD-DETAILS` | Old Credit Card Details | Group | Variable | Stores the original credit card details fetched from the database. | Yes | Low-Values | Used for comparison during updates. | Yes | Data Record |
| `CCUP-NEW-DETAILS` | New Credit Card Details | Group | Variable | Stores the updated credit card details provided by the user. | Yes | Low-Values | Used for database updates. | Yes | Data Record |
| `CARD-UPDATE-RECORD` | Update Record | Group | Variable | Stores the final record to be written to the database. | Yes | Low-Values | Used for database updates. | Yes | Data Record |

### General Notes:
- **Mandatory Fields**: Fields marked as mandatory must be provided by the user or system to ensure proper functionality.
- **Default Values**: Default values are used to initialize fields and prevent errors during processing.
- **Relevance for Modernization**: All fields are relevant for modernization, as they define the core data structure of the application. Transitioning to modern data formats (e.g., JSON, XML) and databases will require mapping these fields to new structures.
- **Information Type**: Fields are categorized as user input, system response, system message, or data record to clarify their purpose within the application.
## Items Relevant for Modernization

In the context of modernization, the following functions and methods from the `COCRDUPC.cbl` program are identified as most relevant. These items are critical for ensuring the program's functionality aligns with modern standards, technologies, and user expectations.

### 1. **Input Validation**
- **Relevance**: Input validation is essential for ensuring data integrity and preventing errors in downstream processes. Modernization should focus on implementing robust validation mechanisms using modern frameworks or libraries.
- **Key Methods**:
  - Validation of account numbers, card numbers, cardholder names, expiration dates, and active statuses.
  - Error flagging and user feedback mechanisms.
- **Modernization Focus**:
  - Replace COBOL validation logic with modern validation libraries (e.g., JavaScript, Python validators).
  - Implement real-time validation in web or mobile interfaces.

---

### 2. **Data Retrieval**
- **Relevance**: Accurate and efficient data retrieval is critical for enabling user interaction and ensuring the integrity of credit card details.
- **Key Methods**:
  - `9000-READ-DATA`: Fetches credit card details from the database.
  - `9100-GETCARD-BYACCTCARD`: Reads the database using account and card numbers as keys.
- **Modernization Focus**:
  - Transition from CICS file-based operations to modern database systems (e.g., SQL, NoSQL).
  - Implement APIs for data retrieval to support web and mobile applications.

---

### 3. **Data Update**
- **Relevance**: Safely updating credit card details is crucial for maintaining accurate records and ensuring data integrity.
- **Key Methods**:
  - `9200-WRITE-PROCESSING`: Updates credit card details in the database.
  - `9300-CHECK-CHANGE-IN-REC`: Checks for concurrent modifications before updating.
- **Modernization Focus**:
  - Replace CICS `REWRITE` operations with modern database update mechanisms.
  - Implement optimistic locking or version control to handle concurrent modifications.

---

### 4. **Screen Management**
- **Relevance**: The program's screen management logic is central to user interaction. Modernization should focus on transitioning from CICS screens to web-based or mobile interfaces.
- **Key Methods**:
  - `3000-SEND-MAP`: Sends screens to the user.
  - `1100-RECEIVE-MAP`: Receives user inputs from screens.
- **Modernization Focus**:
  - Replace CICS screen handling with modern UI frameworks (e.g., React, Angular).
  - Implement responsive design for better accessibility across devices.

---

### 5. **Error Handling**
- **Relevance**: Effective error handling ensures the program can recover gracefully from unexpected scenarios and provide meaningful feedback to users.
- **Key Methods**:
  - `ABEND-ROUTINE`: Handles abnormal program termination.
  - Error flagging and message construction throughout the program.
- **Modernization Focus**:
  - Replace COBOL error handling with structured exception handling in modern languages (e.g., Java, Python).
  - Implement centralized logging and monitoring systems for better error tracking.

---

### 6. **Transaction Management**
- **Relevance**: Managing transactions is critical for ensuring data consistency and integrity in a multi-user environment.
- **Key Methods**:
  - CICS `SYNCPOINT` commands for transaction synchronization.
  - Handling of transaction IDs (`WS-TRANID`, `WS-UCTRANS`).
- **Modernization Focus**:
  - Transition to modern transaction management systems (e.g., REST APIs, microservices).
  - Implement distributed transaction management for scalability.

---

### 7. **Data Structure**
- **Relevance**: The program's data structure defines how credit card details and user inputs are stored and processed. Modernization should focus on transitioning to modern data formats.
- **Key Methods**:
  - Use of COBOL group structures (`CCUP-OLD-DETAILS`, `CCUP-NEW-DETAILS`).
  - Field-level validation and manipulation.
- **Modernization Focus**:
  - Replace COBOL data structures with JSON or XML for better interoperability.
  - Implement schema validation using modern tools (e.g., JSON Schema, XML Schema).

---

### 8. **Constants and Literals**
- **Relevance**: Predefined constants and literals are used throughout the program for transaction IDs, map names, and validation ranges. Modernization should focus on centralizing these values for better maintainability.
- **Key Methods**:
  - Use of literals like `LIT-THISTRANID`, `LIT-CARDFILENAME`.
- **Modernization Focus**:
  - Replace hardcoded literals with configuration files or environment variables.
  - Implement centralized management of constants using modern tools (e.g., YAML, .env files).

---

### 9. **User Feedback Mechanisms**
- **Relevance**: Providing meaningful feedback to users is essential for guiding their actions and improving the user experience.
- **Key Methods**:
  - Construction of information and error messages (`WS-INFO-MSG`, `WS-RETURN-MSG`).
- **Modernization Focus**:
  - Replace COBOL message handling with modern notification systems (e.g., toast messages, modal dialogs).
  - Implement multilingual support for better accessibility.

---

### 10. **Integration with External Systems**
- **Relevance**: The program interacts with external systems like the credit card database and CICS environment. Modernization should focus on improving integration capabilities.
- **Key Methods**:
  - CICS commands for file operations (`READ`, `REWRITE`).
- **Modernization Focus**:
  - Replace CICS file-based operations with RESTful APIs or database queries.
  - Implement secure communication protocols (e.g., HTTPS, OAuth).

---

### Summary
The modernization of the `COCRDUPC.cbl` program should prioritize these functions and methods to ensure the system is scalable, maintainable, and user-friendly. Transitioning to modern technologies will enhance performance, improve user experience, and ensure compliance with current standards.
## Items Irrelevant for Modernization

In the context of modernization, certain functions and methods in the `COCRDUPC.cbl` program are deemed irrelevant due to their limited applicability in modern systems or their redundancy in newer architectures. These items can be ignored or replaced with more efficient alternatives during the modernization process.

### 1. **Hardcoded Literals for Screen Titles and Transaction Identifiers**
- **Description**: The program uses hardcoded literals such as `LIT-THISTRANID`, `LIT-THISMAPSET`, and `LIT-THISMAP` to define transaction IDs, map sets, and screen titles.
- **Reason for Irrelevance**: Modern systems typically use dynamic configuration files or environment variables to manage such values, making hardcoded literals unnecessary.
- **Modernization Approach**: Replace these literals with centralized configuration management tools (e.g., YAML, JSON, or .env files).

---

### 2. **CICS-Specific Screen Attribute Management**
- **Description**: The program uses CICS-specific commands and copybooks (`DFHBMSCA`) to manage screen attributes such as field protection and cursor positioning.
- **Reason for Irrelevance**: Modern systems use web-based or mobile interfaces that rely on HTML, CSS, and JavaScript for UI management, eliminating the need for CICS-specific screen attributes.
- **Modernization Approach**: Transition to modern UI frameworks (e.g., React, Angular) for screen management.

---

### 3. **PFKey Mapping**
- **Description**: The program includes logic for mapping function keys (PFKeys) to specific actions using the `CSSTRPFY` copybook.
- **Reason for Irrelevance**: Modern systems use graphical user interfaces (GUIs) with clickable buttons or touch-based interactions, making PFKey mapping obsolete.
- **Modernization Approach**: Replace PFKey-based navigation with intuitive UI elements like buttons, dropdowns, and menus.

---

### 4. **Abend Routine**
- **Description**: The program includes an abend routine (`ABEND-ROUTINE`) to handle abnormal program termination and send error messages to the user.
- **Reason for Irrelevance**: Modern systems use structured exception handling mechanisms and centralized logging systems, making abend routines redundant.
- **Modernization Approach**: Replace abend routines with modern error handling frameworks (e.g., try-catch blocks in Java or Python) and implement centralized logging (e.g., ELK Stack, Splunk).

---

### 5. **Low-Level CICS Commands for File Operations**
- **Description**: The program uses CICS commands (`READ`, `REWRITE`) for file-based database operations.
- **Reason for Irrelevance**: Modern systems use relational or NoSQL databases with standardized query languages (e.g., SQL, MongoDB queries), making CICS file operations unnecessary.
- **Modernization Approach**: Replace CICS file operations with database queries or API calls.

---

### 6. **Hardcoded Validation Ranges**
- **Description**: The program uses hardcoded ranges for validation, such as valid months (1-12) and years (1950-2099).
- **Reason for Irrelevance**: Modern systems use dynamic validation rules stored in configuration files or databases, allowing for easier updates and scalability.
- **Modernization Approach**: Replace hardcoded ranges with configurable validation rules stored in external files or databases.

---

### 7. **Legacy Screen Layouts**
- **Description**: The program uses COBOL copybooks (`COCRDUP`) to define screen layouts for CICS-based interfaces.
- **Reason for Irrelevance**: Modern systems use responsive web or mobile interfaces, making COBOL screen layouts obsolete.
- **Modernization Approach**: Replace COBOL screen layouts with modern UI frameworks and responsive design principles.

---

### 8. **Static Message Construction**
- **Description**: The program constructs static error and information messages using predefined literals and flags.
- **Reason for Irrelevance**: Modern systems use dynamic message generation and localization frameworks, allowing for multilingual support and context-aware messages.
- **Modernization Approach**: Replace static message construction with dynamic message generation using modern libraries (e.g., i18n for localization).

---

### 9. **CICS-Specific Transaction Management**
- **Description**: The program uses CICS-specific transaction management commands (`SYNCPOINT`) to ensure data consistency.
- **Reason for Irrelevance**: Modern systems use distributed transaction management frameworks (e.g., Kafka, RabbitMQ) or RESTful APIs for transaction handling.
- **Modernization Approach**: Replace CICS transaction management with modern distributed systems or API-based transaction handling.

---

### 10. **Redundant Flags**
- **Description**: The program uses multiple flags (e.g., `WS-RETURN-FLAG`, `WS-PFK-FLAG`) to track program states and user inputs.
- **Reason for Irrelevance**: Modern systems use centralized state management frameworks (e.g., Redux, Vuex) or session management tools, making redundant flags unnecessary.
- **Modernization Approach**: Replace redundant flags with centralized state management or session handling mechanisms.

---

### Summary
The items listed above are irrelevant for modernization due to their reliance on outdated technologies and practices. By replacing these components with modern alternatives, the program can achieve better scalability, maintainability, and user experience. These items can be safely ignored or replaced during the modernization process.
