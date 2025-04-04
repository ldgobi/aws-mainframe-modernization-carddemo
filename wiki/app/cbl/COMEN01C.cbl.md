# Documentation for `COMEN01C.CBL`

## Metadata
| **File Name** | **Application** | **Type** | **Function** |
|---------------|-----------------|----------|--------------|
| COMEN01C.CBL  | CardDemo        | CICS COBOL Program | Main Menu for Regular Users |

## Overview
The `COMEN01C.CBL` program is a CICS COBOL application designed to provide a main menu interface for regular users in the CardDemo application. It handles user interactions, menu navigation, and transitions between screens. The program is licensed under the Apache License, Version 2.0.

---

## Sections

### Identification Division
Defines the program name (`COMEN01C`) and author (`AWS`).

### Environment Division
Specifies the configuration settings for the program.

### Data Division

#### Working-Storage Section
Contains variables used for program execution. Key variables include:
| **Variable Name** | **Type** | **Purpose** |
|--------------------|----------|-------------|
| `WS-PGMNAME`       | PIC X(08) | Stores the program name (`COMEN01C`). |
| `WS-TRANID`        | PIC X(04) | Transaction ID (`CM00`). |
| `WS-MESSAGE`       | PIC X(80) | Holds error or informational messages. |
| `WS-USRSEC-FILE`   | PIC X(08) | User security file name (`USRSEC`). |
| `WS-ERR-FLG`       | PIC X(01) | Error flag (`Y` for error, `N` for no error). |
| `WS-RESP-CD`       | PIC S9(09) COMP | Response code for CICS operations. |
| `WS-OPTION`        | PIC 9(02) | Stores the selected menu option. |
| `WS-MENU-OPT-TXT`  | PIC X(40) | Text for menu options. |

#### Linkage Section
Defines the `DFHCOMMAREA` structure for passing data between programs. It uses dynamic sizing based on `EIBCALEN`.

---

### Procedure Division

#### Main Logic (`MAIN-PARA`)
Handles the main flow of the program:
1. Initializes error flags and messages.
2. Checks if the program is being re-entered (`EIBCALEN`).
3. Sends or receives the menu screen based on user input.
4. Evaluates user actions (e.g., pressing Enter, PF3, or invalid keys).
5. Returns control to the CICS transaction.

#### Subroutines
| **Subroutine Name**       | **Purpose** |
|----------------------------|-------------|
| `PROCESS-ENTER-KEY`        | Validates and processes the user's menu selection. Handles numeric validation and access control for admin-only options. |
| `RETURN-TO-SIGNON-SCREEN`  | Redirects the user to the sign-on screen. |
| `SEND-MENU-SCREEN`         | Sends the menu screen to the user. Populates header information and builds menu options. |
| `RECEIVE-MENU-SCREEN`      | Receives user input from the menu screen. |
| `POPULATE-HEADER-INFO`     | Populates header information such as current date, time, transaction name, and program name. |
| `BUILD-MENU-OPTIONS`       | Dynamically builds menu options based on available choices. |

---

## Insights

### Key Features
- **Dynamic Menu Handling**: The program dynamically builds menu options based on user type and available choices.
- **Error Management**: Implements error flags and messages to handle invalid inputs and restricted access.
- **CICS Integration**: Utilizes CICS commands (`EXEC CICS`) for screen handling and program transitions.
- **User Type Validation**: Ensures that admin-only options are restricted for regular users.

### Dependencies
The program includes several copybooks:
| **Copybook Name** | **Purpose** |
|--------------------|-------------|
| `COCOM01Y`         | Common definitions. |
| `COMEN02Y`         | Menu-related definitions. |
| `COMEN01`          | Screen map definitions. |
| `COTTL01Y`         | Title-related definitions. |
| `CSDAT01Y`         | Date-related definitions. |
| `CSMSG01Y`         | Message-related definitions. |
| `CSUSR01Y`         | User-related definitions. |
| `DFHAID`           | CICS attention identifier definitions. |
| `DFHBMSCA`         | CICS basic mapping support definitions. |

### Error Handling
- Invalid menu options trigger error messages and prevent further processing.
- Admin-only options are restricted for regular users, with appropriate feedback provided.

### User Interaction
- The program supports navigation using Enter and PF3 keys.
- Displays dynamic menu options and provides feedback for invalid inputs.

---

## License
The program is licensed under the Apache License, Version 2.0. For details, visit [http://www.apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0).
