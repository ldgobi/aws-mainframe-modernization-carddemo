# COCRDLIC.cbl: List Credit Cards

## Overview
The `COCRDLIC.cbl` file is a COBOL program designed to manage and display a list of credit cards based on user context and permissions. It operates within a CICS (Customer Information Control System) environment and provides functionality for filtering, navigating, and interacting with credit card records. The program supports two main use cases:
1. **Admin Users**: Display all credit cards.
2. **Non-Admin Users**: Display only credit cards associated with a specific account provided in the COMMAREA.

The program also handles user interactions such as paging through records, selecting specific cards for detailed views or updates, and returning to the main menu. It ensures data integrity and user input validation throughout its operations.

<!-- general-rule-start -->
## Business Rules:
### 1. **User Context and Permissions**
   - **Admin Users**: If the user is an admin and no specific account context is provided, the program lists all credit cards.
   - **Non-Admin Users**: If the user is not an admin, the program filters credit cards based on the account ID provided in the COMMAREA. Only cards associated with the specified account are displayed.

### 2. **Input Validation**
   - **Account ID Validation**:
     - Must be an 11-digit numeric value.
     - If invalid, an error message is displayed: "ACCOUNT FILTER, IF SUPPLIED MUST BE AN 11 DIGIT NUMBER."
   - **Card Number Validation**:
     - Must be a 16-digit numeric value.
     - If invalid, an error message is displayed: "CARD ID FILTER, IF SUPPLIED MUST BE A 16 DIGIT NUMBER."
   - **Selection Validation**:
     - Users can select one record for viewing or updating. If multiple selections are made, an error message is displayed: "PLEASE SELECT ONLY ONE RECORD TO VIEW OR UPDATE."

### 3. **Paging and Navigation**
   - **Page Down (PF8)**:
     - Displays the next set of records if available.
     - If no more records exist, an error message is displayed: "NO MORE PAGES TO DISPLAY."
   - **Page Up (PF7)**:
     - Displays the previous set of records if available.
     - If already on the first page, an error message is displayed: "NO PREVIOUS PAGES TO DISPLAY."
   - **First and Last Page Indicators**:
     - Flags are used to track whether the user is on the first or last page of records.

### 4. **Record Filtering**
   - Records are filtered based on the account ID and card number provided by the user.
   - If a record does not match the filter criteria, it is excluded from the display.

### 5. **Error Handling**
   - Errors during file operations (e.g., reading records) are captured and displayed with detailed messages, including the operation name, file name, and response codes.

### 6. **Screen Display**
   - The program initializes and populates the screen with credit card data.
   - Each screen can display up to 7 records at a time.
   - Attributes such as colors and protections are applied to screen elements based on the context (e.g., errors, valid selections).

### 7. **User Actions**
   - **PF3 (Exit)**: Returns to the main menu.
   - **Enter**:
     - If a record is selected for viewing, the program transfers control to the card detail view program (`COCRDSLC`).
     - If a record is selected for updating, the program transfers control to the card update program (`COCRDUPC`).

### 8. **File Operations**
   - **Start Browse**: Initiates a browse operation on the credit card file (`CARDDAT`).
   - **Read Next**: Reads the next record in the file.
   - **Read Previous**: Reads the previous record in the file.
   - **End Browse**: Ends the browse operation.

### 9. **COMMAREA Management**
   - The program uses the COMMAREA to pass data between different programs and transactions.
   - It initializes the COMMAREA on the first run and updates it with the current context during subsequent operations.

### 10. **Screen Messages**
   - Informational messages guide the user on available actions (e.g., "TYPE S FOR DETAIL, U TO UPDATE ANY RECORD").
   - Error messages are displayed prominently to indicate issues with user input or system operations.

### 11. **Constants and Literals**
   - The program defines several constants for transaction IDs, program names, mapsets, and file names to ensure consistency across operations.

### 12. **Debugging and Development**
   - The program includes debugging features such as sending plain text or long text messages for troubleshooting purposes. These features are not intended for production use.

### 13. **Performance Considerations**
   - The program limits the number of records displayed per screen to 7 to optimize performance and user experience.
   - Efficient file operations (e.g., STARTBR, READNEXT, READPREV) are used to minimize system resource usage.

### 14. **Error Scenarios**
   - If no records match the search criteria, the program displays: "NO RECORDS FOUND FOR THIS SEARCH CONDITION."
   - If a file operation fails, the program constructs a detailed error message and exits gracefully.

