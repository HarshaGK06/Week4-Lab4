# Week4-Lab4 COBOL Account Management

## Project Overview
This sample COBOL project implements a simple student account management system using three programs:
- `main.cob` — user interface and main menu loop
- `operations.cob` — account operations (view, credit, debit), and calls data management
- `data.cob` — persistent in-memory storage logic for account balance

It demonstrates modular COBOL design with `CALL` and `USING` parameter passing.

## File Purposes

### `src/cobol/main.cob`
- Shows a menu to the user (view balance, credit, debit, exit).
- Reads numeric user choice with `ACCEPT`.
- Uses `EVALUATE` (switch/case) to route operations.
- Calls `Operations` with operation codes:
  - `'TOTAL '` to view balance
  - `'CREDIT'` to credit account
  - `'DEBIT '` to debit account
- Loops until user selects exit.

### `src/cobol/operations.cob`
- Receives an operation code via linkage parameter `PASSED-OPERATION`.
- Uses `IF/ELSE` to distinguish operation codes:
  - `TOTAL`: read and display balance
  - `CREDIT`: prompt amount, read balance, add amount, write back balance
  - `DEBIT`: prompt amount, read balance, check for sufficient funds, subtract amount, write back balance
- Uses `CALL 'DataProgram' USING ...` to read/write shared balance.
- Enforces business logic for student account operations.

### `src/cobol/data.cob`
- Maintains working storage `STORAGE-BALANCE` (initial 1000.00).
- Accepts `PASSED-OPERATION` and `BALANCE` by reference.
- Implements two modes:
  - `READ`: move stored balance to caller's `BALANCE`
  - `WRITE`: replace stored balance with caller's `BALANCE`
- Ensures data is shared across calls and operations.

## Key Functions and Logic
- Menu selection and loop (`PERFORM UNTIL CONTINUE-FLAG = 'NO'`) in `main.cob`.
- Operation dispatch in `operations.cob` by text operation code.
- Persistence simulation with `data.cob` through `STORAGE-BALANCE` and call-by-reference.
- Basic validation in debit flow:
  - If `FINAL-BALANCE >= AMOUNT`, debit succeeds.
  - Otherwise show "Insufficient funds" and do not update balance.

## Student Account Business Rules
1. Starting account balance is 1000.00.
2. `View Balance` only reads and displays the current balance.
3. `Credit Account` adds the user-entered amount to the balance and writes it.
4. `Debit Account` only deducts if sufficient funds exist; insufficient funds do not change balance.
5. The user can repeat operations until choosing `4. Exit`.

## Notes
- This implementation is a simple console COBOL student account demo, with all data stored in program memory only (not persisted to disk).
- Operation codes are fixed strings and must match exactly (`TOTAL `, `CREDIT`, `DEBIT `) including spaces where defined.

## Sequence Diagram (Mermaid)
```mermaid
sequenceDiagram
    autonumber
    participant User
    participant Main as MainProgram
    participant Ops as Operations
    participant Data as DataProgram

    User->>Main: Start program
    Main->>User: Display menu
    User->>Main: Enter choice
    Main->>Ops: CALL 'Operations' USING 'TOTAL ' / 'CREDIT' / 'DEBIT '

    alt View Balance
      Ops->>Data: CALL 'DataProgram' USING 'READ', FINAL-BALANCE
      Data-->>Ops: RETURN FINAL-BALANCE
      Ops-->>User: DISPLAY Current balance
    else Credit
      Ops-->>User: DISPLAY Enter credit amount
      User->>Ops: ACCEPT AMOUNT
      Ops->>Data: CALL 'DataProgram' USING 'READ', FINAL-BALANCE
      Data-->>Ops: RETURN FINAL-BALANCE
      Ops: ADD AMOUNT TO FINAL-BALANCE
      Ops->>Data: CALL 'DataProgram' USING 'WRITE', FINAL-BALANCE
      Ops-->>User: DISPLAY New balance
    else Debit
      Ops-->>User: DISPLAY Enter debit amount
      User->>Ops: ACCEPT AMOUNT
      Ops->>Data: CALL 'DataProgram' USING 'READ', FINAL-BALANCE
      Data-->>Ops: RETURN FINAL-BALANCE
      alt Sufficient funds
        Ops: SUBTRACT AMOUNT FROM FINAL-BALANCE
        Ops->>Data: CALL 'DataProgram' USING 'WRITE', FINAL-BALANCE
        Ops-->>User: DISPLAY New balance
      else Insufficient funds
        Ops-->>User: DISPLAY Insufficient funds
      end
    end

    Main->>User: Display menu again or exit
    User->>Main: Choose 4 (Exit)
    Main->>User: Exiting program
```

