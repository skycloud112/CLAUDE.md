# Testing Notes

## Test Generation

- **Gemini chip**: With old test cases and example new style for tests, generated test cases for refactored code correctly with comprehensive cases
- Put sample data beside column definitions for test generation

## Database Gateway Testing

### Stored Procedures
- Just mock the return value

### SQL Queries
- Use Testcontainer to test SQL queries
- For optionals, provide empty string in case test DB doesn't have connected records

### Time-Based SQL Queries
- Mock fixed time in Node.js process
- Do NOT use `NOW()` in SQL query - Testcontainer time still uses real time
- Create time in DB gateway implementation instead

## Testing Strategies

### Mobile Testing
- Testing on real device helps catch:
  - Untappable areas
  - Mobile-specific gestures on maps
