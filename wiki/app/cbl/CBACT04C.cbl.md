# Documentation for COBOL Program: CBACT04C

## Metadata
| **File Name** | **Application** | **Type** | **Function** |
|---------------|-----------------|----------|--------------|
| CBACT04C.cbl  | CardDemo        | Batch COBOL Program | Interest Calculator Program |

## Overview
The program `CBACT04C` is designed to calculate interest for accounts based on transaction categories and update account balances accordingly. It processes multiple indexed files, performs calculations, and writes transaction records. The program is structured into multiple procedures for file operations, data retrieval, interest computation, and error handling.

---

## File Definitions
The program interacts with several indexed and sequential files. Below is a summary of the files and their structure:

| **File Name**       | **Organization** | **Access Mode** | **Record Key**               | **Alternate Key**         | **File Status**         |
|----------------------|------------------|-----------------|------------------------------|---------------------------|-------------------------|
| TCATBAL-FILE         | Indexed         | Sequential      | FD-TRAN-CAT-KEY             | N/A                       | TCATBALF-STATUS         |
| XREF-FILE            | Indexed         | Random          | FD-XREF-CARD-NUM            | FD-XREF-ACCT-ID           | XREFFILE-STATUS         |
| ACCOUNT-FILE         | Indexed         | Random          | FD-ACCT-ID                  | N/A                       | ACCTFILE-STATUS         |
| DISCGRP-FILE         | Indexed         | Random          | FD-DISCGRP-KEY              | N/A                       | DISCGRP-STATUS          |
| TRANSACT-FILE        | Sequential      | Sequential      | N/A                         | N/A                       | TRANFILE-STATUS         |

### File Record Structures
#### TCATBAL-FILE
| **Field Name**         | **Data Type** | **Length** |
|-------------------------|---------------|------------|
| FD-TRANCAT-ACCT-ID      | Numeric       | 11         |
| FD-TRANCAT-TYPE-CD      | Alphanumeric  | 2          |
| FD-TRANCAT-CD           | Numeric       | 4          |
| FD-FD-TRAN-CAT-DATA     | Alphanumeric  | 33         |

#### XREF-FILE
| **Field Name**         | **Data Type** | **Length** |
|-------------------------|---------------|------------|
| FD-XREF-CARD-NUM        | Alphanumeric  | 16         |
| FD-XREF-CUST-NUM        | Numeric       | 9          |
| FD-XREF-ACCT-ID         | Numeric       | 11         |
| FD-XREF-FILLER          | Alphanumeric  | 14         |

#### DISCGRP-FILE
| **Field Name**         | **Data Type** | **Length** |
|-------------------------|---------------|------------|
| FD-DIS-ACCT-GROUP-ID    | Alphanumeric  | 10         |
| FD-DIS-TRAN-TYPE-CD     | Alphanumeric  | 2          |
| FD-DIS-TRAN-CAT-CD      | Numeric       | 4          |
| FD-DISCGRP-DATA         | Alphanumeric  | 34         |

#### ACCOUNT-FILE
| **Field Name**         | **Data Type** | **Length** |
|-------------------------|---------------|------------|
| FD-ACCT-ID              | Numeric       | 11         |
| FD-ACCT-DATA            | Alphanumeric  | 289        |

#### TRANSACT-FILE
| **Field Name**         | **Data Type** | **Length** |
|-------------------------|---------------|------------|
| FD-TRANS-ID             | Alphanumeric  | 16         |
| FD-ACCT-DATA            | Alphanumeric  | 334        |

---

## Key Procedures
### Initialization
- **File Open Procedures**: Opens all required files (`TCATBAL-FILE`, `XREF-FILE`, `DISCGRP-FILE`, `ACCOUNT-FILE`, `TRANSACT-FILE`) and validates their statuses.
- **Error Handling**: Displays error messages and aborts the program if file opening fails.

### Main Processing Loop
- **Record Retrieval**: Reads records from `TCATBAL-FILE` sequentially until the end of the file.
- **Account Validation**: Checks if the account ID has changed and updates balances accordingly.
- **Interest Rate Retrieval**: Fetches interest rates from `DISCGRP-FILE` based on transaction category and type.
- **Interest Calculation**: Computes monthly interest using the formula:
  ```
  WS-MONTHLY-INT = (TRAN-CAT-BAL * DIS-INT-RATE) / 1200
  ```
- **Transaction Record Creation**: Writes transaction records to `TRANSACT-FILE`.

### File Closing
- **File Close Procedures**: Closes all files and validates their statuses.
- **Error Handling**: Displays error messages and aborts the program if file closing fails.

### Utility Procedures
- **Timestamp Conversion**: Converts the current date into DB2 timestamp format.
- **Error Display**: Displays detailed file status codes for debugging purposes.
- **Program Abortion**: Calls `CEE3ABD` to terminate the program in case of critical errors.

---

## Insights
1. **Interest Calculation Logic**: The program calculates interest based on transaction category balances and predefined interest rates. The formula ensures precision by dividing the annual rate by 1200 (monthly rate).
2. **Error Handling**: Comprehensive error handling is implemented for file operations, ensuring robust execution and clear debugging information.
3. **File Organization**: The use of indexed files allows efficient random access for account and transaction data, while sequential files are used for transaction processing.
4. **Modular Design**: The program is divided into modular procedures, making it easier to maintain and extend functionality.
5. **Unimplemented Features**: The procedure `1400-COMPUTE-FEES` is marked as "To be implemented," indicating future enhancements for fee computation.

---

## Program Flow
1. **Initialization**: Open files and validate statuses.
2. **Processing**:
   - Retrieve transaction category records.
   - Validate account changes and update balances.
   - Fetch interest rates and compute interest.
   - Write transaction records.
3. **Finalization**: Close files and validate statuses.
4. **Error Handling**: Abort program execution in case of critical errors.

---

## Dependencies
- **COPY Statements**: The program uses several copybooks (`CVTRA01Y`, `CVACT03Y`, `CVTRA02Y`, `CVACT01Y`, `CVTRA05Y`) for file status definitions.
- **External Parameters**: The program accepts external parameters (`PARM-LENGTH`, `PARM-DATE`) for processing.

---

## Future Enhancements
- Implement fee computation logic in `1400-COMPUTE-FEES`.
- Optimize file access patterns for better performance in large-scale data processing.
- Enhance error handling to include retry mechanisms for transient file access issues.
