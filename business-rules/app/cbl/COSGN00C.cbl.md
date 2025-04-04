# COSGN00C.cbl: Signon Screen for CardDemo Application

## Overview
The `COSGN00C.cbl` file is a CICS COBOL program designed to handle the sign-on functionality for the CardDemo application. It provides a user interface for authentication, validates user credentials, and directs users to appropriate screens based on their roles. The program interacts with CICS resources, such as maps and datasets, to facilitate user authentication and navigation within the application.

The main purpose of this file is to ensure secure access to the CardDemo application by verifying user credentials against stored data and providing appropriate feedback or navigation based on the input. It contributes to the overall functionality of the system by serving as the entry point for users, ensuring they are authenticated before accessing other parts of the application.

<!-- general-rule-start -->
## Business Rules:
### 1. **Initialization and Error Handling**
   - The program begins by initializing key variables:
     - `WS-MESSAGE` is cleared to ensure no residual messages are displayed.
     - `ERR-FLG` is set to "OFF" to indicate no errors initially.
   - If the communication area (`EIBCALEN`) is empty, the program prepares the sign-on screen by:
     - Clearing the map (`COSGN0AO`) with low-values.
     - Setting the `USERIDL` field to `-1` to indicate no user ID is provided.
     - Displaying the sign-on screen using the `SEND-SIGNON-SCREEN` procedure.

### 2. **User Input Handling**
   - The program evaluates the user action (`EIBAID`) based on the key pressed:
     - **Enter Key (`DFHENTER`)**: Processes user input via the `PROCESS-ENTER-KEY` procedure.
     - **PF3 Key (`DFHPF3`)**: Displays a "Thank You" message using the `SEND-PLAIN-TEXT` procedure.
     - **Other Keys**: Displays an "Invalid Key" message and redisplays the sign-on screen.

### 3. **Processing Enter Key**
   - The program receives user input from the sign-on screen (`COSGN0A`) and validates the following:
     - **User ID (`USERIDI`)**:
       - If empty or contains low-values, an error message is displayed: "Please enter User ID ...".
       - The `USERIDL` field is set to `-1`, and the sign-on screen is redisplayed.
     - **Password (`PASSWDI`)**:
       - If empty or contains low-values, an error message is displayed: "Please enter Password ...".
       - The `PASSWDL` field is set to `-1`, and the sign-on screen is redisplayed.
   - If both fields are valid:
     - Converts the user ID and password to uppercase for consistency.
     - Stores the values in `WS-USER-ID` and `WS-USER-PWD`.
     - Proceeds to read the user security file (`READ-USER-SEC-FILE`) for authentication.

### 4. **Reading User Security File**
   - The program reads the `USRSEC` dataset to verify user credentials:
     - **Successful Read (`RESP-CD = 0`)**:
       - Compares the stored password (`SEC-USR-PWD`) with the entered password (`WS-USER-PWD`).
       - If passwords match:
         - Sets user context variables (`CDEMO-USER-ID`, `CDEMO-USER-TYPE`, etc.).
         - Navigates to the appropriate program based on user type:
           - **Admin User (`CDEMO-USRTYP-ADMIN`)**: Transfers control to `COADM01C`.
           - **Regular User**: Transfers control to `COMEN01C`.
       - If passwords do not match:
         - Displays an error message: "Wrong Password. Try again ...".
         - Redisplays the sign-on screen.
     - **User Not Found (`RESP-CD = 13`)**:
       - Displays an error message: "User not found. Try again ...".
       - Redisplays the sign-on screen.
     - **Other Errors**:
       - Displays a generic error message: "Unable to verify the User ...".
       - Redisplays the sign-on screen.

### 5. **Sending Sign-On Screen**
   - The `SEND-SIGNON-SCREEN` procedure:
     - Populates header information (e.g., current date, time, program name, transaction ID) using the `POPULATE-HEADER-INFO` procedure.
     - Sends the sign-on screen (`COSGN0A`) to the user with the current error message (`WS-MESSAGE`).

### 6. **Sending Plain Text**
   - The `SEND-PLAIN-TEXT` procedure:
     - Sends a plain text message (`WS-MESSAGE`) to the user.
     - Clears the screen and frees the keyboard for further input.

### 7. **Populating Header Information**
   - The `POPULATE-HEADER-INFO` procedure:
     - Retrieves the current date and time using system functions.
     - Formats the date and time into specific fields (`CURDATEO`, `CURTIMEO`) for display.
     - Assigns application and system IDs (`APPLIDO`, `SYSIDO`) using CICS commands.

