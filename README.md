# PostgreSQL Course Notes

## [Course Link](https://youtu.be/qw--VYLpxG4)

## Notes

### Getting Started

Install PostgreSQL & pgAdmin4 using the following script (For Debian - MX Linux XFCE)

```bash
apt install postgresql
```

Then install pgAdmin using the following scipt

```bash
# Setup the repository

# Install the public key for the repository (if not done previously):
curl -fsS https://www.pgadmin.org/static/packages_pgadmin_org.pub | sudo gpg --dearmor -o /usr/share/keyrings/packages-pgadmin-org.gpg

# Create the repository configuration file:
sudo sh -c 'echo "deb [signed-by=/usr/share/keyrings/packages-pgadmin-org.gpg] https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/$(lsb_release -cs) pgadmin4 main" > /etc/apt/sources.list.d/pgadmin4.list && apt update'

# -----------------------------------------------------
# Install pgAdmin

# Install for both desktop and web modes:
sudo apt install pgadmin4

# Install for desktop mode only:
sudo apt install pgadmin4-desktop

# Install for web mode only:
sudo apt install pgadmin4-web
```

switch to psql user view

```bash
sudo -i -u [PSQL_USER (default = 'postgres')]
psql
```

adjust the server credentials

```sql
alter user PSQL_USER with password '[PSQL_PASSWORD]';
```

---

### Create a server

using pgAdmin4, enter host name `localhost`, password is the password you chose for the above command

---

### Create & connect to DB

using pgAdmin4 or using this command (never forget the `;` when dealing with SQL commands)

```sql
sudo -i -u [POSTGRES_DATABSE_USER]
psql
CREATE DATABASE [db_name];
\c [db_name]
```

To go back from table to psql user view

```bash
\q
```

To connect again

```bash
psql #Enter the psql user view
# you can do anything here from psql user view, like creating DBs
\c [db_name]
```

To create table

```sql
/* after connecting to db, write the following: */
CREATE TABLE [table_name] (
    column_name [data_type] CONSTRAINTS,
    column_name [data_type] CONSTRAINTS );
/*
last column will have the closing bracket with the semi colon
DON'T write them in a separate line, DON't end the last column with a comma
*/
```

---

### Data Types in PostgreSQL

[Refer to The Documentation](https://www.postgresql.org/docs/current/datatype.html)

- `INT` => integer
- `INT8` => big integer
- `FLOAT` => float
- `FLOAT8` => double
- `SERIAL4` => auto incrementing int
- `SERIAL8` => auto incrementing big int
- `BOOL` => boolean
- `CHAR(n)` => string with fixed length `n`
- `VARCHAR(n)` => string with a maximum length `n`
- `text` => string with no maximum length
- `DATE` => date (without time)
- `TIMESTAMP` => date with time (no timezone)
- `TIMESTAMPTZ` => date with time (and timezone)
- `UUID` => Uneversally Unique Identifier
- `JSON`, `JSONB` => JSON formats
- `MONEY` => currency

and much much more...

---

### Constraints in PostgreSQL

- `CHECK [condition]` => checks the following colums satsfies certain condition
- `NOT NULL` => can't be null
- `UNIQUE`
- `PRIMARY KEY`
- `REFERENCES [TABLE_WITH_PRIMARY_KEY].[PRIMARY_KEY_COLUMN]` => foriegn key

### Deleting Databases or Tables

To delete a DB or a table inside the databse:

1. Open the psql user view using the following command

```bash
sudo -i -u [PSQL_USER]
psql
```

2. If you want to delete a database, skip this step. Otherwise, if you want to delete a table, connect to the databse using this command. You may be required to enter the db password

```bash
\c [DB_NAME]
```

3. Delete the databse using the following command

```sql
DROP DATABASE [DB_NAME];
```

3. Or delete a table using the following command

```sql
DROP TABLE [TABLE_NAME];
```

Or you can use `pgAdmin` Which will make you like 10000000000% easier.

```sql
ORDER BY column1, column2
```

When ordering by multiple columns. 1st column will be considered for sorting, if there are multiple equal values in the first column. Second column will be considered, if 1st & 2nd columns' values are equal, 3rd column will be considered for sorting, and so on...

### Insert Values to Table

```sql
-- Connect to DB first \c db_name => Then use the following command

INSERT INTO table_name (
    c1_name,
    c2_name,
    c3_name,
)
VALUES (c1_value, c2_value, c3_value);
```

- **NOTE THAT** SQL is very sensitive to quotes (double quotes causes problems, use single quotes)
- When dealing with dates in SQL, write `DATE` followed up with the date in a single quotes in this format only 'yyyy-mm-dd'