### 15. **User Interaction Flow**
   - The program guides the user through a structured flow:
     - Validate inputs.
     - Display records based on filters.
     - Allow navigation and selection.
     - Handle user actions (view, update, exit).
     - Return to the main menu or transfer control to another program as needed.

### 16. **Data Structures**
   - **Working Storage**:
     - Contains variables for screen data, input flags, error messages, and file handling.
   - **Linkage Section**:
     - Defines the COMMAREA structure for data exchange.
   - **Screen Data**:
     - Organized into rows and columns for efficient display and manipulation.

### 17. **CICS Integration**
   - The program leverages CICS commands for transaction management, screen handling, and file operations.
   - Key CICS commands include `SEND MAP`, `RECEIVE MAP`, `STARTBR`, `READNEXT`, `READPREV`, and `ENDBR`.

### 18. **Program Initialization**
   - On the first run, the program initializes all variables and sets up the context for subsequent operations.
   - It ensures a clean slate by resetting error messages and flags.

### 19. **Screen Attributes**
   - The program dynamically adjusts screen attributes (e.g., colors, protections) based on the current context and user actions.
   - Errors are highlighted in red, and valid selections are marked with specific attributes.

### 20. **Exit Conditions**
   - The program exits gracefully by returning control to the calling program or transaction.
   - It ensures that all resources (e.g., file handles) are released before exiting.

<!-- general-rule-end -->
## Dependencies
The `COCRDLIC.cbl` program relies on several dependencies to function correctly within the CICS environment. These dependencies include copybooks, datasets, and CICS commands that facilitate data handling, screen management, and transaction control. Each dependency plays a critical role in ensuring the program operates as intended. Below is a detailed breakdown of the dependencies:

| Dependency       | Description                                                                 | Type          | Reference                | Relevance for Modernization                                                                 |
|-------------------|-----------------------------------------------------------------------------|---------------|--------------------------|--------------------------------------------------------------------------------------------|
| `DFHBMSCA`       | IBM-supplied copybook for basic mapping support in CICS.                   | Copybook      | IBM CICS Library         | Essential for managing screen layouts and attributes in the CICS environment.              |
| `DFHAID`         | IBM-supplied copybook for handling attention identifier (AID) keys.        | Copybook      | IBM CICS Library         | Critical for detecting user actions such as pressing function keys (e.g., PF3, PF7, PF8). |
| `COCOM01Y`       | Application-specific copybook for managing the COMMAREA structure.         | Copybook      | Project Repository       | Required for passing data between programs and transactions.                               |
| `COCRDLI`        | Copybook defining the layout of the credit card list screen.               | Copybook      | Project Repository       | Essential for rendering the credit card list screen and managing user interactions.        |
| `CVACT02Y`       | Copybook defining the structure of the credit card dataset.                | Copybook      | Project Repository       | Necessary for reading and writing credit card records from the dataset.                    |
| `CARDDAT`        | Dataset containing credit card records.                                    | Dataset       | Dataset Name: `CARDDAT`  | Core data source for the program, containing all credit card information.                  |
| `CARDAIX`        | Alternate index dataset for accessing credit card records by account ID.   | Dataset       | Dataset Name: `CARDAIX`  | Enables efficient filtering of records based on account ID.                                |
| `CSUSR01Y`       | Copybook for managing signed-on user data.                                 | Copybook      | Project Repository       | Provides user context, such as admin or non-admin status, for filtering records.           |
| `CSDAT01Y`       | Copybook for handling current date and time.                               | Copybook      | Project Repository       | Used for displaying the current date and time on the screen.                               |
| `CSMSG01Y`       | Copybook for common messages used across the application.                  | Copybook      | Project Repository       | Provides standardized error and informational messages for user feedback.                  |
| `CSSTRPFY`       | Copybook for storing PF key mappings.                                      | Copybook      | Project Repository       | Ensures consistent handling of PF keys across the application.                             |
| `CCRDLIA`        | Mapset defining the layout of the credit card list screen.                 | Mapset        | Mapset Name: `CCRDLIA`   | Required for rendering the user interface in the CICS environment.                         |
| `COCRDSLC`       | Program for viewing detailed information about a specific credit card.     | Program       | Program Name: `COCRDSLC` | Called when a user selects a card for detailed viewing.                                    |
| `COCRDUPC`       | Program for updating credit card information.                              | Program       | Program Name: `COCRDUPC` | Called when a user selects a card for updating.                                            |
| `COMEN01C`       | Main menu program for the application.                                     | Program       | Program Name: `COMEN01C` | Called when the user exits the credit card list screen to return to the main menu.         |
| `IBM CICS`       | Transaction processing system used to run the program.                     | System        | IBM CICS Environment     | Provides the runtime environment for executing the program and managing transactions.      |

