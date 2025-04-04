# CBSTM03B Documentation

## Metadata
| **File Name** | **CBSTM03B.CBL** |
|---------------|------------------|
| **Author**    | AWS              |
| **Application** | CardDemo       |
| **Type**      | Batch COBOL Subroutine |
| **Function**  | File processing related to Transact Report |

---

## Overview

CBSTM03B is a batch COBOL subroutine designed to handle file operations for the `CardDemo` application. It processes multiple indexed files (`TRNX-FILE`, `XREF-FILE`, `CUST-FILE`, and `ACCT-FILE`) and performs operations such as opening, reading, and closing files. The program is invoked by a higher-level statement creation program and operates based on the linkage section parameters provided.

---

## File Definitions

### File Control
The program defines four indexed files with specific access modes and record keys:

| **File Name** | **Assignment** | **Organization** | **Access Mode** | **Record Key**       | **File Status**       |
|---------------|----------------|-------------------|------------------|----------------------|-----------------------|
| `TRNX-FILE`   | `TRNXFILE`     | Indexed           | Sequential       | `FD-TRNXS-ID`        | `TRNXFILE-STATUS`     |
| `XREF-FILE`   | `XREFFILE`     | Indexed           | Sequential       | `FD-XREF-CARD-NUM`   | `XREFFILE-STATUS`     |
| `CUST-FILE`   | `CUSTFILE`     | Indexed           | Random           | `FD-CUST-ID`         | `CUSTFILE-STATUS`     |
| `ACCT-FILE`   | `ACCTFILE`     | Indexed           | Random           | `FD-ACCT-ID`         | `ACCTFILE-STATUS`     |

---

### File Section
Each file has a defined structure for its records:

| **File Name** | **Record Structure**                                                                 |
|---------------|--------------------------------------------------------------------------------------|
| `TRNX-FILE`   | `FD-TRNX-CARD` (16 bytes), `FD-TRNX-ID` (16 bytes), `FD-ACCT-DATA` (318 bytes)       |
| `XREF-FILE`   | `FD-XREF-CARD-NUM` (16 bytes), `FD-XREF-DATA` (34 bytes)                            |
| `CUST-FILE`   | `FD-CUST-ID` (9 bytes), `FD-CUST-DATA` (491 bytes)                                  |
| `ACCT-FILE`   | `FD-ACCT-ID` (11 digits), `FD-ACCT-DATA` (289 bytes)                                |

---

## Linkage Section

The linkage section defines the interface for external programs to interact with this subroutine. Key fields include:

| **Field Name**      | **Description**                          | **Picture Clause** |
|---------------------|------------------------------------------|--------------------|
| `LK-M03B-DD`        | File identifier                         | `PIC X(08)`        |
| `LK-M03B-OPER`      | Operation code                          | `PIC X(01)`        |
| `LK-M03B-RC`        | Return code                             | `PIC X(02)`        |
| `LK-M03B-KEY`       | Key value for indexed file operations   | `PIC X(25)`        |
| `LK-M03B-KEY-LN`    | Length of the key                       | `PIC S9(4)`        |
| `LK-M03B-FLDT`      | File data buffer                        | `PIC X(1000)`      |

### Operation Codes
The following operation codes are defined as condition names:

| **Operation Code** | **Description** |
|--------------------|-----------------|
| `O`                | Open file       |
| `C`                | Close file      |
| `R`                | Read file       |
| `K`                | Read file by key|
| `W`                | Write file      |
| `Z`                | Rewrite file    |

---

## Procedure Division

### Main Logic
The program starts by evaluating the file identifier (`LK-M03B-DD`) and performs the corresponding file operation procedure. If the file identifier does not match any predefined files, the program exits.

#### File Operation Procedures
Each file has a dedicated procedure for handling operations:

| **Procedure Name**       | **File**       | **Operations**                                                                 |
|--------------------------|----------------|-------------------------------------------------------------------------------|
| `1000-TRNXFILE-PROC`     | `TRNX-FILE`    | Open, Read, Close                                                            |
| `2000-XREFFILE-PROC`     | `XREF-FILE`    | Open, Read, Close                                                            |
| `3000-CUSTFILE-PROC`     | `CUST-FILE`    | Open, Read by Key, Close                                                     |
| `4000-ACCTFILE-PROC`     | `ACCT-FILE`    | Open, Read by Key, Close                                                     |

#### Exit Procedures
Each file operation procedure has an exit section to move the file status to the return code (`LK-M03B-RC`) and terminate the procedure.

---

## Insights

1. **Modular Design**: The program uses modular procedures for file operations, making it easier to maintain and extend.
2. **Error Handling**: File statuses are captured and returned via `LK-M03B-RC`, enabling external programs to handle errors effectively.
3. **Indexed File Operations**: The program is optimized for indexed file handling, supporting both sequential and random access modes.
4. **Scalability**: Additional files and operations can be integrated by defining new procedures and updating the `EVALUATE` statement.
5. **Linkage Section Flexibility**: The linkage section provides a flexible interface for external programs to specify operations and file identifiers dynamically.

---
