## SQL vs psql commands

In `psql` there are **two kinds of “commands”**:

1. **SQL statements** → Postgres executes these (end with `;`)
    - `CREATE DATABASE ...;`
    - `CREATE TABLE ...;`
    - `INSERT ...;`
    - `SELECT ...;`
2. **psql meta-commands** → `psql` client handles these (start with `\`, no `;`)
    - `\c test_db` (connect to database)
    - `\l` (list databases)
    - `\dt` (list tables)

## Getting Started with basic commands

## 1) `CREATE DATABASE test_db;`

### Meaning

- “Create a new database named `test_db` on this Postgres server.”

### Structure

```sql
CREATE DATABASE database_name;
```

### Notes / Rules

- `test_db` is an identifier (a name).
- Most simple names are fine. If you use spaces or special chars, you must quote:
    
    ```sql
    CREATE DATABASE "test db";
    ```
    

### How you’d verify

```sql
\l
```

---

## 2) `\c test_db` (psql equivalent of “USE test_db”)

### Meaning

- “Disconnect from current DB and connect to `test_db`.”

### Structure

```
\c database_name
```

### Notes

- This is a **psql meta-command**, not SQL — so no `;`
- It changes the *current session’s* database.

### Verify you’re in the right DB

```sql
SELECT current_database();
```

---

## 3) `CREATE TABLE greetings (...)`

### Meaning

- “Create a table named `greetings` with columns defined inside parentheses.”

### Structure (general)

```sql
CREATE TABLE table_name (
  column_name data_type constraints,
  column_name data_type constraints,
  ...
);
```

### Your table (modern Postgres way)

```sql
CREATE TABLE greetings (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  message VARCHAR(255)
);
```

Let’s decode each part:

### `CREATE TABLE greetings`

- Creates a new table object in the current DB, usually in the `public` schema unless you specify another.

### `( ... )`

Inside are **column definitions**.

### `id INT ...`

- Column name: `id`
- Data type: `INT` (integer)

### `GENERATED ALWAYS AS IDENTITY`

- This is the Postgres-native replacement for MySQL’s `AUTO_INCREMENT`.
- Postgres will automatically generate a new number for `id` when you insert rows (unless you explicitly override, but “ALWAYS” makes it stricter).

(Alternative you might see: `SERIAL` — older style.)

### `PRIMARY KEY`

- Means: this column uniquely identifies each row.
- Adds:
    - **NOT NULL**
    - **UNIQUE**
    - An index for fast lookups

### `message VARCHAR(255)`

- A text column limited to 255 characters.
- In Postgres, you can also simply do:
    
    ```sql
    message TEXT
    ```
    
    (Often preferred unless you *need* strict length.)
    

### Verify table exists

```sql
\dt
```

### See table structure

```sql
\d greetings
```

---

## 4) `INSERT INTO greetings (message) VALUES ('Hello, World!');`

### Meaning

- “Insert a new row into `greetings` table, putting this value into the `message` column.”

### Structure (general)

```sql
INSERT INTO table_name (col1, col2, ...)
VALUES (val1, val2, ...);
```

### Why we didn’t insert `id`

Because `id` is **identity/auto-generated**, Postgres fills it automatically.

### Important rules

- Strings use **single quotes**:
    - ✅ `'Hello'`
    - ❌ `"Hello"` (double quotes are for identifiers like column/table names)
- Order matters: the values match the listed columns.

### Verify inserted row

```sql
SELECT * FROM greetings;
```