### General Explanation
1. **Copybooks**: These are reusable code modules that define data structures, screen layouts, and common logic. They ensure consistency and reduce redundancy across the application.
2. **Datasets**: The program interacts with datasets (`CARDDAT` and `CARDAIX`) to retrieve and filter credit card records. These datasets are central to the program's functionality.
3. **CICS Commands**: The program uses CICS commands (e.g., `SEND MAP`, `RECEIVE MAP`, `STARTBR`, `READNEXT`, `READPREV`, `ENDBR`) to manage user interactions, file operations, and transaction control.
4. **External Programs**: The program integrates with other modules (`COCRDSLC`, `COCRDUPC`, `COMEN01C`) to provide detailed views, updates, and navigation to the main menu.

### Relevance for Modernization
- **Copybooks**: These should be reviewed and potentially refactored into reusable modules or classes in the target modernization platform.
- **Datasets**: The dataset structure and access methods may need to be migrated to a modern database system (e.g., relational or NoSQL databases).
- **CICS Commands**: CICS-specific logic should be abstracted or replaced with equivalent transaction management mechanisms in the target environment.
- **Screen Layouts**: The mapsets and screen definitions should be converted to modern UI frameworks (e.g., web-based interfaces or desktop applications).
- **Integration Points**: The interactions with external programs should be re-evaluated for compatibility with modern service-oriented architectures (e.g., REST APIs or microservices).
## Detailed Rules

<!-- rule-start -->
1. ## Rule / Function / Method - Input Validation for Account ID
   - **Detailed Description of the Rule**: This rule validates the account ID provided by the user. The account ID must be an 11-digit numeric value. If the input is invalid, the program sets an error flag and displays an appropriate error message.
   - **What it proposes to do**: Ensure that the account ID meets the required format and is valid for further processing.

   ## Rule Status: 
   - Relevant for Modernization

   ## Algorithm:
   - **Step 1**: Check if the account ID is empty or contains only spaces.
     - If the account ID is empty or contains spaces, set the flag `FLG-ACCTFILTER-BLANK` to `TRUE`.
     - Set the account ID in the COMMAREA (`CDEMO-ACCT-ID`) to zero.
     - Exit the validation process.
   - **Step 2**: Check if the account ID is numeric.
     - If the account ID is not numeric, set the error flag `INPUT-ERROR` to `TRUE`.
     - Set the flag `FLG-ACCTFILTER-NOT-OK` to `TRUE`.
     - Set the flag `FLG-PROTECT-SELECT-ROWS-YES` to `TRUE`.
     - Display the error message: "ACCOUNT FILTER, IF SUPPLIED MUST BE AN 11 DIGIT NUMBER."
     - Set the account ID in the COMMAREA (`CDEMO-ACCT-ID`) to zero.
     - Exit the validation process.
   - **Step 3**: Check the length of the account ID.
     - If the account ID is not exactly 11 characters long, follow the same steps as in Step 2.
   - **Step 4**: If the account ID passes all checks:
     - Move the account ID to the COMMAREA (`CDEMO-ACCT-ID`).
     - Set the flag `FLG-ACCTFILTER-ISVALID` to `TRUE`.
   - **Mathematical Formula**: No mathematical calculations are involved in this rule.
<!-- rule-end -->

<!-- rule-start -->
2. ## Rule / Function / Method - Input Validation for Card Number
   - **Detailed Description of the Rule**: This rule validates the card number provided by the user. The card number must be a 16-digit numeric value. If the input is invalid, the program sets an error flag and displays an appropriate error message.
   - **What it proposes to do**: Ensure that the card number meets the required format and is valid for further processing.

   ## Rule Status: 
   - Relevant for Modernization

   ## Algorithm:
   - **Step 1**: Check if the card number is empty or contains only spaces.
     - If the card number is empty or contains spaces, set the flag `FLG-CARDFILTER-BLANK` to `TRUE`.
     - Set the card number in the COMMAREA (`CDEMO-CARD-NUM`) to zero.
     - Exit the validation process.
   - **Step 2**: Check if the card number is numeric.
     - If the card number is not numeric, set the error flag `INPUT-ERROR` to `TRUE`.
     - Set the flag `FLG-CARDFILTER-NOT-OK` to `TRUE`.
     - Set the flag `FLG-PROTECT-SELECT-ROWS-YES` to `TRUE`.
     - Display the error message: "CARD ID FILTER, IF SUPPLIED MUST BE A 16 DIGIT NUMBER."
     - Set the card number in the COMMAREA (`CDEMO-CARD-NUM`) to zero.
     - Exit the validation process.
   - **Step 3**: Check the length of the card number.
     - If the card number is not exactly 16 characters long, follow the same steps as in Step 2.
   - **Step 4**: If the card number passes all checks:
     - Move the card number to the COMMAREA (`CDEMO-CARD-NUM`).
     - Set the flag `FLG-CARDFILTER-ISVALID` to `TRUE`.
   - **Mathematical Formula**: No mathematical calculations are involved in this rule.
