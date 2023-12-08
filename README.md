# PostgreSQL Course Notes

## [Course Link](https://youtu.be/qw--VYLpxG4)

---

## Getting Started

**Note that the installation for Windows & MAC users is mentioned in the course. You can access it from the official documentations for PostgreSQL & pgAdmin4. I will explain the installation for the linux users here**

1. Install PostgreSQL & pgAdmin4 using the following script (For Debian)

```bash
apt install postgresql
```

2. Then install pgAdmin using the following scipt

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

3. Switch to psql user view

```bash
sudo -i -u '<postgres_username (default:postgres)>'
psql
```

4. Adjust the server credentials

```sql
alter user <postgres_username> with password '<psql_password>'
```

---

## Creating Your First Server

using pgAdmin4:

1. Expand the "Servers" dropdown
2. Right click and choose Register -> Server
3. Name your server
4. Go to "Connection" tab and enter host name `localhost`, password is the password you chose for the above command (Adjusting server credentials)
5. Confirm

---

## Creating & Connecting to Your First DB

using pgAdmin4 or using this command (never forget the `;` when dealing with SQL commands)

```bash
# 1. Change the user to the postgreSQL user
sudo -i -u '<postgres_username>'

# 2. Enter psql
psql

# 3. Create the db
CREATE DATABASE '<db_name>';

# 4. Connect to the database
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

To go back from table to psql user view in the terminal window

```bash
\q
```

To connect again

```bash
psql #Enter the psql user view
# you can do anything here from psql user view, like creating DBs
\c '<db_name>'
```

---

## Creating Your First Table in a Database

**For SQL queries, you can use th Query Tool in pgAdmin4, just expand the "Databses" dropdown, then right click and choose "Query Tool". Here, you'll be able to deal with pgAdmin4 and not the command line terminal**

Connect to a databse fist. Then, to create table. Use the following query

```sql
CREATE TABLE [table_name] (
  column_name [data_type] CONSTRAINTS,
  column_name [data_type] CONSTRAINTS );

-- last column will have the closing bracket with the semi colon
-- DON'T write them in a separate line, DON't end the last column with a comma
```

---

## Data Types in PostgreSQL

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
- `ENUM(values)` => enums
  - The enums are a type from a fixed list, for example (male or female, can't accept anything other than these two). It stands for enumerated types
- `NUMERIC(precision, scale)` => Numeric Values
  - can take no arguments at all. Or can take 2 arguments (precision = max total number of the digits, scale, total number of digits after the decimal place) => `NUMERIC(8,2) => 123456.78`

and much much more...

### Enums Creation (Special Case)

Unlike other data types, to create an enum in PostgreSQL. We create them as a type first, then assign the type into the table columns.

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

---

## Constraints in PostgreSQL

- `CHECK [condition]` => checks the following colums satsfies certain condition
- `NOT NULL` => can't be null
- `UNIQUE`
- `PRIMARY KEY`
- `FOREIGN KEY REFERENCES [TABLE_WITH_PRIMARY_KEY].[PRIMARY_KEY_COLUMN]` => foreign key

---

## Deleting Databases or Tables

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

---

## Sorting Columns

When sorting columns in PostgreSQL, we use the keyword `ORDER BY` after selecting the columns and defining the tables. It's a good practice to write the `ORDER BY` as the last statement in your query.

```sql
SELECT column1, column2, column3
FROM my_table
ORDER BY column1, column2 DESC;
```

- When ordering by multiple columns.
  - 1st column will be considered for sorting
  - If there are multiple equal values in the first column. Second column will be considered.
  - If 1st & 2nd columns' values are equal, 3rd column will be considered for sorting, and so on...
- The sorting order is ascending by default
- You can write `ASC` at the end of your `ORDER BY` statement to confirm ascending order, and `DESC` to change the order to descending order

---

## Insert Values to Table

```sql
INSERT INTO table_name (
  -- If you're using command line, make sure you connect to the database
  c1_name,
  c2_name,
  c3_name,
  ...
)
VALUES (c1_value, c2_value, c3_value);
```

- **NOTE THAT** SQL is very sensitive to quotes (double quotes causes problems, use single quotes)
- When dealing with dates in SQL, write `DATE` followed up with the date in a single quotes in this format only 'yyyy-mm-dd'
- In creating and selecting a column named `id` selection. Put it inside double quotes `id is a BIGSERIAL`. This is because `id` is a reserved word in SQL

---

## Querying Data

```sql
SELECT column1, column2, column3, ...
FROM table_name
WHERE (condition)
ORDER BY column1, column2, ...;
```

- The condition is very similar to the if statements in programming
- You can use logical operators `AND`, `OR`, `NOT`. Or even arithmetic operators (`=`, `<`, `>`, `<=`, `>=`, `<>`)
  - Note that "Not Equal" in SQL is `<>` and NOT `!=` or `~=` like other programming languages
  - In MySQL, `!=` is a valid Syntax for `<>`
- You can use multiple conditions. You can use some reserved keywords like `NULL`. See the following query below:

```sql
SELECT first_name, last_name, date_of_birth, gender
FROM best_table
WHERE (gender = 'Female' OR gender = 'Male') AND email IS NULL -- You can write IS NOT NULL for the opposite
ORDER BY first_name, last_name;
```

If you want to select/count unqiue values only. We use `DISTINCT` keyword

```sql
SELECT DISTINCT column1, column2
FROM table_name
```

You can check the truth of a condition using `SELECT` command

```sql
SELECT 1 = 1; --true
SELECT 1 > 3; --false
SELECT 2 > 'Hi' --error
```

---

## Data Filteration

### Limiting & Offsetting Data

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

### Bringing Data with Any of these Values/Conditions

To filter data that satisfies any of the values/conditions, you can use `IN` keyword. Which is better than writing multiple `OR`s.

```SQL
SELECT "id", first_name, last_name, date_of_birth, gender
FROM best_table
WHERE gender IN ('Male', 'Female') AND email IS NULL  -- very neat and easy now
OFFSET 5 LIMIT 15;
```

### Bringing Data Within Certain Range of Values

To select data within a range. We use `BETWEEN` keyword

```sql
SELECT "id", first_name, last_name, date_of_birth, gender
FROM best_table
WHERE date_of_birth BETWEEN DATE '2000-01-01' AND DATE '2004-12-31'
AND gender IN ('Male', 'Female')
ORDER BY first_name, last_name;
```

### Matching Strings

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

## Aggregate Functions & Grouping Data

You can use aggregate functions in the `SELECT` statement. The aggregate functions are

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

You can filter `GROUP BY` by adding `HAVING`. `HAVING` is a `WHERE` condition that can be added ONLY to aggregate functions, since they don't have `WHERE` statement

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

## Arithmetic Operatos in SQL

- `+-`: addition and subtraction
- `*/`: multiplication and division
- `%`: modulus
- `^`: power
- `!` factorial

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

You might see in the above query the word `AS`. It's for aliasing. Simply renaming the column. If I didn't rename them. THey will be shoun in their original names (the ones used in `CREATE TABLE`), or they will be renamed to the aggregate function used with them. Also, you might see the `::NUMERIC`, we do this to cast/convert `MONEY` type into a number. Since we can't do arithmetic operations and use aggregate functions on `MONEY`

```sql
SELECT ROUND(AVG(car_price::NUMERIC)) -- => column name = round
```

---

## Dealing With Nullities in SQL

We use `COALESCE` to handle nulls inside a column. It's written inside the `SELECT` statement, and takes at least 2 arguments.

`COALESCE(column, default value if the value in the column is null, 2nd default value if the 1st defualt is null, ...)`

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

## Date & Time in PostgreSQL

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

### Adding and Subtracting Intervals to Dates

To add and subtract dates. We can user `INTERVAL`. Assume you want to find the date 20 years, 3 months and 9 days before/after now

```sql
SELECT (date_of_birth + INTERVAL '20 YEARS 3 MONTHS 9 DAYS')::DATE AS new_date
-- use `-` to find dates before, `+` to find dates after
FROM best_table
```

### Extract Date Fields

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

### Finding Intervals & Durations

We can find the duration between two dates using the `AGE` function. The `AGE` function takes 2 arguments

```sql
SELECT first_name,
  last_name,
  AGE(NOW(), date_of_birth) AS age,
  EXTRACT(YEAR FROM AGE(NOW(), date_of_birth)) AS age_years,
  EXTRACT(MONTH FROM AGE(NOW(), date_of_birth)) AS age_months