### 8. **CICS Return**
   - After processing, the program returns control to CICS with:
     - The transaction ID (`WS-TRANID`).
     - The communication area (`CARDDEMO-COMMAREA`).
     - The length of the communication area.

### 9. **Error Flags and Messages**
   - The program uses the `ERR-FLG` variable to track errors:
     - `ERR-FLG-ON` indicates an error has occurred.
     - `ERR-FLG-OFF` indicates no errors.
   - Error messages are stored in `WS-MESSAGE` and displayed to the user.

### 10. **Dataset Interaction**
   - The program interacts with the `USRSEC` dataset to retrieve user security data:
     - Uses the user ID (`WS-USER-ID`) as the key for the read operation.
     - Validates the retrieved password against the entered password.

### 11. **Navigation Based on User Type**
   - Admin users are directed to the `COADM01C` program.
   - Regular users are directed to the `COMEN01C` program.
   - Navigation is performed using the CICS `XCTL` command.

### 12. **Field Definitions**
   - Key fields used in the program:
     - `USERIDI` and `PASSWDI`: Input fields for user ID and password.
     - `USERIDL` and `PASSWDL`: Length fields for user ID and password.
     - `SEC-USR-PWD`: Stored password in the `USRSEC` dataset.
     - `SEC-USR-TYPE`: User type (e.g., admin or regular user).
     - `WS-MESSAGE`: Error or informational messages displayed to the user.

### 13. **Error Codes**
   - The program handles specific error codes (`RESP-CD`):
     - `0`: Successful operation.
     - `13`: User not found.
     - Other codes: Generic error handling.

### 14. **CICS Commands**
   - The program uses various CICS commands for interaction:
     - `SEND`: To display screens or messages.
     - `RECEIVE`: To retrieve user input.
     - `READ`: To access the `USRSEC` dataset.
     - `ASSIGN`: To retrieve system and application IDs.
     - `XCTL`: To transfer control to other programs.

<!-- general-rule-end -->
## Dependencies

The `COSGN00C.cbl` program relies on several dependencies to function effectively within the CardDemo application. These dependencies include COBOL copybooks, CICS resources, datasets, and system functions. Each dependency plays a critical role in ensuring the program can authenticate users, interact with the system, and navigate between screens. Below is a detailed breakdown of the dependencies:

| Dependency       | Description                                                                 | Type               | Reference               | Relevance for Modernization                          |
|-------------------|-----------------------------------------------------------------------------|--------------------|-------------------------|-----------------------------------------------------|
| `COCOM01Y`       | A COBOL copybook that likely contains common constants or definitions used across the application. | COBOL Copybook     | File: `COCOM01Y`        | Provides reusable constants and definitions, which can be centralized in modern systems. |
| `COSGN00`        | A COBOL copybook that defines the mapset and screen layout for the sign-on screen (`COSGN0A`). | COBOL Copybook     | File: `COSGN00`         | Essential for screen rendering; can be replaced with modern UI frameworks. |
| `COTTL01Y`       | A COBOL copybook that likely contains title or header information for screens. | COBOL Copybook     | File: `COTTL01Y`        | Provides header data for screens; can be modernized into dynamic templates. |
| `CSDAT01Y`       | A COBOL copybook that may define date and time-related fields and formats. | COBOL Copybook     | File: `CSDAT01Y`        | Handles date/time formatting; can be replaced with modern libraries for date/time manipulation. |
| `CSMSG01Y`       | A COBOL copybook that likely contains predefined messages for user feedback. | COBOL Copybook     | File: `CSMSG01Y`        | Centralizes user messages; can be modernized into a message management system. |
| `CSUSR01Y`       | A COBOL copybook that defines user-related fields, such as user ID and password. | COBOL Copybook     | File: `CSUSR01Y`        | Critical for user authentication; can be integrated into modern authentication systems. |
| `DFHAID`         | A CICS copybook that defines terminal input keys (e.g., `DFHENTER`, `DFHPF3`). | CICS Copybook      | File: `DFHAID`          | Maps user input keys; can be replaced with modern event-driven input handling. |
| `DFHBMSCA`       | A CICS copybook that defines attributes for Basic Mapping Support (BMS) screens. | CICS Copybook      | File: `DFHBMSCA`        | Essential for screen attributes; can be replaced with modern UI frameworks. |
| `USRSEC`         | A dataset that stores user security information, including user IDs, passwords, and user types. | CICS Dataset       | Dataset: `USRSEC`       | Central to user authentication; can be migrated to modern databases or identity management systems. |
| `CARDDEMO-COMMAREA` | A communication area used to pass data between programs in the CardDemo application. | CICS Communication Area | Variable: `CARDDEMO-COMMAREA` | Facilitates inter-program communication; can be replaced with modern APIs or shared memory systems. |
| `COADM01C`       | A COBOL program that handles admin user functionality. | COBOL Program      | File: `COADM01C.cbl`    | Admin navigation; can be modernized into modular services. |
| `COMEN01C`       | A COBOL program that handles regular user functionality. | COBOL Program      | File: `COMEN01C.cbl`    | Regular user navigation; can be modernized into modular services. |
| `CURRENT-DATE`   | A system function used to retrieve the current date and time. | System Function    | COBOL intrinsic function | Provides date/time data; can be replaced with modern libraries for date/time manipulation. |
| `EIBAID`         | A CICS variable that captures the action identifier (e.g., key pressed by the user). | CICS Variable      | CICS Environment        | Tracks user actions; can be replaced with modern event-driven systems. |
| `EIBCALEN`       | A CICS variable that indicates the length of the communication area. | CICS Variable      | CICS Environment        | Determines communication area size; can be replaced with modern data-passing mechanisms. |