<!-- rule-end -->

<!-- rule-start -->
3. ## Rule / Function / Method - Paging Logic (Page Down)
   - **Detailed Description of the Rule**: This rule handles the logic for navigating to the next page of credit card records. It ensures that the user can view additional records if available.
   - **What it proposes to do**: Fetch the next set of records from the dataset and update the screen accordingly.

   ## Rule Status: 
   - Relevant for Modernization

   ## Algorithm:
   - **Step 1**: Check if the user pressed the PF8 key (Page Down).
     - If the PF8 key is pressed, proceed to the next step.
   - **Step 2**: Check if there are more records to display.
     - If no more records exist, set the flag `CA-NEXT-PAGE-NOT-EXISTS` to `TRUE`.
     - Display the error message: "NO MORE PAGES TO DISPLAY."
     - Exit the paging process.
   - **Step 3**: Fetch the next set of records.
     - Use the `READNEXT` CICS command to retrieve the next record from the dataset (`CARDDAT`).
     - Increment the screen counter (`WS-SCRN-COUNTER`) by 1 for each record fetched.
     - Populate the screen rows (`WS-ROW-CARD-NUM`, `WS-ROW-ACCTNO`, `WS-ROW-CARD-STATUS`) with the fetched data.
   - **Step 4**: Update the last page indicators.
     - If the maximum number of records for the screen is reached, set the flag `CA-LAST-PAGE-SHOWN` to `TRUE`.
     - Store the last record's account ID and card number in `WS-CA-LAST-CARD-ACCT-ID` and `WS-CA-LAST-CARD-NUM`.
   - **Step 5**: End the browse operation.
     - Use the `ENDBR` CICS command to release resources associated with the browse operation.
   - **Mathematical Formula**: Increment the screen counter:
     - `WS-SCRN-COUNTER = WS-SCRN-COUNTER + 1`
<!-- rule-end -->

<!-- rule-start -->
4. ## Rule / Function / Method - Paging Logic (Page Up)
   - **Detailed Description of the Rule**: This rule handles the logic for navigating to the previous page of credit card records. It ensures that the user can view earlier records if available.
   - **What it proposes to do**: Fetch the previous set of records from the dataset and update the screen accordingly.

   ## Rule Status: 
   - Relevant for Modernization

   ## Algorithm:
   - **Step 1**: Check if the user pressed the PF7 key (Page Up).
     - If the PF7 key is pressed, proceed to the next step.
   - **Step 2**: Check if the user is already on the first page.
     - If the user is on the first page, display the error message: "NO PREVIOUS PAGES TO DISPLAY."
     - Exit the paging process.
   - **Step 3**: Fetch the previous set of records.
     - Use the `READPREV` CICS command to retrieve the previous record from the dataset (`CARDDAT`).
     - Decrement the screen counter (`WS-SCRN-COUNTER`) by 1 for each record fetched.
     - Populate the screen rows (`WS-ROW-CARD-NUM`, `WS-ROW-ACCTNO`, `WS-ROW-CARD-STATUS`) with the fetched data.
   - **Step 4**: Update the first page indicators.
     - If the first record is reached, set the flag `CA-FIRST-PAGE` to `TRUE`.
     - Store the first record's account ID and card number in `WS-CA-FIRST-CARD-ACCT-ID` and `WS-CA-FIRST-CARD-NUM`.
   - **Step 5**: End the browse operation.
     - Use the `ENDBR` CICS command to release resources associated with the browse operation.
   - **Mathematical Formula**: Decrement the screen counter:
     - `WS-SCRN-COUNTER = WS-SCRN-COUNTER - 1`
<!-- rule-end -->

