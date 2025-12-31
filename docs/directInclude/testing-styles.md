# Testing Styles

## General
- Use Vitest
- Assert resolved values

## Test Body
- Use straight-line code, descriptive IDs
- Make each test self-contained. Reset id to 1 in each test.
- Don't test types
- When setting up test data, only provide fields pertinent to current test. For other fields, use default values provided by test helpers.
- For delete/update operations: verify state changes, NOT method calls
- If a test uses setSystemTime, do not call vi.useRealTimers or vi.useFakeTimers
