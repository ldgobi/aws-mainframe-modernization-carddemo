# CBTRN02C COBOL Program Documentation

## Metadata
| **File Name** | **Application** | **Type** | **Function** |
|---------------|-----------------|----------|--------------|
| CBTRN02C.cbl  | CardDemo        | Batch COBOL Program | Post records from daily transaction file |

## Overview
The `CBTRN02C` program is a batch COBOL application designed to process daily transaction records. It reads input files, validates transactions, updates account and transaction balances, and writes valid transactions to an output file while rejecting invalid ones. The program handles multiple indexed and sequential files and ensures data integrity through validation and error handling.

---

## File Definitions

### Input Files
| **File Name**       | **Organization** | **Access Mode** | **Record Key**         | **File Status**       |
|----------------------|------------------|-----------------|------------------------|-----------------------|
| `DALYTRAN-FILE`      | Sequential       | Sequential      | N/A                    | `DALYTRAN-STATUS`     |
| `XREF-FILE`          | Indexed          | Random          | `FD-XREF-CARD-NUM`     | `XREFFILE-STATUS`     |
| `ACCOUNT-FILE`       | Indexed          | Random          | `FD-ACCT-ID`           | `ACCTFILE-STATUS`     |
| `TCATBAL-FILE`       | Indexed          | Random          | `FD-TRAN-CAT-KEY`      | `TCATBALF-STATUS`     |

### Output Files
| **File Name**       | **Organization** | **Access Mode** | **Record Key**         | **File Status**       |
|----------------------|------------------|-----------------|------------------------|-----------------------|
| `TRANSACT-FILE`      | Indexed          | Random          | `FD-TRANS-ID`          | `TRANFILE-STATUS`     |
| `DALYREJS-FILE`      | Sequential       | Sequential      | N/A                    | `DALYREJS-STATUS`     |

---

## Data Structures

### File Records
| **File**             | **Record Name**           | **Fields**                                                                 |
|----------------------|---------------------------|-----------------------------------------------------------------------------|
| `DALYTRAN-FILE`      | `FD-TRAN-RECORD`          | `FD-TRAN-ID` (X(16)), `FD-CUST-DATA` (X(334))                              |
| `TRANSACT-FILE`      | `FD-TRANFILE-REC`         | `FD-TRANS-ID` (X(16)), `FD-ACCT-DATA` (X(334))                             |
| `XREF-FILE`          | `FD-XREFFILE-REC`         | `FD-XREF-CARD-NUM` (X(16)), `FD-XREF-DATA` (X(34))                         |
| `DALYREJS-FILE`      | `FD-REJS-RECORD`          | `FD-REJECT-RECORD` (X(350)), `FD-VALIDATION-TRAILER` (X(80))               |
| `ACCOUNT-FILE`       | `FD-ACCTFILE-REC`         | `FD-ACCT-ID` (9(11)), `FD-ACCT-DATA` (X(289))                              |
| `TCATBAL-FILE`       | `FD-TRAN-CAT-BAL-RECORD`  | `FD-TRAN-CAT-KEY` (Composite Key), `FD-FD-TRAN-CAT-DATA` (X(33))           |

### Working Storage
| **Variable Name**         | **Description**                          | **Type**         |
|---------------------------|------------------------------------------|------------------|
| `DALYTRAN-STATUS`         | File status for `DALYTRAN-FILE`          | X(2)             |
| `TRANFILE-STATUS`         | File status for `TRANSACT-FILE`          | X(2)             |
| `XREFFILE-STATUS`         | File status for `XREF-FILE`              | X(2)             |
| `DALYREJS-STATUS`         | File status for `DALYREJS-FILE`          | X(2)             |
| `ACCTFILE-STATUS`         | File status for `ACCOUNT-FILE`           | X(2)             |
| `TCATBALF-STATUS`         | File status for `TCATBAL-FILE`           | X(2)             |
| `WS-TRANSACTION-COUNT`    | Counter for processed transactions       | 9(9)             |
| `WS-REJECT-COUNT`         | Counter for rejected transactions        | 9(9)             |
| `WS-TEMP-BAL`             | Temporary balance calculation            | S9(9)V99         |
| `WS-VALIDATION-FAIL-REASON` | Validation failure reason code          | 9(4)             |
| `WS-VALIDATION-FAIL-REASON-DESC` | Validation failure description     | X(76)            |