<!-- rule-start -->
5. ## Rule / Function / Method - Record Filtering
   - **Detailed Description of the Rule**: This rule filters credit card records based on the account ID and card number provided by the user. Only records matching the filter criteria are displayed.
   - **What it proposes to do**: Ensure that the displayed records meet the user's search criteria.

   ## Rule Status: 
   - Relevant for Modernization

   ## Algorithm:
   - **Step 1**: Check if the account ID filter is valid (`FLG-ACCTFILTER-ISVALID`).
     - If valid, compare the account ID of the current record (`CARD-ACCT-ID`) with the user-provided account ID (`CC-ACCT-ID`).
     - If they do not match, set the flag `WS-EXCLUDE-THIS-RECORD` to `TRUE` and skip the record.
   - **Step 2**: Check if the card number filter is valid (`FLG-CARDFILTER-ISVALID`).
     - If valid, compare the card number of the current record (`CARD-NUM`) with the user-provided card number (`CC-CARD-NUM-N`).
     - If they do not match, set the flag `WS-EXCLUDE-THIS-RECORD` to `TRUE` and skip the record.
   - **Step 3**: If the record passes all filters:
     - Set the flag `WS-DONOT-EXCLUDE-THIS-RECORD` to `TRUE`.
     - Include the record in the screen rows (`WS-ROW-CARD-NUM`, `WS-ROW-ACCTNO`, `WS-ROW-CARD-STATUS`).
   - **Mathematical Formula**: No mathematical calculations are involved in this rule.
<!-- rule-end -->
## Data Structure

The data structure of the application is primarily defined in the `WORKING-STORAGE SECTION` and `LINKAGE SECTION`. It includes fields for managing user inputs, screen data, file records, and program context. Below is a detailed breakdown of the fields used in the application:

