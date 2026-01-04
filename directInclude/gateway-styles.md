# Gateway Styles

## General Principles
- Gateways should be thin and focus on one entity only
- One gateway should not join tables of another entity

## Implementation
- Do not use "NOW" in sql queries
- In memory impl should be kept in sync with real impl
- Sql query should be written in "@nearform/sql"
- Internal implementation functions return type should match gateway interface
