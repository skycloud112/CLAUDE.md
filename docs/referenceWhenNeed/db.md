# Database Notes

## Data Types & Storage

- DB should store Dates and DB interface should use Date
- For columns that don't have a value, PostgreSQL returns null
- Store date as epoch. `mmddyyyy` date is calculated on the fly
- If only year/month/day needed, store date epoch with midnight time in UTC. Server runs in UTC for easy comparison
- With PostgreSQL, can store lng and lat freeform, no need to constraint by MongoDB point object
- When using `jsonb`, it converts timestamp Date to string

## Date Handling

- **Frontend**: Pass Date around. When calling API, convert to `mmddyyyy` date string
- **Server**: Create Date with midnight UTC and pass around. DB stores midnight UTC for easy comparison
- **MySQL**: Return DATETIME as string and assume timezone at server side with Luxon
- **MySQL**: Get DATETIME as string for easily matching timestamps between tables without converting Date to DATETIME string

## Migrations

- **Migration Steps**: Create new entity → Test create with dummy data (DB create and schema success) → Test each gateway method in script
- Automate migration scripts to prevent missing manually created SQL queries and mistakes
- **Before migration**:
  - Back up to a separate branch in Neon
  - Record high-level migration in accomplishment section in OneNote
  - Copy newest data from prod to dev
  - Copy parent and child tables together (deleting parent table also deletes child table)
  - Save the version and log time
- **After migration**: Save the version and log time
- **Finding migrations**: Find by high-level description to locate date → Find SQL query in Postico and scripts in repo
- **Record keeping**: In both Postico and scripts, record date and time ran to find the previous branch in Neon before migration

## Schema Changes

- Removing fields in entities can detect what code relies on it (gateway impl and use case)
- If migration script depends on gateway impl, it will break
- When reading old fields in migration script, read directly from DB
- When listing fields to move: Create list for new table, create deletion list for old table

## Queries & Performance

- Using "CASE IN" is slow for larger data sets
- When selecting from a joined table, need to include the table in the "GROUP BY"
- One gateway can process from multiple related tables

## Relationships & Integrity

- **Pros of join table**: Referential integrity, search by table content
- Add foreign key constraint (with `ON DELETE RESTRICT`) to prevent referenced rows being deleted

## Data Quality

- Non-dash (186) and dash (186-188) addresses are used interchangeably
- Before removing duplicated addresses, go through addresses to identify those that should be merged and confirmed