| Field ID       | Field Name                  | Data Type   | Field Size | Description                                                                 | Mandatory | Default Value | Additional Notes                                      | Relevant for Modernization | Information Type       |
|----------------|-----------------------------|-------------|------------|-----------------------------------------------------------------------------|-----------|--------------|------------------------------------------------------|-----------------------------|------------------------|
| `WS-RESP-CD`   | Response Code              | Numeric     | 9 digits   | Stores the response code from CICS operations.                              | Yes       | 0            | Used for error handling during CICS operations.      | Yes                         | System Response        |
| `WS-REAS-CD`   | Reason Code                | Numeric     | 9 digits   | Stores the reason code for CICS operations.                                 | Yes       | 0            | Provides additional context for response codes.      | Yes                         | System Response        |
| `WS-TRANID`    | Transaction ID             | Alphanumeric| 4 characters| Stores the transaction ID for the current operation.                        | Yes       | Spaces       | Used to identify the current transaction.            | Yes                         | Transaction Context    |
| `WS-INPUT-FLAG`| Input Flag                 | Alphanumeric| 1 character | Indicates whether the user input is valid or contains errors.               | Yes       | Spaces       | Flags include `INPUT-OK` and `INPUT-ERROR`.          | Yes                         | Validation Flag        |
| `WS-EDIT-ACCT-FLAG` | Account Filter Flag   | Alphanumeric| 1 character | Indicates the validity of the account filter provided by the user.          | Yes       | Spaces       | Flags include `FLG-ACCTFILTER-ISVALID` and others.   | Yes                         | Validation Flag        |
| `WS-EDIT-CARD-FLAG` | Card Filter Flag      | Alphanumeric| 1 character | Indicates the validity of the card filter provided by the user.             | Yes       | Spaces       | Flags include `FLG-CARDFILTER-ISVALID` and others.   | Yes                         | Validation Flag        |
| `WS-EDIT-SELECT-ARRAY` | Selection Array   | Alphanumeric| 7 characters| Stores user selections for viewing or updating records.                     | Yes       | Low-Values   | Each character corresponds to a record selection.    | Yes                         | User Interaction       |
| `CARD-ACCT-ID-X` | Account ID (Alphanumeric)| Alphanumeric| 11 characters| Stores the account ID for a credit card record.                             | Yes       | Spaces       | Used for filtering and displaying records.           | Yes                         | Record Data            |
| `CARD-ACCT-ID-N` | Account ID (Numeric)     | Numeric     | 11 digits   | Numeric representation of the account ID.                                   | Yes       | 0            | Used for numeric comparisons during filtering.       | Yes                         | Record Data            |
| `CARD-CVV-CD-X` | CVV Code (Alphanumeric)   | Alphanumeric| 3 characters | Stores the CVV code for a credit card record.                               | No        | Spaces       | Used for display purposes.                           | Yes                         | Record Data            |
| `CARD-CVV-CD-N` | CVV Code (Numeric)        | Numeric     | 3 digits    | Numeric representation of the CVV code.                                     | No        | 0            | Used for numeric comparisons during filtering.       | Yes                         | Record Data            |
| `WS-CA-LAST-CARD-NUM` | Last Card Number   | Alphanumeric| 16 characters| Stores the card number of the last record displayed on the screen.          | Yes       | Spaces       | Used for paging logic (Page Down).                   | Yes                         | Paging Context         |
| `WS-CA-FIRST-CARD-NUM` | First Card Number | Alphanumeric| 16 characters| Stores the card number of the first record displayed on the screen.         | Yes       | Spaces       | Used for paging logic (Page Up).                     | Yes                         | Paging Context         |
| `WS-CA-SCREEN-NUM` | Screen Number          | Numeric     | 1 digit     | Stores the current screen number for paging purposes.                       | Yes       | 0            | Incremented or decremented during paging operations. | Yes                         | Paging Context         |
| `WS-ROW-ACCTNO` | Row Account Number        | Alphanumeric| 11 characters| Stores the account number for each row displayed on the screen.             | Yes       | Spaces       | Populated during record retrieval.                   | Yes                         | Screen Data            |
| `WS-ROW-CARD-NUM` | Row Card Number        | Alphanumeric| 16 characters| Stores the card number for each row displayed on the screen.                | Yes       | Spaces       | Populated during record retrieval.                   | Yes                         | Screen Data            |
| `WS-ROW-CARD-STATUS` | Row Card Status     | Alphanumeric| 1 character  | Stores the status of the card (e.g., active/inactive) for each row.         | Yes       | Spaces       | Populated during record retrieval.                   | Yes                         | Screen Data            |
| `WS-COMMAREA`   | Communication Area        | Alphanumeric| 2000 characters| Stores data passed between programs and transactions.                       | Yes       | Spaces       | Includes user context, filters, and paging data.     | Yes                         | Communication Data     |
| `CARD-NUM`      | Card Number               | Alphanumeric| 16 characters| Stores the card number for a credit card record.                            | Yes       | Spaces       | Used for filtering and displaying records.           | Yes                         | Record Data            |
| `CARD-ACTIVE-STATUS` | Card Active Status  | Alphanumeric| 1 character  | Indicates whether the card is active or inactive.                           | Yes       | Spaces       | Used for display purposes.                           | Yes                         | Record Data            |
| `WS-ERROR-MSG`  | Error Message             | Alphanumeric| 75 characters | Stores error messages to be displayed to the user.                          | No        | Spaces       | Populated during validation or file operation errors.| Yes                         | User Feedback          |
| `WS-INFO-MSG`   | Informational Message     | Alphanumeric| 45 characters | Stores informational messages to guide the user.                            | No        | Spaces       | Populated during normal operations.                  | Yes                         | User Feedback          |

### Notes:
- **Mandatory Fields**: Fields marked as mandatory are essential for the program's operation and must be initialized or populated during execution.
- **Default Values**: Default values ensure that fields are in a known state before being used.
- **Relevant for Modernization**: Fields relevant for modernization should be reviewed for compatibility with modern data structures and systems.
- **Information Type**: Categorizes fields based on their purpose (e.g., validation, paging, record data, user feedback).
## Items Relevant for Modernization

In the context of modernization, the following functions and methods from the `COCRDLIC.cbl` program are most relevant. These items are critical for ensuring the program's functionality is preserved and adapted to modern systems, such as web-based applications, microservices, or cloud environments.

### 1. **Input Validation**
   - **Relevant Methods**:
     - Validation of Account ID (`2210-EDIT-ACCOUNT`)
     - Validation of Card Number (`2220-EDIT-CARD`)
   - **Relevance**:
     - Input validation ensures data integrity and prevents invalid data from being processed. These methods must be modernized to leverage modern validation frameworks or libraries.
     - For example, these validations can be implemented using regular expressions or integrated into web-based forms with real-time validation.

### 2. **Paging Logic**
   - **Relevant Methods**:
     - Page Down (`9000-READ-FORWARD`)
     - Page Up (`9100-READ-BACKWARDS`)
   - **Relevance**:
     - Paging logic is essential for handling large datasets efficiently. These methods should be modernized to work with modern database systems or APIs that support pagination.
     - For example, these methods can be replaced with database queries using `LIMIT` and `OFFSET` or API calls with pagination parameters.

