# Documentation for COSGN00C.CBL

## Metadata
| **File Name** | **COSGN00C.cbl** |
|---------------|------------------|
| **Application** | CardDemo |
| **Type** | CICS COBOL Program |
| **Function** | Signon Screen for the CardDemo Application |

## Overview
The `COSGN00C.CBL` program is a CICS COBOL application designed to handle the sign-on screen functionality for the CardDemo application. It interacts with the user to collect credentials (User ID and Password), validates them against a user security file, and directs the user to the appropriate program based on their role (e.g., Admin or General User).

## Sections

### Identification Division
- **Program ID**: `COSGN00C`
- **Author**: AWS

### Environment Division
Defines the configuration for the program's runtime environment.

### Data Division

#### Working-Storage Section
This section contains variables used throughout the program. Key variables include:
| **Variable Name** | **Description** | **Type** | **Initial Value** |
|--------------------|-----------------|----------|-------------------|
| `WS-PGMNAME` | Program name | PIC X(08) | `'COSGN00C'` |
| `WS-TRANID` | Transaction ID | PIC X(04) | `'CC00'` |
| `WS-MESSAGE` | Message buffer | PIC X(80) | Spaces |
| `WS-USRSEC-FILE` | User security file name | PIC X(08) | `'USRSEC  '` |
| `WS-ERR-FLG` | Error flag | PIC X(01) | `'N'` |
| `ERR-FLG-ON` | Error flag condition | 88 Level | `'Y'` |
| `ERR-FLG-OFF` | Error flag condition | 88 Level | `'N'` |
| `WS-RESP-CD` | Response code | PIC S9(09) COMP | Zeros |
| `WS-REAS-CD` | Reason code | PIC S9(09) COMP | Zeros |
| `WS-USER-ID` | User ID | PIC X(08) | None |
| `WS-USER-PWD` | User Password | PIC X(08) | None |

#### Linkage Section
Defines the communication area (`DFHCOMMAREA`) for passing data between programs. It uses dynamic sizing based on `EIBCALEN`.

### Procedure Division
The Procedure Division contains the program's logic, organized into several paragraphs:

#### MAIN-PARA
- Initializes error flags and message buffers.
- Handles user input based on the `EIBAID` key pressed:
  - **Enter Key**: Processes user credentials.
  - **PF3 Key**: Displays a "Thank You" message.
  - **Other Keys**: Displays an invalid key message.

#### PROCESS-ENTER-KEY
- Receives user input from the sign-on screen.
- Validates the User ID and Password:
  - Prompts for missing credentials.
  - Converts input to uppercase for consistency.
- Reads the user security file to verify credentials.

#### SEND-SIGNON-SCREEN
- Sends the sign-on screen to the user with appropriate error messages.

#### SEND-PLAIN-TEXT
- Sends plain text messages to the user.

#### POPULATE-HEADER-INFO
- Populates header information such as current date, time, transaction ID, and program name.

#### READ-USER-SEC-FILE
- Reads the user security file to validate credentials.
- Directs the user to the appropriate program based on their role:
  - **Admin**: Transfers control to `COADM01C`.
  - **General User**: Transfers control to `COMEN01C`.

## Insights
- **Error Handling**: The program uses flags (`ERR-FLG-ON` and `ERR-FLG-OFF`) to manage error states and provide user feedback.
- **Dynamic Communication Area**: The `DFHCOMMAREA` is dynamically sized, allowing flexibility in data exchange.
- **Role-Based Navigation**: The program differentiates between Admin and General User roles, ensuring appropriate access control.
- **CICS Integration**: The program heavily relies on CICS commands (`SEND`, `RECEIVE`, `READ`, `XCTL`) for screen management, data access, and program control.
- **Modular Design**: The program is structured into reusable paragraphs, improving maintainability and readability.

## External Dependencies
The program includes several copybooks:
| **Copybook Name** | **Purpose** |
|--------------------|-------------|
| `COCOM01Y` | Common definitions |
| `COSGN00` | Sign-on screen definitions |
| `COTTL01Y` | Title information |
| `CSDAT01Y` | Date-related definitions |
| `CSMSG01Y` | Message definitions |
| `CSUSR01Y` | User-related definitions |
| `DFHAID` | CICS attention identifier |
| `DFHBMSCA` | CICS basic mapping support control area |

## License
The program is licensed under the Apache License, Version 2.0. For details, visit [Apache License 2.0](http://www.apache.org/licenses/LICENSE-2.0).
