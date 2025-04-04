# CBTRN03C COBOL Program Documentation

## Metadata
| **File Name** | **Application** | **Type** | **Function** |
|---------------|-----------------|----------|--------------|
| CBTRN03C.cbl  | CardDemo        | Batch COBOL Program | Print the transaction detail report |

## Overview
The `CBTRN03C` program is a batch COBOL application designed to generate a transaction detail report. It processes transaction data, cross-references card information, and categorizes transactions based on type and category. The program reads input files, performs validations, and writes formatted reports to an output file.

## Key Features
- Reads transaction data from multiple input files.
- Validates and categorizes transactions.
- Generates detailed reports with totals and headers.
- Handles file operations (open, read, write, close) with error handling.
- Supports date range filtering for transactions.

---

## File Definitions

### Input Files
| **File Name**       | **Organization** | **Access Mode** | **Record Key**              | **File Status**       |
|---------------------|------------------|-----------------|-----------------------------|-----------------------|
| `TRANSACT-FILE`     | Sequential       | Sequential      | N/A                         | `TRANFILE-STATUS`     |
| `XREF-FILE`         | Indexed          | Random          | `FD-XREF-CARD-NUM`          | `CARDXREF-STATUS`     |
| `TRANTYPE-FILE`     | Indexed          | Random          | `FD-TRAN-TYPE`              | `TRANTYPE-STATUS`     |
| `TRANCATG-FILE`     | Indexed          | Random          | `FD-TRAN-CAT-KEY`           | `TRANCATG-STATUS`     |
| `DATE-PARMS-FILE`   | Sequential       | Sequential      | N/A                         | `DATEPARM-STATUS`     |

### Output Files
| **File Name**       | **Organization** | **File Status**       |
|---------------------|------------------|-----------------------|
| `REPORT-FILE`       | Sequential       | `TRANREPT-STATUS`     |

---

## Data Structures

### File Section
| **File**            | **Record Structure**                                                                 |
|---------------------|--------------------------------------------------------------------------------------|
| `TRANSACT-FILE`     | Contains transaction data (`FD-TRANS-DATA`), processing timestamp (`FD-TRAN-PROC-TS`), and filler. |
| `XREF-FILE`         | Contains card number (`FD-XREF-CARD-NUM`) and associated data (`FD-XREF-DATA`).      |
| `TRANTYPE-FILE`     | Contains transaction type (`FD-TRAN-TYPE`) and description (`FD-TRAN-DATA`).         |
| `TRANCATG-FILE`     | Contains transaction type code (`FD-TRAN-TYPE-CD`), category code (`FD-TRAN-CAT-CD`), and description (`FD-TRAN-CAT-DATA`). |
| `REPORT-FILE`       | Contains formatted report records (`FD-REPTFILE-REC`).                              |
| `DATE-PARMS-FILE`   | Contains date parameters (`FD-DATEPARM-REC`).                                        |

### Working-Storage Section
| **Variable**        | **Purpose**                                                                 |
|---------------------|-----------------------------------------------------------------------------|
| `WS-DATEPARM-RECORD`| Stores start and end dates for filtering transactions.                     |
| `WS-REPORT-VARS`    | Stores report-related variables such as line counters, totals, and page size. |
| `IO-STATUS`         | Stores file operation status codes.                                        |
| `APPL-RESULT`       | Indicates the result of application operations (e.g., success, EOF, error). |
| `END-OF-FILE`       | Tracks whether the end of a file has been reached.                         |

---

## Logic Flow

### Main Execution
1. **Initialization**:
   - Display start message.
   - Open all required files.
   - Read date parameters for filtering.

2. **Transaction Processing**:
   - Loop through transactions until EOF.
   - Filter transactions based on date range.
   - Validate and categorize transactions.
   - Write transaction details to the report file.
   - Update totals (page, account, grand).

3. **Finalization**:
   - Write final totals to the report.
   - Close all files.
   - Display end message.

### Error Handling
- File operations (open, read, write, close) are validated using file status codes.
- Errors are displayed, and the program aborts using the `9999-ABEND-PROGRAM` routine.

---

## Key Procedures

### File Operations
| **Procedure**         | **Description**                                                                 |
|-----------------------|---------------------------------------------------------------------------------|
| `0000-TRANFILE-OPEN`  | Opens the transaction file for input.                                           |
| `0100-REPTFILE-OPEN`  | Opens the report file for output.                                               |
| `0200-CARDXREF-OPEN`  | Opens the card cross-reference file for input.                                  |
| `0300-TRANTYPE-OPEN`  | Opens the transaction type file for input.                                      |
| `0400-TRANCATG-OPEN`  | Opens the transaction category file for input.                                  |
| `0500-DATEPARM-OPEN`  | Opens the date parameter file for input.                                        |
| `9000-TRANFILE-CLOSE` | Closes the transaction file.                                                    |
| `9100-REPTFILE-CLOSE` | Closes the report file.                                                         |
| `9200-CARDXREF-CLOSE` | Closes the card cross-reference file.                                           |
| `9300-TRANTYPE-CLOSE` | Closes the transaction type file.                                               |
| `9400-TRANCATG-CLOSE` | Closes the transaction category file.                                           |
| `9500-DATEPARM-CLOSE` | Closes the date parameter file.                                                 |

### Report Generation
| **Procedure**               | **Description**                                                                 |
|-----------------------------|---------------------------------------------------------------------------------|
| `1100-WRITE-TRANSACTION-REPORT` | Writes transaction details to the report file.                                |
| `1110-WRITE-PAGE-TOTALS`        | Writes page totals to the report file.                                        |
| `1120-WRITE-ACCOUNT-TOTALS`     | Writes account totals to the report file.                                     |
| `1110-WRITE-GRAND-TOTALS`       | Writes grand totals to the report file.                                       |
| `1120-WRITE-HEADERS`            | Writes headers to the report file.                                            |

### Lookup Operations
| **Procedure**         | **Description**                                                                 |
|-----------------------|---------------------------------------------------------------------------------|
| `1500-A-LOOKUP-XREF`  | Validates and retrieves card cross-reference data.                              |
| `1500-B-LOOKUP-TRANTYPE` | Validates and retrieves transaction type data.                                |
| `1500-C-LOOKUP-TRANCATG` | Validates and retrieves transaction category data.                            |

---

## Insights

1. **File Status Validation**:
   - The program rigorously checks file status codes after every file operation to ensure data integrity and proper error handling.

2. **Modular Design**:
   - The program is divided into modular procedures for file operations, report generation, and lookup operations, enhancing maintainability.

3. **Error Handling**:
   - Comprehensive error handling ensures that invalid data or file issues are logged and the program terminates gracefully.

4. **Date Filtering**:
   - Transactions are filtered based on a user-defined date range, making the report generation flexible and targeted.

5. **Totals Calculation**:
   - The program calculates page, account, and grand totals dynamically during report generation, ensuring accurate financial reporting.

6. **Apache License**:
   - The program is licensed under the Apache License 2.0, allowing for open-source usage and modification within the license terms.