### General Explanation
The dependencies listed above are integral to the functionality of the `COSGN00C.cbl` program. They include COBOL copybooks for reusable definitions, CICS resources for screen management and user input handling, datasets for storing user security information, and system functions for date/time manipulation. These dependencies ensure the program can authenticate users, display screens, and navigate between different parts of the application.

### Relevance for Modernization
Modernizing these dependencies involves replacing legacy COBOL copybooks and CICS resources with contemporary technologies such as REST APIs, modern databases, and web-based UI frameworks. For example:
- **COBOL Copybooks**: Can be replaced with centralized configuration files or database tables.
- **CICS Resources**: Can be replaced with event-driven architectures and modern input handling systems.
- **Datasets**: Can be migrated to relational or NoSQL databases with enhanced security features.
- **System Functions**: Can be replaced with modern libraries for date/time manipulation.

By addressing these dependencies, the program can be transformed into a modular, scalable, and maintainable system that aligns with current technology standards.
## Detailed Rules

<!-- rule-start -->
### 1. Rule / Function / Method - MAIN-PARA:
- **Detailed Description of the Rule**:  
  The `MAIN-PARA` procedure is the entry point of the program. It initializes key variables, handles user input, and determines the flow of execution based on the communication area (`EIBCALEN`) and user actions (`EIBAID`). It ensures that the sign-on screen is displayed and processes user authentication.

- **What it proposes to do**:  
  - Initialize error flags and messages.
  - Display the sign-on screen if no communication area is provided.
  - Process user input based on the key pressed (e.g., Enter, PF3, or other keys).
  - Return control to CICS after processing.

- **Rule Status**:  
  Relevant for Modernization.

- **Algorithm**:
  1. **Initialization**:
     - Set the error flag (`ERR-FLG`) to "OFF" to indicate no errors.
     - Clear the message field (`WS-MESSAGE`) and the error message output field (`ERRMSGO` of `COSGN0AO`).
  2. **Communication Area Check**:
     - If `EIBCALEN` equals 0:
       - Clear the map (`COSGN0AO`) by moving low-values to it.
       - Set the `USERIDL` field of `COSGN0AI` to `-1` to indicate no user ID is provided.
       - Call the `SEND-SIGNON-SCREEN` procedure to display the sign-on screen.
     - Otherwise:
       - Evaluate the action identifier (`EIBAID`):
         - **Enter Key (`DFHENTER`)**: Call the `PROCESS-ENTER-KEY` procedure to process user input.
         - **PF3 Key (`DFHPF3`)**: Move the "Thank You" message (`CCDA-MSG-THANK-YOU`) to `WS-MESSAGE` and call the `SEND-PLAIN-TEXT` procedure.
         - **Other Keys**: Set the error flag (`ERR-FLG`) to "ON", move the "Invalid Key" message (`CCDA-MSG-INVALID-KEY`) to `WS-MESSAGE`, and call the `SEND-SIGNON-SCREEN` procedure.
  3. **Return to CICS**:
     - Execute the CICS `RETURN` command with the following parameters:
       - Transaction ID (`WS-TRANID`).
       - Communication area (`CARDDEMO-COMMAREA`).
       - Length of the communication area (`LENGTH OF CARDDEMO-COMMAREA`).

<!-- rule-end -->

<!-- rule-start -->
### 2. Rule / Function / Method - PROCESS-ENTER-KEY:
- **Detailed Description of the Rule**:  
  The `PROCESS-ENTER-KEY` procedure handles user input from the sign-on screen. It validates the user ID and password fields, converts them to uppercase, and initiates the user authentication process by reading the security file.