FROM best_table
ORDER BY age ASC;
  /* We can write the code without EXTRACT but it will show age in years, months, days
   and time counted from midnight */

  /*In MySQL, we can group age with year and months by using
  EXTRACT(YEAR_MONTH FROM AGE(...)) but it's not supported in postgres unfortunately*/
```

---

## Primary Keys

- Primary keys is a unique identifier for a record in a table
- Primary key can be a column or more in a table, but the table can't have multiple primary keys
  - For example, name & email can be 1 priamry key (the email can duplicate, the name can duplicate, but a specific name and email can't occur together more than once)

To add primary keys in the query, we either use the `PRIMARY KEY` operator when creating table. Or after creating the table we use this command

```sql
ALTER TABLE <table_name> ADD PRIMARY KEY(column1, column2, ...)
```

To add primary key when creating table for multiple columns

```sql
CREATE TABLE my_table(
  user_id BIGSERIAL NOT NULL,
  username VARCHAR(15) NOT NULL,
  email VARCAHR(50) NOT NULL
  PRIMARY KEY(user_id, username)
);
```

### Uniquness

- Primary keys are unique, but we can have unique values taht are not primary keys
- Emails are not a unique identifier (primary key), because they are nullable. But if they are not null, they must be unique

### Adding Constraints After Creating Tables

We talked about constraints like `UNQIUE`, `NOT NULL` and others above. But how to add a constraint to a created table? We simply use this query

```sql
ALTER TABLE my_table ADD CONSTRAINT optional_constraint_name UNIQUE(columns_name, ...)
```

- We can define a constraint name, or let postgres define it by default. This also applies for primary key

To remove constraints or key, we simply replace `ADD` with `DROP`

```sql
ALTER TABLE my_table DROP CONSTRAINT constraint_name
```

Let's use the check constraint

```sql
ALTER TABLE my_table ADD CONSTRAINT CHECK(gender = 'Male' OR gender = 'Female')
```

---

## CRUD Operations in SQL Records

CRUD stands for Create, Read, Update & Delete. To Create a record

- We use the `INSERT` keyword as we mentioned above

```sql
INSERT INTO table_name (
-- If you're using command line, make sure you connect to the database
c1_name,
c2_name,
c3_name,
...
)
VALUES (c1_value, c2_value, c3_value);

```

- To access/read records, we use the `SELECT` statement
- To update records, we use the `UPDATE... SET` statement

```sql
UPDATE table_name
SET
  column1 = value1,
  column2 = value2,
  ...
WHERE condition;
```

- Finally, to delete a record. We use `DELETE` operator

```sql
DELETE
FROM table_name
WHERE condition
```

The `WHERE` in updating and deleting is very important to prevent affecting all the records. It's a good practice to delete by primary key.