### 3. **Record Filtering**
   - **Relevant Methods**:
     - Filtering Records (`9500-FILTER-RECORDS`)
   - **Relevance**:
     - Filtering ensures that only relevant records are displayed to the user. This functionality should be modernized to use database queries or API filters for improved performance and scalability.
     - For example, filtering can be implemented using SQL `WHERE` clauses or API query parameters.

### 4. **Screen Management**
   - **Relevant Methods**:
     - Screen Initialization (`1100-SCREEN-INIT`)
     - Screen Array Initialization (`1200-SCREEN-ARRAY-INIT`)
     - Screen Attribute Setup (`1300-SETUP-SCREEN-ATTRS`)
     - Message Setup (`1400-SETUP-MESSAGE`)
     - Sending Screen (`1500-SEND-SCREEN`)
   - **Relevance**:
     - These methods manage the user interface and display data to the user. Modernization should focus on replacing CICS screen handling with web-based or desktop UI frameworks.
     - For example, these methods can be adapted to render HTML pages or populate UI components in a modern application.

### 5. **COMMAREA Management**
   - **Relevant Methods**:
     - COMMAREA Initialization and Manipulation (`0000-MAIN`, `COMMON-RETURN`)
   - **Relevance**:
     - The COMMAREA is used for passing data between programs and transactions. Modernization should replace this mechanism with modern data exchange methods, such as JSON or XML payloads in REST APIs.
     - For example, the COMMAREA structure can be mapped to a JSON schema for seamless integration with web services.

### 6. **CICS File Operations**
   - **Relevant Methods**:
     - Start Browse (`STARTBR`)
     - Read Next (`READNEXT`)
     - Read Previous (`READPREV`)
     - End Browse (`ENDBR`)
   - **Relevance**:
     - These methods interact with datasets to retrieve and manipulate records. Modernization should replace these operations with database queries or API calls.
     - For example, `STARTBR` can be replaced with a database connection initialization, and `READNEXT`/`READPREV` can be replaced with SQL queries or API pagination.

### 7. **Error Handling**
   - **Relevant Methods**:
     - Error Message Setup (`1400-SETUP-MESSAGE`)
     - Error Handling in File Operations (`9000-READ-FORWARD`, `9100-READ-BACKWARDS`)
   - **Relevance**:
     - Error handling ensures the program responds gracefully to issues. Modernization should focus on integrating robust error-handling mechanisms, such as exception handling in modern programming languages.
     - For example, error messages can be logged using modern logging frameworks and displayed to users via web-based notifications.

### 8. **User Interaction**
   - **Relevant Methods**:
     - Handling PF Keys (`YYYY-STORE-PFKEY`)
     - User Actions (`EVALUATE TRUE` blocks for PF3, PF7, PF8, Enter)
   - **Relevance**:
     - User interaction logic should be modernized to work with modern UI frameworks and event-driven programming models.
     - For example, PF key handling can be replaced with button click events or keyboard shortcuts in web or desktop applications.

### 9. **Integration with External Programs**
   - **Relevant Methods**:
     - Transfer to Card Detail View (`COCRDSLC`)
     - Transfer to Card Update Program (`COCRDUPC`)
     - Return to Main Menu (`COMEN01C`)
   - **Relevance**:
     - Integration with external programs should be modernized to use service-oriented architectures, such as REST APIs or microservices.
     - For example, these transfers can be replaced with API calls or inter-service communication mechanisms.

### 10. **Dataset Structure**
   - **Relevant Methods**:
     - Dataset Interaction (`CARDDAT`, `CARDAIX`)
   - **Relevance**:
     - The dataset structure should be modernized to use relational or NoSQL databases for improved scalability and performance.
     - For example, the dataset can be migrated to a database with tables for credit card records and indexes for efficient querying.

### 11. **Debugging and Development**
   - **Relevant Methods**:
     - Send Plain Text (`SEND-PLAIN-TEXT`)
     - Send Long Text (`SEND-LONG-TEXT`)
   - **Relevance**:
     - Debugging methods should be modernized to use logging frameworks or debugging tools in modern programming environments.
     - For example, plain text messages can be replaced with structured logs or debugging output in development environments.

### Summary
The modernization of these items will ensure the program is compatible with modern systems and technologies, improving scalability, maintainability, and user experience. Each relevant function and method should be carefully analyzed and adapted to the target modernization platform.
## Items Irrelevant for Modernization