- **What it proposes to do**:  
  - Validate user input for the user ID and password fields.
  - Convert user input to uppercase for consistency.
  - Authenticate the user by reading the security file.

- **Rule Status**:  
  Relevant for Modernization.

- **Algorithm**:
  1. **Receive User Input**:
     - Execute the CICS `RECEIVE` command to retrieve data from the sign-on screen (`COSGN0A`) into the input map (`COSGN0AI`).
     - Capture response codes (`RESP` and `RESP2`) for error handling.
  2. **Validation**:
     - Evaluate the user ID (`USERIDI` of `COSGN0AI`):
       - If the field is empty or contains low-values:
         - Set the error flag (`ERR-FLG`) to "ON".
         - Move the message "Please enter User ID ..." to `WS-MESSAGE`.
         - Set the `USERIDL` field to `-1`.
         - Call the `SEND-SIGNON-SCREEN` procedure to redisplay the sign-on screen.
     - Evaluate the password (`PASSWDI` of `COSGN0AI`):
       - If the field is empty or contains low-values:
         - Set the error flag (`ERR-FLG`) to "ON".
         - Move the message "Please enter Password ..." to `WS-MESSAGE`.
         - Set the `PASSWDL` field to `-1`.
         - Call the `SEND-SIGNON-SCREEN` procedure to redisplay the sign-on screen.
  3. **Convert Input to Uppercase**:
     - Use the `UPPER-CASE` function to convert the user ID (`USERIDI`) and password (`PASSWDI`) to uppercase.
     - Store the converted values in `WS-USER-ID` and `WS-USER-PWD`.
  4. **Authenticate User**:
     - If the error flag (`ERR-FLG`) is "OFF", call the `READ-USER-SEC-FILE` procedure to authenticate the user.

<!-- rule-end -->

<!-- rule-start -->
### 3. Rule / Function / Method - READ-USER-SEC-FILE:
- **Detailed Description of the Rule**:  
  The `READ-USER-SEC-FILE` procedure reads the `USRSEC` dataset to verify user credentials. It checks if the user ID exists and validates the password. Based on the user type, it navigates to the appropriate program.

- **What it proposes to do**:  
  - Verify the existence of the user ID in the `USRSEC` dataset.
  - Validate the password against the stored password.
  - Navigate to the appropriate program based on user type.

- **Rule Status**:  
  Relevant for Modernization.

- **Algorithm**:
  1. **Read Dataset**:
     - Execute the CICS `READ` command to retrieve user security data (`SEC-USER-DATA`) from the `USRSEC` dataset.
     - Use the user ID (`WS-USER-ID`) as the key for the read operation.
     - Capture response codes (`RESP` and `RESP2`) for error handling.
  2. **Evaluate Response Code**:
     - **Successful Read (`RESP-CD = 0`)**:
       - Compare the stored password (`SEC-USR-PWD`) with the entered password (`WS-USER-PWD`):
         - If passwords match:
           - Set user context variables (`CDEMO-USER-ID`, `CDEMO-USER-TYPE`, etc.).
           - Navigate to the appropriate program:
             - **Admin User (`CDEMO-USRTYP-ADMIN`)**: Execute the CICS `XCTL` command to transfer control to `COADM01C`.
             - **Regular User**: Execute the CICS `XCTL` command to transfer control to `COMEN01C`.
         - If passwords do not match:
           - Move the message "Wrong Password. Try again ..." to `WS-MESSAGE`.
           - Set the `PASSWDL` field to `-1`.
           - Call the `SEND-SIGNON-SCREEN` procedure to redisplay the sign-on screen.
     - **User Not Found (`RESP-CD = 13`)**:
       - Set the error flag (`ERR-FLG`) to "ON".
       - Move the message "User not found. Try again ..." to `WS-MESSAGE`.
       - Set the `USERIDL` field to `-1`.
       - Call the `SEND-SIGNON-SCREEN` procedure to redisplay the sign-on screen.
     - **Other Errors**:
       - Set the error flag (`ERR-FLG`) to "ON".
       - Move the message "Unable to verify the User ..." to `WS-MESSAGE`.
       - Set the `USERIDL` field to `-1`.
       - Call the `SEND-SIGNON-SCREEN` procedure to redisplay the sign-on screen.

<!-- rule-end -->

