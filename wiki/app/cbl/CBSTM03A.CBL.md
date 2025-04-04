# COBOL Program Documentation: CBSTM03A

## Metadata
- **File Name**: `CBSTM03A.CBL`
- **Author**: AWS
- **Application**: CardDemo
- **Type**: Batch COBOL Program
- **Function**: Print account statements from transaction data in two formats: plain text and HTML.

---

## Overview

This COBOL program processes transaction data to generate account statements in two formats: plain text and HTML. It utilizes various COBOL features such as control block addressing, `ALTER` and `GO TO` statements, `COMP` and `COMP-3` variables, two-dimensional arrays, and subroutine calls. The program is designed to modernize legacy systems and demonstrate COBOL's capabilities in handling structured data and file operations.

---

## Features

1. **Mainframe Control Block Addressing**: The program uses control blocks (`PSA`, `TCB`, `TIOT`) to interact with system-level data structures.
2. **File Operations**: Handles multiple files (`STMT-FILE`, `HTML-FILE`, `TRNXFILE`, `XREFFILE`, `CUSTFILE`, `ACCTFILE`) for reading and writing data.
3. **Dynamic File Handling**: Uses `ALTER` and `GO TO` statements to dynamically change program flow based on file types.
4. **Data Structures**:
   - **COMP and COMP-3 Variables**: Used for efficient numeric calculations.
   - **Two-Dimensional Arrays**: Stores transaction data for multiple cards.
5. **HTML Generation**: Creates HTML output for account statements with structured formatting.
6. **Subroutine Calls**: Calls external subroutine `CBSTM03B` for file operations.

---

## Data Structures

### File Section
| **File**       | **Record Layout**                     |
|-----------------|---------------------------------------|
| `STMT-FILE`    | `FD-STMTFILE-REC` (80 characters)     |
| `HTML-FILE`    | `FD-HTMLFILE-REC` (100 characters)    |

### Working-Storage Section
| **Variable Group**       | **Description**                                                                 |
|---------------------------|-------------------------------------------------------------------------------|
| `COMP-VARIABLES`          | Numeric variables for counters and jumps (`CR-CNT`, `TR-CNT`, etc.).          |
| `COMP3-VARIABLES`         | Packed decimal variable for total amount (`WS-TOTAL-AMT`).                   |
| `MISC-VARIABLES`          | Miscellaneous variables (`WS-FL-DD`, `END-OF-FILE`, etc.).                   |
| `STATEMENT-LINES`         | Structured layout for plain text statements.                                 |
| `HTML-LINES`              | Predefined HTML tags and dynamic content for HTML statements.                |
| `WS-TRNX-TABLE`           | Two-dimensional array for card and transaction data.                         |
| `PSA`, `TCB`, `TIOT`      | Control blocks for system-level operations.                                  |

---

## Logic Flow

### Procedure Division

#### Initialization
1. **Control Block Setup**:
   - Sets addresses for `PSA`, `TCB`, and `TIOT` blocks.
   - Displays running JCL and step information.
2. **File Operations**:
   - Opens `STMT-FILE` and `HTML-FILE`.
   - Initializes transaction tables.

#### Mainline Processing
1. **File Selection**:
   - Dynamically alters program flow based on file type (`TRNXFILE`, `XREFFILE`, etc.).
2. **Data Retrieval**:
   - Reads data from `XREFFILE`, `CUSTFILE`, `ACCTFILE`, and `TRNXFILE`.
   - Populates working-storage variables and tables.
3. **Statement Creation**:
   - Generates plain text and HTML statements using structured layouts.
   - Writes transaction details and summary.

#### File Closure
- Closes all files (`TRNXFILE`, `XREFFILE`, `CUSTFILE`, `ACCTFILE`, `STMT-FILE`, `HTML-FILE`).

#### Error Handling
- Displays error messages and calls `CEE3ABD` for program abend in case of failures.

---

## Key Sections

### Statement Layouts
#### Plain Text
| **Line**       | **Content**                                                                 |
|-----------------|-----------------------------------------------------------------------------|
| `ST-LINE0`     | Header with "START OF STATEMENT".                                           |
| `ST-LINE1`     | Customer name.                                                             |
| `ST-LINE2-4`   | Address lines.                                                             |
| `ST-LINE7-9`   | Basic details (Account ID, Balance, FICO Score).                           |
| `ST-LINE13-14` | Transaction details (ID, Description, Amount).                             |
| `ST-LINE14A`   | Total transaction amount.                                                  |
| `ST-LINE15`    | Footer with "END OF STATEMENT".                                            |

#### HTML
| **Tag**        | **Description**                                                            |
|-----------------|-----------------------------------------------------------------------------|
| `<table>`      | Main container for statement layout.                                       |
| `<tr>`         | Row for each section (header, details, transactions).                     |
| `<td>`         | Columns for specific data (e.g., Account ID, Balance, Transactions).       |

---

## Insights

1. **Modernization Potential**:
   - The program demonstrates COBOL's ability to handle structured data and generate modern outputs (HTML).
   - It can be integrated with web-based systems for real-time statement generation.

2. **Performance Optimization**:
   - Use of `COMP` and `COMP-3` variables ensures efficient numeric processing.
   - Two-dimensional arrays simplify transaction data management.

3. **Dynamic Flow Control**:
   - `ALTER` and `GO TO` statements provide flexibility but may complicate debugging. Consider replacing with structured programming constructs.

4. **Error Handling**:
   - Error messages are displayed for file operations, but recovery mechanisms are limited. Enhancing error handling could improve robustness.

5. **HTML Formatting**:
   - Predefined HTML tags ensure consistent formatting but limit customization. Dynamic HTML generation could be explored for more flexibility.

6. **Legacy System Integration**:
   - The program interacts with mainframe control blocks, making it suitable for legacy system environments.
