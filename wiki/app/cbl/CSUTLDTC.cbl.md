# Documentation for `CSUTLDTC.cbl`

## Overview

The COBOL program `CSUTLDTC` is designed to interact with the `CEEDAYS` API, which converts a date into the Lillian date format. The program processes input dates and formats, evaluates the results, and provides feedback on the validity of the date. It includes error handling for various conditions returned by the API.

---

## Metadata

| **File Name** | `CSUTLDTC.cbl` |
|---------------|----------------|

---

## Sections

### Identification Division
- **Program ID**: `CSUTLDTC`
- This section identifies the program.

---

### Data Division

#### **Working-Storage Section**
This section declares variables used during program execution.

| **Variable Name**       | **Description**                                                                 |
|--------------------------|---------------------------------------------------------------------------------|
| `WS-DATE-TO-TEST`        | Structure to hold the input date passed to the `CEEDAYS` API.                  |
| `WS-DATE-FORMAT`         | Structure to hold the input date format passed to the `CEEDAYS` API.           |
| `OUTPUT-LILLIAN`         | Holds the Lillian date output from the `CEEDAYS` API.                          |
| `WS-MESSAGE`             | Contains the message and result information for the processed date.            |
| `FEEDBACK-CODE`          | Feedback code returned by the `CEEDAYS` API, with conditions for error handling.|

##### `WS-DATE-TO-TEST` Structure
| **Field Name**       | **Description**                                                                 |
|-----------------------|---------------------------------------------------------------------------------|
| `Vstring-length`      | Length of the date string.                                                     |
| `Vstring-text`        | Text of the date string, with dynamic length based on `Vstring-length`.         |

##### `WS-DATE-FORMAT` Structure
| **Field Name**       | **Description**                                                                 |
|-----------------------|---------------------------------------------------------------------------------|
| `Vstring-length`      | Length of the date format string.                                              |
| `Vstring-text`        | Text of the date format string, with dynamic length based on `Vstring-length`. |

##### `WS-MESSAGE` Structure
| **Field Name**       | **Description**                                                                 |
|-----------------------|---------------------------------------------------------------------------------|
| `WS-SEVERITY`         | Severity code returned by the API.                                             |
| `WS-SEVERITY-N`       | Numeric representation of `WS-SEVERITY`.                                       |
| `WS-MSG-NO`           | Message number returned by the API.                                            |
| `WS-MSG-NO-N`         | Numeric representation of `WS-MSG-NO`.                                         |
| `WS-RESULT`           | Result message describing the validity of the date.                           |
| `WS-DATE`             | Processed date string.                                                        |
| `WS-DATE-FMT`         | Processed date format string.                                                 |

##### `FEEDBACK-CODE` Structure
| **Field Name**       | **Description**                                                                 |
|-----------------------|---------------------------------------------------------------------------------|
| `FEEDBACK-TOKEN-VALUE` | Token value returned by the API.                                              |
| `CASE-1-CONDITION-ID` | Contains severity and message number.                                          |
| `CASE-2-CONDITION-ID` | Redefines `CASE-1-CONDITION-ID` with class and cause codes.                    |
| `CASE-SEV-CTL`        | Severity control.                                                             |
| `FACILITY-ID`         | Facility identifier.                                                          |
| `I-S-INFO`            | Additional information.                                                       |

##### Feedback Code Conditions
| **Condition Name**         | **Value**                  | **Description**                     |
|-----------------------------|----------------------------|-------------------------------------|
| `FC-INVALID-DATE`           | `X'0000000000000000'`      | Indicates the date is valid.       |
| `FC-INSUFFICIENT-DATA`      | `X'000309CB59C3C5C5'`      | Insufficient data provided.        |
| `FC-BAD-DATE-VALUE`         | `X'000309CC59C3C5C5'`      | Invalid date value.                |
| `FC-INVALID-ERA`            | `X'000309CD59C3C5C5'`      | Invalid era specified.             |
| `FC-UNSUPP-RANGE`           | `X'000309D159C3C5C5'`      | Unsupported range.                 |
| `FC-INVALID-MONTH`          | `X'000309D559C3C5C5'`      | Invalid month value.               |
| `FC-BAD-PIC-STRING`         | `X'000309D659C3C5C5'`      | Bad picture string.                |
| `FC-NON-NUMERIC-DATA`       | `X'000309D859C3C5C5'`      | Non-numeric data encountered.      |
| `FC-YEAR-IN-ERA-ZERO`       | `X'000309D959C3C5C5'`      | Year in era is zero.               |

#### **Linkage Section**
This section defines variables passed to and from the program.

| **Variable Name**   | **Description**                                                                 |
|----------------------|---------------------------------------------------------------------------------|
| `LS-DATE`           | Input date string passed to the program.                                       |
| `LS-DATE-FORMAT`    | Input date format string passed to the program.                                |
| `LS-RESULT`         | Output result string returned by the program.                                  |

---

### Procedure Division

#### **Main Logic**
The program processes the input date and format, calls the `CEEDAYS` API, and evaluates the feedback code to determine the validity of the date.

1. **Initialization**:
   - Clears `WS-MESSAGE` and `WS-DATE`.

2. **Main Processing**:
   - Moves the input date and format into the working-storage variables (`WS-DATE-TO-TEST` and `WS-DATE-FORMAT`).
   - Calls the `CEEDAYS` API with the input date, format, and feedback code.
   - Updates `WS-MESSAGE` with the severity and message number from the feedback code.

3. **Feedback Evaluation**:
   - Uses an `EVALUATE` statement to interpret the feedback code and set the result message (`WS-RESULT`).

4. **Output**:
   - Moves the result message (`WS-MESSAGE`) to the linkage section variable (`LS-RESULT`).

#### **Subroutine: `A000-MAIN`**
Handles the main processing logic, including:
- Preparing input for the `CEEDAYS` API.
- Calling the API.
- Handling feedback and setting the result message.

#### **Exit Logic**
- Exits the program after processing.

---

## Insights

- **Purpose**: The program is designed to validate dates and convert them into the Lillian date format using the `CEEDAYS` API.
- **Error Handling**: Comprehensive error handling is implemented using feedback codes and conditions.
- **Dynamic Structures**: The use of `DEPENDING ON` clauses allows for dynamic handling of date and format strings.
- **API Integration**: The program demonstrates integration with an external API (`CEEDAYS`) for date processing.
- **Reusability**: The linkage section allows the program to be reused as a callable module in other COBOL programs.

---