---

## Program Logic

### Execution Flow
1. **Initialization**:
   - Open all required files (`DALYTRAN-FILE`, `TRANSACT-FILE`, `XREF-FILE`, `DALYREJS-FILE`, `ACCOUNT-FILE`, `TCATBAL-FILE`).
   - Validate file statuses and handle errors.

2. **Processing Transactions**:
   - Read records from `DALYTRAN-FILE`.
   - Validate transactions using:
     - Cross-reference lookup (`XREF-FILE`).
     - Account validation (`ACCOUNT-FILE`).
   - Post valid transactions:
     - Update transaction balances (`TCATBAL-FILE`).
     - Update account balances (`ACCOUNT-FILE`).
     - Write transaction to `TRANSACT-FILE`.
   - Write rejected transactions to `DALYREJS-FILE`.

3. **Finalization**:
   - Close all files.
   - Display summary of processed and rejected transactions.

4. **Error Handling**:
   - Display file status codes for errors.
   - Abort program execution if critical errors occur.

---

## Key Procedures

### File Operations
| **Procedure Name**       | **Operation**         | **Description**                          |
|--------------------------|-----------------------|------------------------------------------|
| `0000-DALYTRAN-OPEN`     | Open                 | Open `DALYTRAN-FILE` for input           |
| `0100-TRANFILE-OPEN`     | Open                 | Open `TRANSACT-FILE` for output          |
| `0200-XREFFILE-OPEN`     | Open                 | Open `XREF-FILE` for input               |
| `0300-DALYREJS-OPEN`     | Open                 | Open `DALYREJS-FILE` for output          |
| `0400-ACCTFILE-OPEN`     | Open                 | Open `ACCOUNT-FILE` for I-O              |
| `0500-TCATBALF-OPEN`     | Open                 | Open `TCATBAL-FILE` for I-O              |
| `9000-DALYTRAN-CLOSE`    | Close                | Close `DALYTRAN-FILE`                    |
| `9100-TRANFILE-CLOSE`    | Close                | Close `TRANSACT-FILE`                    |
| `9200-XREFFILE-CLOSE`    | Close                | Close `XREF-FILE`                        |
| `9300-DALYREJS-CLOSE`    | Close                | Close `DALYREJS-FILE`                    |
| `9400-ACCTFILE-CLOSE`    | Close                | Close `ACCOUNT-FILE`                     |
| `9500-TCATBALF-CLOSE`    | Close                | Close `TCATBAL-FILE`                     |

### Transaction Processing
| **Procedure Name**       | **Operation**         | **Description**                          |
|--------------------------|-----------------------|------------------------------------------|
| `1000-DALYTRAN-GET-NEXT` | Read                 | Read next record from `DALYTRAN-FILE`    |
| `1500-VALIDATE-TRAN`     | Validate             | Validate transaction using `XREF-FILE` and `ACCOUNT-FILE` |
| `2000-POST-TRANSACTION`  | Post                 | Post valid transaction                   |
| `2500-WRITE-REJECT-REC`  | Write                | Write rejected transaction to `DALYREJS-FILE` |
| `2700-UPDATE-TCATBAL`    | Update               | Update transaction balance in `TCATBAL-FILE` |
| `2800-UPDATE-ACCOUNT-REC`| Update               | Update account balance in `ACCOUNT-FILE` |
| `2900-WRITE-TRANSACTION-FILE` | Write          | Write transaction to `TRANSACT-FILE`    |

---

## Insights

1. **Validation Logic**:
   - Transactions are validated against card numbers (`XREF-FILE`) and account records (`ACCOUNT-FILE`).
   - Additional checks include credit limits and account expiration dates.

2. **Error Handling**:
   - File operations are monitored using status codes.
   - Errors are displayed and critical failures result in program termination.

3. **Performance**:
   - The program uses indexed files for efficient random access during validation and updates.
   - Sequential files are used for input and rejected transactions.

4. **Extensibility**:
   - Validation logic can be extended by adding more checks in `1500-VALIDATE-TRAN`.
   - Modular design allows easy addition of new procedures.

5. **Timestamp Handling**:
   - DB2-compatible timestamps are generated for transaction processing.

6. **Counters**:
   - Transaction and rejection counters provide a summary of processing results.

---