<!-- rule-start -->
### 4. Rule / Function / Method - SEND-SIGNON-SCREEN:
- **Detailed Description of the Rule**:  
  The `SEND-SIGNON-SCREEN` procedure displays the sign-on screen (`COSGN0A`) to the user. It populates header information and includes any error messages.

- **What it proposes to do**:  
  - Populate header information for the sign-on screen.
  - Display the sign-on screen with the current error message.

- **Rule Status**:  
  Relevant for Modernization.

- **Algorithm**:
  1. **Populate Header Information**:
     - Call the `POPULATE-HEADER-INFO` procedure to populate header fields (e.g., current date, time, program name, transaction ID).
  2. **Display Screen**:
     - Move the error message (`WS-MESSAGE`) to the error message output field (`ERRMSGO` of `COSGN0AO`).
     - Execute the CICS `SEND` command to display the sign-on screen (`COSGN0A`) with the following options:
       - Erase the screen.
       - Position the cursor for user input.

<!-- rule-end -->

<!-- rule-start -->
### 5. Rule / Function / Method - POPULATE-HEADER-INFO:
- **Detailed Description of the Rule**:  
  The `POPULATE-HEADER-INFO` procedure populates header fields for the sign-on screen, including the current date, time, program name, transaction ID, application ID, and system ID.

- **What it proposes to do**:  
  - Retrieve and format the current date and time.
  - Populate header fields with system and application information.

- **Rule Status**:  
  Relevant for Modernization.

- **Algorithm**:
  1. **Retrieve Current Date and Time**:
     - Use the `CURRENT-DATE` function to retrieve the current date and time.
     - Extract and format the date into month (`MM`), day (`DD`), and year (`YY`) components.
     - Extract and format the time into hours (`HH`), minutes (`MM`), and seconds (`SS`) components.
  2. **Populate Header Fields**:
     - Move the formatted date (`MM-DD-YY`) to the `CURDATEO` field of `COSGN0AO`.
     - Move the formatted time (`HH-MM-SS`) to the `CURTIMEO` field of `COSGN0AO`.
     - Assign the application ID (`APPLIDO`) and system ID (`SYSIDO`) using CICS `ASSIGN` commands.
     - Populate other header fields, such as program name (`PGMNAMEO`) and transaction ID (`TRNNAMEO`).

<!-- rule-end -->
## Data Structure

The data structure of the `COSGN00C.cbl` program is primarily defined in the `WORKING-STORAGE SECTION` and the `LINKAGE SECTION`. It includes fields for user authentication, error handling, screen rendering, and system information. Below is a detailed breakdown of the fields used in the program:

