# Documentation for `COADM01C.cbl`

## Metadata
| **File Name** | **Application** | **Type** | **Function** |
|---------------|-----------------|----------|--------------|
| `COADM01C.cbl` | CardDemo | CICS COBOL Program | Admin Menu for Admin users |

## Overview
The `COADM01C.cbl` program is a CICS COBOL application designed to provide an administrative menu for admin users in the CardDemo application. It handles user interactions, menu navigation, and program transitions based on user input. The program is licensed under the Apache License, Version 2.0.

---

## Sections

### Identification Division
Defines the program name (`COADM01C`) and author (`AWS`).

### Environment Division
Specifies the configuration for the program's environment.

### Data Division

#### Working-Storage Section
Contains variables used throughout the program. Key variables include:
| **Variable Name**       | **Type**       | **Purpose**                                                                 |
|--------------------------|----------------|-----------------------------------------------------------------------------|
| `WS-PGMNAME`            | PIC X(08)      | Stores the program name (`COADM01C`).                                       |
| `WS-TRANID`             | PIC X(04)      | Stores the transaction ID (`CA00`).                                         |
| `WS-MESSAGE`            | PIC X(80)      | Stores error or informational messages.                                     |
| `WS-USRSEC-FILE`        | PIC X(08)      | Stores the user security file name (`USRSEC`).                              |
| `WS-ERR-FLG`            | PIC X(01)      | Error flag (`Y` for error, `N` for no error).                               |
| `WS-RESP-CD`            | PIC S9(09) COMP| Stores response code from CICS operations.                                  |
| `WS-OPTION`             | PIC 9(02)      | Stores the menu option selected by the user.                                |
| `WS-ADMIN-OPT-TXT`      | PIC X(40)      | Stores text for menu options.                                               |

#### Linkage Section
Defines the `DFHCOMMAREA` structure for passing data between programs. It uses dynamic sizing based on `EIBCALEN`.

---

### Procedure Division
The main logic of the program is implemented in this section. It includes multiple paragraphs for handling specific tasks.

#### Main-PARA
Handles the initial program flow:
- Resets error flags and clears messages.
- Checks if `EIBCALEN` is zero to determine if the program is being entered for the first time.
- Sends or receives the menu screen based on user input.
- Evaluates user actions (e.g., pressing `ENTER`, `PF3`, or invalid keys).

#### Process-Enter-Key
Processes the user's menu selection:
- Validates the input option.
- Executes the corresponding program if the option is valid.
- Displays a "coming soon" message for unimplemented options.

#### Return-to-Signon-Screen
Transitions the user back to the sign-on screen.

#### Send-Menu-Screen
Sends the menu screen to the user:
- Populates header information.
- Builds menu options dynamically.
- Sends the screen using CICS `SEND` command.

#### Receive-Menu-Screen
Receives user input from the menu screen using the CICS `RECEIVE` command.

#### Populate-Header-Info
Populates header information for the menu screen, including:
- Current date and time.
- Program and transaction identifiers.

#### Build-Menu-Options
Dynamically builds menu options based on the number of available options (`CDEMO-ADMIN-OPT-COUNT`).

---

## Insights

### Key Features
- **Dynamic Menu Handling**: The program dynamically builds menu options based on the number of available options, ensuring flexibility.
- **Error Handling**: Implements robust error handling for invalid user inputs and system errors.
- **CICS Integration**: Utilizes CICS commands (`SEND`, `RECEIVE`, `XCTL`, `RETURN`) for seamless interaction with the user interface and program transitions.
- **Reusable Components**: Includes multiple `COPY` statements to reuse predefined structures and constants.

### Dependencies
The program relies on several external components and copybooks:
| **Copybook Name** | **Purpose** |
|--------------------|-------------|
| `COCOM01Y`         | Common constants and definitions. |
| `COADM02Y`         | Admin-specific definitions.       |
| `COADM01`          | Admin menu screen definitions.    |
| `COTTL01Y`         | Title-related constants.          |
| `CSDAT01Y`         | Date-related constants.           |
| `CSMSG01Y`         | Message-related constants.        |
| `CSUSR01Y`         | User-related constants.           |
| `DFHAID`           | CICS attention identifier constants. |
| `DFHBMSCA`         | CICS basic mapping support constants. |

### User Interaction
The program provides a menu-driven interface for admin users, allowing them to select options and navigate between screens. Invalid inputs are handled gracefully with error messages.

### Scalability
The program is designed to handle up to 10 menu options dynamically, making it scalable for additional features in the future.

### Licensing
The program is licensed under the Apache License, Version 2.0, ensuring open-source usage and distribution.

---
