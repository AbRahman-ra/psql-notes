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
sudo -i -u '<postgres_username (default:postgres)>'
psql
```

adjust the server credentials

```sql
alter user <postgres_username> with password '<psql_password>'
```

---

### Create a server

using pgAdmin4, enter host name `localhost`, password is the password you chose for the above command

---

### Create & connect to DB

using pgAdmin4 or using this command (never forget the `;` when dealing with SQL commands)

```sql
sudo -i -u '<postgres_username>'
psql
CREATE DATABASE '<db_name>';
\c '<db_name>'
```

**A much better option to connect the database** is to navigate to the folder your sql files are in

```bash
cd 'path/to/working/directory'
```

Then, connect to the databse. Configure the connection inside the working directory

```bash
psql -h localhost -p '<port (default is 5432)>' -U '<postgres_username>' -d '<db_name>'
```

To go back from table to psql user view

```bash
\q
```

To connect again

```bash
psql #Enter the psql user view
# you can do anything here from psql user view, like creating DBs
\c '<db_name>'
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
- `NUMERIC(precision, scale)` => can take no arguments at all. Or can take 2 arguments (precision = max total number of the digits, scale, total number of digits after the decimal place) => `NUMERIC(8,2) => 123456.78`

If we want to create a stype from a fixed list, for example (male or female, can't accept anything other than these two). So, welcome to enumerated types (enums)!

To create an enum in PostgreSQL. We create them as a type first, then assign the type into the columns.

```sql
CREATE TYPE gender AS ENUM ("male", "female");
-- creating a dummy database for twitter
CREATE DATABASE twitter;

CREATE TABLE users (
    user_id VARCHAR(20) UNIQUE NOT NULL PRIMARY KEY,
    user_name VARCHAR(50) NOT NULL,
    user_gender gender --user gender column is of type gender
    -- and much more columns
);
```

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
sudo -i -u [postgres_username]
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
- When dealing with dates in SQL, write `DATE` followed up with the date in a single quotes in this format only 'yyyy-mm-dd'. In `id` selection. Only double quotes work for me `id is a BIGSERIAL`.

---

### Querying Data

```sql
SELECT column1, column2, column3, ...
FROM table_name
WHERE (condition)
ORDER BY column1, column2, ...;
```

The condition is very similar to the if statements in programming. You can use logical operators `AND`, `OR`, `NOT`. Or even arithmetic operators (`=`, `<`, `>`, `<=`, `>=`, `<>`)

Note that "Not Equal" in SQL is `<>` and NOT `!=` or `~=` like other programming languages

You can use multiple conditions. You can use some reserved keywords like `NULL`. See the following query below:

```sql
SELECT first_name, last_name, date_of_birth, gender
FROM best_table
WHERE (gender = 'Female' OR gender = 'Male') AND email IS NULL
ORDER BY first_name, last_name;
```

If you want to select/count unqiue values only. We use

```sql
SELECT DISTINCT column1, column2
FROM 'table'
```

You can check the truth of a condition using `SELECT` command

```sql
SELECT 1 = 1; --true
SELECT 1 > 3; --false
SELECT 2 > 'Hi' --error
```

---

### Data Filteration

You can view `n` rows using `LIMIT n`. You can specify the starting point using `OFFSET x`.

```sql
SELECT first_name, last_name, date_of_birth, gender
FROM best_table
OFFSET 5 LIMIT 10 --start counting from the 5th item
```

You can use `FETCH` which does the same as `LIMIT`

```sql
SELECT first_name, last_name, date_of_birth, gender
FROM best_table
OFFSET 5 FETCH FIRST 10 ROWS ONLY;
```

The difference is `FETCH` is a SQL standard

To filter data using conditions, you can use `IN` keyword. Which is better than writing multiple `OR`s.

```SQL
SELECT "id", first_name, last_name, date_of_birth, gender
FROM best_table
WHERE gender IN ('Male', 'Female') AND email IS NULL  -- very neat and easy now
OFFSET 5 LIMIT 15;
```

To select data within a range. We use `BETWEEN` keyword

```sql
SELECT "id", first_name, last_name, date_of_birth, gender
FROM best_table
WHERE date_of_birth BETWEEN DATE '2000-01-01' AND DATE '2004-12-31'
AND gender IN ('Male', 'Female')
ORDER BY first_name, last_name;
```

---

### More Data Filtration

To match specific text patteer. We can user `LIKE` inside the `WHERE` condition. We follow `LIKE` with a text expression (similar to RegEx). Where `%` means any character of length 0 and more (similar to `\w*` in RegEx), and `_` means only 1 character (similar to `\w` in RegEx). You can use multiple underscores `___s` means 3 exact characters and followed by s.

```sql
SELECT "id", first_name, last_name, date_of_birth, gender
FROM best_table
WHERE date_of_birth BETWEEN DATE '2000-01-01' AND DATE '2004-12-31'
AND email LIKE '%.com'
AND first_name LIKE '%u_' -- The second to last letter is u
AND gender IN ('Male', 'Female')
ORDER BY first_name, last_name;
```

There is also `ILIKE` operator, which does the same exact as `LIKE` but it's case insensitive.

---

### Aggregate Functions & Grouping Data

you can use aggregate functions in the `SELECT` statement. The aggregate functions are

- `COUNT(column)`
- `AVG(column)`
- `MAX(column)`
- `MIN(column)`
- `SUM(column)`

to find the count of each gender in our data

```sql
SELECT COUNT("id"), gender
FROM best_table
WHERE gender = 'Male' OR gender = 'Female' -- There are other genders in the database
GROUP BY gender
```

**Note That** `GROUP BY` MUST be before `ORDER BY`

You can filter `GROUP BY` by adding `HAVING`. `HAVING` is a `WHERE` condition that can be added ONLY to aggregate functions

```sql
SELECT COUNT("id"), gender
FROM best_table
WHERE gender = 'Male' OR gender = 'Female' -- There are other genders in the database
GROUP BY gender
HAVING COUNT("id") > 200 -- show counts of gender that have only more than 200 people
```

Another Advanced example, where we want to found the min, average, max price for each car company. Displaying the number of cars each company has

```sql
-- company, #cars, min, avg, max, sum
SELECT
	company,
	COUNT(model) AS no_of_cars,
	/* Since car_price is money type, it will
	throw erros if we run the aggregate functions
	directky, we will cast them to numbers using
	the ::NUMERIC operator
	*/
	ROUND(MIN(car_price::NUMERIC)) AS min_price,
	ROUND(AVG(car_price::NUMERIC)) AS avg_price,
	ROUND(MAX(car_price::NUMERIC)) AS max_price,
	ROUND(SUM(car_price::NUMERIC)) AS total_sales
    /* We can not use ROUND and enter data
    to NUMERIC(p,s) isntead. But this may not be super
    good (found some prices like this 11788.000000000)
    */
FROM car_table
GROUP BY company
ORDER BY avg_price
```

---

### Arithmetic Operatos in SQL

- `+-`: addition and subtraction
- `*/`: multiplication and division
- `%`: modulus
- `^`: power
- `!` factorial

---

### Operations on columns

Consider that the following companies reduced their prices 20%

- Hyundai
- Ford
- Mitsubishi
- Lexus
- Mazda

Let's find the discounted price using SQL, we will refuce the min, max & avg for simplicity for now

```sql
SELECT
    company,
    COUNT(model),
    ROUND(MIN(car_price::NUMERIC)) AS min_original_price,
    ROUND(MIN(car_price::NUMERIC)*0.8) AS min_discounted_price,
    ROUND(AVG(car_price::NUMERIC)) AS avg_original_price,
    ROUND(AVG(car_price::NUMERIC)*0.8) AS avg_discounted_price,
    ROUND(MAX(car_price::NUMERIC)) AS max_original_price,
    ROUND(MAX(car_price::NUMERIC)*0.8) AS max_discounted_price
FROM car_table
WHERE company IN ('Hyundai', 'Ford', 'Mitsubishi', 'Lexus', 'Mazda')
-- in IN(), we use single quotes. Double quotes make problems
GROUP BY company
ORDER BY min_discounted_price;
```

You might see in the above query the word `AS`. It's for aliasing. Simply renaming the column. If I didn't rename them. THey will be shoun in their original names (the ones used in `CREATE TABLE`), or they will be renamed to the aggregate function used with them

```sql
SELECT ROUND(AVG(car_price::NUMERIC)) -- => column name = round
```

---

### Dealing with nullities in SQL

We use `COALESCE` to handle nulls inside a column. It's written inside the `SELECT` statement, and takes 2+ arguments.

`COALESCE(column, default value if the value in the column is null, 2nd default value if the 1st defualt is null)`

```sql
SELECT first_name, COALESCE(email, '0') -- all null emails will be replaced with 0
FROM best_table
```

Another important nullity control is the `NULLIF` operator in the `SELECT` statement. It takes 2 expressions. It returns null if the 2 expressions are equal. Otherwise it returns the first expression

```sql
SELECT NULLIF(0, COALESCE(email, 0))
/* Replace null emails with zero.
If the email is 0 replace it with NULL. It's stupid I know,
but I didn't find insightful examples
*/
FROM best_table
```

---

### Date & Time in PostgreSQL

Similar to JavaScript & TypeScript having a date function

```ts
let now = Date.now();
```

We can do the same in PostgreSQL

```sql
SELECT NOW()
-- "2023-12-07 07:26:31.453289+03" => "yyyy-mm-dd hh:mm:ss.ms"
```

To cast it to only date or only time

```sql
SELECT NOW()::DATE; -- "2023-12-07"
SELECT NOW()::TIME; -- "07:26:31.453289+03"
/* Don't write multiple queries in the same query tool in pgAdmin 4,
only the last one will run
*/
```

**Adding and subtracting dates**

To add and subtract dates. We can user `INTERVAL`. Assume you want to find the date 20 years, 3 months and 9 days before/after now

```sql
SELECT (date_of_birth + INTERVAL '20 YEARS 3 MONTHS 9 DAYS')::DATE AS new_date
-- use `-` to find dates before, `+` to find dates after
FROM best_table
```

To extract specific values such as year, month or day. We use `EXTRACT` operator. Its syntax is

```sql
EXTRACT(CENTURY/YEAR/MONTH/DAY/HOUR/MINUTE/SECOND FROM date_or_time)
```

**Note that** when extracting days. They will be ordered from 0 (Sunday) till 6 (Saturday)

Let's get it work

```sql
SELECT
    date_of_birth,
    (date_of_birth + INTERVAL '15 YEARS 4 MONTHS 13 DAYS')::DATE AS new_date,
EXTRACT(YEAR FROM date_of_birth + INTERVAL'15 YEARS') AS new_year
FROM best_table
```
