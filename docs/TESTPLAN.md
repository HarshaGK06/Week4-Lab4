# Account Management COBOL Test Plan

This test plan covers the business logic in the current COBOL app (`main.cob`, `operations.cob`, `data.cob`) and is meant for stakeholder validation.

| Test Case ID | Test Case Description | Pre-conditions | Test Steps | Expected Result | Actual Result | Status (Pass/Fail) | Comments |
|---|---|---|---|---|---|---|---|
| TC-01 | View initial balance | Program started fresh (balance starts at 1000.00) | 1. Run application\n2. Select option 1 (View Balance) | Displays current balance as 001000.00 | | | |
| TC-02 | Credit account with valid amount | Balance is 1000.00 | 1. Run application\n2. Select option 2 (Credit Account)\n3. Enter amount 100 | Displays amount credited and new balance 001100.00 | | | |
| TC-03 | Debit account with valid amount <= balance | Balance is 1000.00 | 1. Run application\n2. Select option 3 (Debit Account)\n3. Enter amount 500 | Displays amount debited and new balance 000500.00 | | | |
| TC-04 | Debit account with amount greater than balance (insufficient funds) | Balance is 1000.00 | 1. Run application\n2. Select option 3 (Debit Account)\n3. Enter amount 1200 | Displays "Insufficient funds for this debit." and balance remains unchanged (001000.00) | | | |
| TC-05 | Repeated operations preserve balance state across calls | Start with balance 1000.00 | 1. Run application\n2. Option 2, amount 200 (credit)\n3. Option 3, amount 100 (debit)\n4. Option 1 (view) | Balance displays 001100.00 (1000 + 200 - 100) | | | |
| TC-06 | Exit program gracefully | Program running | 1. Run application\n2. Select option 4 (Exit) | Program exits after printing goodbye message | | | |
| TC-07 | Invalid menu option handling | Program running | 1. Run application\n2. Enter choice 5 or text | Displays "Invalid choice, please select 1-4." and returns to menu | | | |
| TC-08 | Operation code matching exact string length | Program running from main options | 1. CALL 'Operations' USING 'TOTAL '\n2. CALL 'Operations' USING 'DEBIT ' | Works with exact fixed-length codes; operations recognized correctly | | | |
| TC-09 | Data program READ/WRITE behavior with linkage | Program running operations | 1. In `Operations`, call `DataProgram` with 'READ' and variable\n2. Call with 'WRITE' and updated variable | DataProgram reads/writes storage balance correctly using passed parameter | | | |
| TC-10 | No data persistence across program exits | After run and exit | 1. Run application\n2. Change balance via credit/debit\n3. Exit\n4. Re-run and view balance | Balance resets to 001000.00 since storage is in-memory only | | | |