| Field ID       | Field Name         | Data Type   | Field Size | Description                                                                 | Mandatory | Default Value | Additional Notes                                      | Relevant for Modernization | Information Type       |
|-----------------|--------------------|-------------|------------|-----------------------------------------------------------------------------|-----------|---------------|------------------------------------------------------|-----------------------------|------------------------|
| WS-PGMNAME      | Program Name       | Alphanumeric | 8          | Name of the program (`COSGN00C`).                                           | Yes       | `'COSGN00C'`  | Used for header information on screens.              | Yes                         | System Metadata        |
| WS-TRANID       | Transaction ID     | Alphanumeric | 4          | Transaction ID for the program (`CC00`).                                    | Yes       | `'CC00'`      | Used for CICS transaction management.                | Yes                         | System Metadata        |
| WS-MESSAGE      | Message Field      | Alphanumeric | 80         | Field to store error or informational messages displayed to the user.       | No        | Spaces        | Populated dynamically based on program flow.         | Yes                         | User Feedback          |
| WS-USRSEC-FILE  | User Security File | Alphanumeric | 8          | Name of the dataset used for user authentication (`USRSEC`).                | Yes       | `'USRSEC  '`  | Critical for user authentication.                    | Yes                         | Dataset Reference      |
| WS-ERR-FLG      | Error Flag         | Alphanumeric | 1          | Flag to indicate whether an error has occurred (`Y` or `N`).                | Yes       | `'N'`         | Controlled using condition names (`ERR-FLG-ON/OFF`). | Yes                         | Error Handling         |
| WS-RESP-CD      | Response Code      | Signed Numeric | 9 (COMP)   | Response code from CICS commands.                                           | No        | Zeros         | Used for error handling in CICS operations.          | Yes                         | System Feedback        |
| WS-REAS-CD      | Reason Code        | Signed Numeric | 9 (COMP)   | Secondary response code from CICS commands.                                 | No        | Zeros         | Used for detailed error handling.                    | Yes                         | System Feedback        |
| WS-USER-ID      | User ID            | Alphanumeric | 8          | Field to store the user ID entered by the user.                             | Yes       | Spaces        | Converted to uppercase for consistency.              | Yes                         | User Input             |
| WS-USER-PWD     | User Password      | Alphanumeric | 8          | Field to store the password entered by the user.                            | Yes       | Spaces        | Converted to uppercase for consistency.              | Yes                         | User Input             |
| ERRMSGO         | Error Message Output | Alphanumeric | 80         | Field in the output map (`COSGN0AO`) to display error messages.             | No        | Spaces        | Populated dynamically based on program flow.         | Yes                         | User Feedback          |
| USERIDI         | User ID Input      | Alphanumeric | 8          | Field in the input map (`COSGN0AI`) for user ID entered by the user.         | Yes       | Spaces        | Validated for presence and converted to uppercase.   | Yes                         | User Input             |
| PASSWDI         | Password Input     | Alphanumeric | 8          | Field in the input map (`COSGN0AI`) for password entered by the user.        | Yes       | Spaces        | Validated for presence and converted to uppercase.   | Yes                         | User Input             |
| USERIDL         | User ID Length     | Signed Numeric | 9 (COMP)   | Field in the input map (`COSGN0AI`) to store the length of the user ID.      | No        | `-1`          | Used for validation and error handling.              | Yes                         | User Input Metadata    |
| PASSWDL         | Password Length    | Signed Numeric | 9 (COMP)   | Field in the input map (`COSGN0AI`) to store the length of the password.     | No        | `-1`          | Used for validation and error handling.              | Yes                         | User Input Metadata    |
| SEC-USR-PWD     | Stored Password    | Alphanumeric | 8          | Field in the `USRSEC` dataset to store the user's password.                  | Yes       | Spaces        | Compared with the entered password for authentication. | Yes                         | Dataset Field          |
| SEC-USR-TYPE    | User Type          | Alphanumeric | 1          | Field in the `USRSEC` dataset to store the user's type (e.g., admin or regular user). | Yes       | Spaces        | Determines navigation to admin or regular user programs. | Yes                         | Dataset Field          |
| APPLIDO         | Application ID     | Alphanumeric | Variable   | Field to store the application ID assigned by CICS.                         | No        | Spaces        | Populated dynamically using CICS `ASSIGN`.           | Yes                         | System Metadata        |
| SYSIDO          | System ID          | Alphanumeric | Variable   | Field to store the system ID assigned by CICS.                              | No        | Spaces        | Populated dynamically using CICS `ASSIGN`.           | Yes                         | System Metadata        |
| CURDATEO        | Current Date Output | Alphanumeric | Variable   | Field in the output map (`COSGN0AO`) to display the current date.            | No        | Spaces        | Populated dynamically using the `CURRENT-DATE` function. | Yes                         | Date Metadata          |
| CURTIMEO        | Current Time Output | Alphanumeric | Variable   | Field in the output map (`COSGN0AO`) to display the current time.            | No        | Spaces        | Populated dynamically using the `CURRENT-DATE` function. | Yes                         | Time Metadata          |

### General Notes:
- **Mandatory Fields**: Fields such as `WS-USER-ID`, `WS-USER-PWD`, and `SEC-USR-PWD` are mandatory for user authentication.
- **Default Values**: Many fields are initialized with spaces or zeros to ensure clean data handling.
- **Relevant for Modernization**: Fields related to user input, error handling, and dataset interaction are critical for modernization. They can be replaced with modern authentication systems, dynamic error handling frameworks, and relational databases.
- **Information Type**: Fields are categorized into system metadata, user input, user feedback, dataset fields, and error handling to streamline modernization efforts.
## Items Relevant for Modernization

In the context of modernization, several functions and methods within the `COSGN00C.cbl` program are critical for transitioning to a more contemporary architecture. These items are essential for ensuring the program's functionality is preserved while leveraging modern technologies, frameworks, and best practices. Below is a detailed list of the most relevant items for modernization:

### 1. **MAIN-PARA**
- **Relevance**:  
  The `MAIN-PARA` procedure serves as the entry point for the program and controls the overall flow of execution. It handles initialization, user input processing, and navigation based on user actions. Modernization efforts should focus on replacing this centralized control structure with modular, event-driven workflows.
- **Modernization Focus**:  
  - Replace COBOL procedural logic with modular service-oriented architecture.
  - Implement event-driven systems to handle user actions dynamically.
  - Use modern frameworks for screen rendering and user input validation.

### 2. **PROCESS-ENTER-KEY**
- **Relevance**:  
  This procedure validates user input, converts data to uppercase, and initiates the authentication process. It is critical for ensuring secure and accurate user authentication.