In the context of modernization, certain functions and methods in the `COCRDLIC.cbl` program are either redundant or tied to legacy systems that will be replaced entirely. These items can be ignored during the modernization process as they do not contribute to the core functionality or are superseded by modern technologies.

### 1. **PF Key Mapping**
   - **Irrelevant Methods**:
     - `YYYY-STORE-PFKEY`
   - **Reason for Irrelevance**:
     - PF key mapping is specific to legacy CICS systems and terminal-based user interfaces. Modern systems use event-driven programming models with buttons, keyboard shortcuts, or touch-based interactions, making this functionality obsolete.

### 2. **Plain Text Debugging**
   - **Irrelevant Methods**:
     - `SEND-PLAIN-TEXT`
   - **Reason for Irrelevance**:
     - This method is primarily used for debugging in legacy environments and is not suitable for production use. Modern debugging tools and logging frameworks provide more robust and structured ways to capture and analyze program behavior.

### 3. **Long Text Debugging**
   - **Irrelevant Methods**:
     - `SEND-LONG-TEXT`
   - **Reason for Irrelevance**:
     - Similar to `SEND-PLAIN-TEXT`, this method is used for debugging and should be replaced with modern logging mechanisms or debugging tools during modernization.

### 4. **Legacy Screen Attributes**
   - **Irrelevant Methods**:
     - `1250-SETUP-ARRAY-ATTRIBS`
   - **Reason for Irrelevance**:
     - This method sets screen attributes such as colors and protections for terminal-based interfaces. Modern UI frameworks handle these attributes dynamically and do not require manual setup.

### 5. **Low-Level CICS Commands**
   - **Irrelevant Methods**:
     - `EXEC CICS XCTL`
   - **Reason for Irrelevance**:
     - XCTL is used for transferring control between programs in CICS. Modern systems use service-oriented architectures, such as REST APIs or microservices, which eliminate the need for this command.

### 6. **Hardcoded Literals**
   - **Irrelevant Items**:
     - Hardcoded literals such as `LIT-THISTRANID`, `LIT-THISPGM`, `LIT-THISMAPSET`, and `LIT-THISMAP`.
   - **Reason for Irrelevance**:
     - These literals are specific to the legacy environment and are hardcoded for identification purposes. Modern systems use dynamic configurations or environment variables, making hardcoded literals unnecessary.

### 7. **Terminal-Specific Error Messages**
   - **Irrelevant Methods**:
     - Error messages tied to terminal-based interfaces, such as `WS-EXIT-MESSAGE` and `WS-INFORM-REC-ACTIONS`.
   - **Reason for Irrelevance**:
     - These messages are designed for legacy terminal screens and are not applicable in modern UI frameworks, which use dynamic notifications or alerts.

### 8. **Unused Flags**
   - **Irrelevant Items**:
     - Flags such as `WS-CONTEXT-FRESH-START` and `WS-CONTEXT-FRESH-START-NO`.
   - **Reason for Irrelevance**:
     - These flags are tied to legacy session management and are not required in modern systems that use stateless architectures or advanced session handling mechanisms.

### 9. **Legacy Screen Titles**
   - **Irrelevant Items**:
     - Screen titles defined in copybooks such as `COTTL01Y`.
   - **Reason for Irrelevance**:
     - These titles are specific to terminal-based interfaces and are not relevant for modern UI frameworks, which use dynamic and responsive layouts.

### 10. **Legacy Date and Time Handling**
   - **Irrelevant Methods**:
     - Date and time handling using `CSDAT01Y`.
   - **Reason for Irrelevance**:
     - Modern programming languages and frameworks provide built-in libraries for date and time manipulation, making this legacy approach redundant.

### 11. **Abend Variables**
   - **Irrelevant Items**:
     - Copybooks such as `CSMSG02Y` for handling abend variables.
   - **Reason for Irrelevance**:
     - Abend handling is specific to CICS and is not applicable in modern systems, which use structured exception handling mechanisms.

### 12. **Unused Screen Rows**
   - **Irrelevant Items**:
     - Screen rows that are not populated or used, such as `WS-ALL-ROWS`.
   - **Reason for Irrelevance**:
     - These rows are placeholders for legacy screen layouts and are not required in modern UI frameworks.

### Summary
These items are tied to legacy systems and terminal-based interfaces, which will be replaced entirely during modernization. Ignoring these items will streamline the modernization process and focus efforts on adapting core functionality to modern technologies.