- **Modernization Focus**:  
  - Replace COBOL validation logic with modern input validation libraries.
  - Use secure hashing or encryption for password handling instead of plain text.
  - Implement API-based authentication systems for scalability and security.

### 3. **READ-USER-SEC-FILE**
- **Relevance**:  
  The `READ-USER-SEC-FILE` procedure interacts with the `USRSEC` dataset to verify user credentials. It is central to the authentication process and determines user navigation based on their type.
- **Modernization Focus**:  
  - Migrate the `USRSEC` dataset to a relational or NoSQL database with enhanced security features.
  - Replace COBOL file handling with database queries using modern ORM (Object-Relational Mapping) tools.
  - Implement role-based access control (RBAC) for user type management.

### 4. **SEND-SIGNON-SCREEN**
- **Relevance**:  
  This procedure displays the sign-on screen to the user, including error messages and header information. It is essential for user interaction and feedback.
- **Modernization Focus**:  
  - Replace COBOL screen rendering with web-based UI frameworks (e.g., React, Angular).
  - Use dynamic templates for error messages and header information.
  - Implement responsive design for better user experience across devices.

### 5. **SEND-PLAIN-TEXT**
- **Relevance**:  
  The `SEND-PLAIN-TEXT` procedure sends plain text messages to the user. It is used for simple feedback, such as "Thank You" messages.
- **Modernization Focus**:  
  - Replace plain text messaging with modern notification systems (e.g., toast notifications, modal dialogs).
  - Use localization and internationalization frameworks for multi-language support.

### 6. **POPULATE-HEADER-INFO**
- **Relevance**:  
  This procedure populates header fields with system and application information, including the current date and time. It ensures the sign-on screen displays accurate metadata.
- **Modernization Focus**:  
  - Replace COBOL date/time handling with modern libraries (e.g., Moment.js, Java's `LocalDateTime`).
  - Use dynamic templates for header information.
  - Implement centralized metadata management for system and application details.

### 7. **Error Handling (`ERR-FLG` and Messages)**
- **Relevance**:  
  The program uses the `ERR-FLG` variable and `WS-MESSAGE` field to track and display errors. This mechanism is critical for user feedback and debugging.
- **Modernization Focus**:  
  - Replace flag-based error tracking with structured exception handling.
  - Use centralized logging frameworks for error tracking and debugging.
  - Implement user-friendly error messages with detailed explanations.

### 8. **Dataset Interaction (`USRSEC`)**
- **Relevance**:  
  The `USRSEC` dataset stores user credentials and types. It is the backbone of the authentication process.
- **Modernization Focus**:  
  - Migrate the dataset to a modern database with encryption and access control.
  - Replace COBOL file handling with database queries.
  - Implement secure authentication protocols (e.g., OAuth, JWT).

### 9. **Navigation (`XCTL` Commands)**
- **Relevance**:  
  The program uses `XCTL` commands to navigate to other programs (`COADM01C` for admin users and `COMEN01C` for regular users). This navigation is critical for directing users to the appropriate functionality.
- **Modernization Focus**:  
  - Replace program-based navigation with modular microservices.
  - Use API-based routing for dynamic navigation.
  - Implement role-based dashboards for user-specific functionality.

### 10. **System Metadata (`APPLIDO`, `SYSIDO`)**
- **Relevance**:  
  The program retrieves application and system IDs using CICS `ASSIGN` commands. These metadata fields are essential for system identification and tracking.
- **Modernization Focus**:  
  - Replace CICS metadata handling with centralized configuration management systems.
  - Use environment variables or configuration files for system metadata.
  - Implement dynamic metadata retrieval for scalability.

### 11. **Date and Time Handling (`CURRENT-DATE`)**
- **Relevance**:  
  The program uses the `CURRENT-DATE` function to retrieve and format the current date and time. This information is displayed on the sign-on screen.
- **Modernization Focus**:  
  - Replace COBOL date/time handling with modern libraries (e.g., Moment.js, Java's `LocalDateTime`).
  - Implement timezone-aware date/time handling for global applications.
  - Use dynamic templates for date/time display.

### Summary
The modernization of these items will involve transitioning from COBOL-based procedural logic and CICS-specific commands to modular, API-driven architectures, modern databases, and web-based UI frameworks. This will enhance scalability, security, and user experience while aligning the application with contemporary technology standards.
## Items Irrelevant for Modernization

In the context of modernization, certain functions and methods within the `COSGN00C.cbl` program are either redundant or can be replaced entirely by modern frameworks and tools. These items are considered irrelevant for modernization because their functionality is either outdated or can be handled more efficiently by contemporary technologies. Below is a detailed list of the items that can be ignored during the modernization process:

### 1. **DFHAID Copybook**
- **Reason for Irrelevance**:  
  The `DFHAID` copybook defines terminal input keys (e.g., `DFHENTER`, `DFHPF3`). In modern systems, user input handling is typically event-driven and managed by frameworks such as JavaScript, React, or Angular.
- **Why It Can Be Ignored**:  
  - Modern UI frameworks handle user input dynamically without relying on predefined terminal keys.
  - Event-driven architectures eliminate the need for static key definitions.

### 2. **DFHBMSCA Copybook**
- **Reason for Irrelevance**:  
  The `DFHBMSCA` copybook defines attributes for Basic Mapping Support (BMS) screens. Modern systems use web-based UI frameworks that do not require BMS attributes.
- **Why It Can Be Ignored**:  
  - Screen attributes are dynamically managed in modern UI frameworks.
  - HTML, CSS, and JavaScript provide more flexibility and customization for screen rendering.

### 3. **Low-Values Initialization**
- **Reason for Irrelevance**:  
  The program uses `LOW-VALUES` to clear fields in the map (`COSGN0AO`). This approach is specific to COBOL and CICS and is unnecessary in modern systems.
- **Why It Can Be Ignored**:  
  - Modern systems use default values or null values to initialize fields.
  - Clearing fields dynamically is handled by frameworks or libraries.

### 4. **CICS ERASE Option**
- **Reason for Irrelevance**:  
  The `ERASE` option in CICS commands is used to clear the screen before displaying new content. Modern systems use dynamic rendering techniques that automatically replace old content.
- **Why It Can Be Ignored**:  
  - Web-based applications dynamically update the DOM without requiring explicit screen clearing.
  - Frameworks like React and Angular handle content updates efficiently.

### 5. **CICS FREEKB Option**
- **Reason for Irrelevance**:  
  The `FREEKB` option in CICS commands is used to free the keyboard for user input. This functionality is redundant in modern systems where input handling is event-driven.
- **Why It Can Be Ignored**:  
  - Modern systems automatically enable user input without requiring explicit commands.
  - Event listeners handle input dynamically.

### 6. **88-Level Condition Names (`ERR-FLG-ON` and `ERR-FLG-OFF`)**
- **Reason for Irrelevance**:  
  The program uses 88-level condition names to manage the error flag (`ERR-FLG`). This COBOL-specific feature is unnecessary in modern systems.
- **Why It Can Be Ignored**:  
  - Modern systems use boolean variables or enums for error tracking.
  - Error handling frameworks provide structured exception management.

### 7. **CICS ASSIGN Commands**
- **Reason for Irrelevance**:  
  The program uses CICS `ASSIGN` commands to retrieve application and system IDs (`APPLIDO` and `SYSIDO`). These IDs are typically managed by configuration files or environment variables in modern systems.
- **Why It Can Be Ignored**:  
  - Configuration management tools (e.g., Kubernetes, Docker) handle system metadata dynamically.
  - Environment variables provide a more flexible and scalable approach.

### 8. **Static Field Sizes**
- **Reason for Irrelevance**:  
  Many fields in the program have fixed sizes (e.g., `PIC X(08)` for user ID and password). Modern systems use dynamic data structures that do not require fixed sizes.
- **Why It Can Be Ignored**:  
  - Dynamic data structures (e.g., JSON, objects) allow for variable-length fields.
  - Fixed sizes are restrictive and unnecessary in modern applications.

### 9. **CICS RETURN Command**
- **Reason for Irrelevance**:  
  The `RETURN` command is used to return control to CICS with transaction and communication area details. Modern systems use APIs or service calls for inter-program communication.
- **Why It Can Be Ignored**:  
  - RESTful APIs or microservices handle communication dynamically.
  - Transaction management is integrated into modern frameworks.

### 10. **Static Screen Layouts**
- **Reason for Irrelevance**:  
  The program uses predefined screen layouts (`COSGN0A`) for the sign-on screen. Modern systems use responsive and dynamic layouts that adapt to different devices and user preferences.
- **Why It Can Be Ignored**:  
  - Modern UI frameworks provide responsive design capabilities.
  - Static layouts are replaced by dynamic templates.

### Summary
These items are irrelevant for modernization because their functionality is either outdated or can be replaced by more efficient and scalable solutions. Ignoring these items allows modernization efforts to focus on critical components, such as authentication, user input validation, and database interaction, while leveraging contemporary technologies and best practices.
