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

You might see in the above query the word `AS`. It's for aliasing. Simply renaming the column. If I didn't rename them. They will be shoun in their original names (the ones used in `CREATE TABLE`), or they will be renamed to the aggregate function used with them. Also, you might see the `::NUMERIC`, we do this to cast/convert `MONEY` type into a number. Since we can't do arithmetic operations and use aggregate functions on `MONEY`

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
  - In databases, we refer to table rows as records, and table columns as fields. We will use this terminology a lot
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

---

## Conflict Handling

Suppose you want to add a new record with a primary key that exists

```sql
INSERT INTO best_table (
	"id",
	first_name,
	last_name,
	gender,
	email,
	date_of_birth,
	country
)
VALUES (
	911,
	'f_n',
	'l_n',
	'Female',
	'e@ma.il',
	DATE '2000-09-21',
	'Finland'
);
```

You'll get a duplicate key error since the id 911 is the primary key and it already exists in `best_table`. To handle such a case. We add a new handler `ON CONFLICT(c1,c2,...[optional input]) DO NOTHING`

```sql
INSERT INTO best_table (
	"id",
	first_name,
	last_name,
	gender,
	email,
	date_of_birth,
	country
)
VALUES (
	911,
	'f_n',
	'l_n',
	'Female',
	'e@ma.il',
	DATE '2000-09-21',
	'Finland'
)
ON CONFLICT DO NOTHING;
-- INSERT 0 0 => no insert happen
```

You may wish to update the record with the new record. Here, we introduce new keyword `EXCLUDED.<column_name>`

Assume we want to update the first name, last name and email for the above example

```sql
INSERT INTO best_table (
	"id",
	first_name,
	last_name,
	gender,
	email,
	date_of_birth,
	country
)
VALUES (
	911,
	'f_n',
	'l_n',
	'Female',
	'e@ma.il',
	DATE '2000-09-21',
	'Finland'
)
ON CONFLICT(id) DO UPDATE SET -- No parantheses
  first_name = EXCLUDED.first_name,
  last_name = EXCLUDED.last_name,
  email = EXCLUDED.email
-- INSERT 0 1
```

Note that the input is completely optional in `ON CONFLICT DO NOTHING`. While it's mandatory in `ON CONFLICT(column) DO UPDATE`

Updating on conflict is also referred to as upserting

---

## Foreign Keys, Relations & Joins

Assume we have 2 tables

1. Users

- user_id
- user_name
- user_email
- user_phone

2. Cars

- car_id
- car_make
- car_model

Now, before we display both tables at once. We need to define what's the relation between them

- A person can have only one car, and the car belongs only to one person (one-to-one relationship)
- A person can have multiple cars, and each car belongs to only one person (one-to-many relationship)
- A person can have multiple cars, and each car can be used by multiple people (many-to-many relationship)

Here, you might think that why don't we merge the 2 columns and thank you that's it. but this is a bad design as it's resourceful (takes much memory), it also doesn't refer which car for which person.

To make the best joins or displaying both tables at once, we use the forign keys. This in other words, means, we add an extra column in the first table which is a primary key in the second table. This column is called Foreign Key. The purpose for foreign keys is to refrence tables and link them together

### Differences between foreign key and other table's primary key

Assume we want to create a table called Cars, that will have a one-to-one relationship with the table Users

```sql
CREATE TABLE Cars (
  car_id BIGSERIAL NOT NULL PRIMARY KEY,
  car_make VARCHAR(50) NOT NULL,
  car_model VARCHAR(50) NOT NULL,
  car_price NUMERIC(10,2) NOT NULL
);
```

Now, to link the car with the `Users` table. We must understand that the foreign key's constraints is only defined in the table where it's a primary key. In other words, we will add `car_id` as a foreign key in `Users` table. But all the constraints related to this id should be defined only in `Cars` table. Let's implement

```sql
CREATE TABLE Users (
  "id" BIGSERIAL NOT NULL PRIMARY KEY,
	first_name VARCHAR(50) NOT NULL,
	last_name VARCHAR(50) NOT NULL,
	gender VARCHAR(20) NOT NULL,
	email VARCHAR(50),
	date_of_birth DATE NOT NULL,
	country VARCHAR(50) NOT NULL,
  car_id BIGINT REFERENCES Cars(car_id) UNIQUE
)
```

Let's consider the last line a bit. Look that we didn't put `NOT NULL`, that's because tere might be a person who doesn't have a car. Also, we defined the data type as a `BIGINT` and not a `BIGSERIAL`. That's because `BIGSERIAL` means that the value will be incremented. That's true but the incrementation will take place in the `Cars` table not in the `Users` one. The `BIGINT` will make sure to grab the same data type from the table but without incrementing inaside ther `Users` table as we mentioned. Finally, the `UNQIUE` constraint is just a double check for the values, it's a safety valve.

It's important to note that the `Users` table is depending on the `Cars` table by referencing to it, and not the opposite. So, when writing the executable query, we make sure that the `Cars` table is created first. Otherwise, there will be no references in the `Users` table and we will get an error.

### Start making relations!

Now, let's link tables before joining them

```sql
UPDATE <table_with_foreign_key>
SET <foreign_key_column> = <foreign_key_value>
WHERE <primary_key_column> = <primary_key_value>
```

Let's put into action, remember that the `Users` table has a foreign key `car_id`, which is a primary key in `Cars` table

```sql
UPDATE Users
SET car_id = 1
WHERE user_id = 3
-- user with user_id = 3 is now having the car with car_id = 1
```

---

## Joins

### Inner Joins

Consider the same two tables `Users` (which is the same as `best_table`) and `Cars` (which is the same as `car_table`). Now, assume we want to extract all data for only the people who have cars (i.e. people that share the same key(s) in both tables). The key can be foreign in both tables, or primary at one table and foreign in other table. To extract the records that have keys in both tables. We use `INNER JOIN`

The syntax is simple `INNER JOIN second_table ON first_table.key = second.table.key`. Also, in the `FROM` statement, we don't mentiion the second table. A general syntax will be something like this

```sql
SELECT first_table_columns, second_table_columns
FROM first_table
INNER JOIN second_table ON first_table.key = second_table.key
```

If the keys have the same name, we can use `USING(key_name)`

Another important information is that the joining must occur before the `WHERE` condition;

Now, let's get it work

```sql
SELECT
  first_name,
  last_name,
  best_table.car_id,
  -- if not specifying table will throw error because column name is same in both
  company,
  model
  /*company and model are from "car_table" but we don't
  mention it in "FROM", They will be retrieved by join */
FROM best_table
INNER JOIN car_table ON best_table.car_id = car_table.car_id
```

To demonstrate it in another example, assume we have 2 tables

```
table 1
--------|--------
name    | car_id
--------|--------
Rashed  | 3
--------|--------
Lubna   | 5


======


table 2
--------|--------
car_id  | model
--------|--------
1       | Sunny
--------|--------
3       | Accent
```

The `INNER JOIN` will show rows with common keys only

```
inner join table
--------|---------|--------
name    | car_id  | model
--------|---------|--------
Rashed  | 3       | Accent
```

Note that car_id = 1 didn't appear because there is no name associated with it. The same thing occus with Lubna since there is no car_id = 5. We will use the same example for other joining types

### Left & Right Joins

Inner joins merge & display only records that have keys in both tables. Left joins focus on displaying and merging all the records from the first table. If there is a common key between the tables, they will appear as normal. Otherwise, the values will be null.

The left join will show all records from table 1, and common records from table 2. Everything else will be null. Let's apply it for the same tables above

```
left join table
--------|---------|--------
name    | car_id  | model
--------|---------|--------
Rashed  | 3       | Accent
--------|---------|--------
Lubna   | 5       | NULL
```

Note that table 1 appears as it is, but table 2 only shows the records of common cars only (`car_id = 3`), that's why the `car_id = 1` is not shown (because it's not common between 2 tables). While the `car_id = 5` is `NULL` because it's not in the second table

The right join is very similar to the left join, the only difference is that the second table and the first table are swapped. The second table appears as it is, the first table only appears the common records. In simple words, the following 2 queries are equivalent

```sql
-- QUERY 1
SELECT first_name, last_name, best_table.car_id, company, model
FROM best_table
RIGHT JOIN car_table ON best_table.car_id = car_table.car_id

-- QUERY 2
SELECT first_name, last_name, best_table.car_id, company, model
FROM car_table
LEFT JOIN best_table ON best_table.car_id = car_table.car_id
```

Note that the tables name are changed in the `FROM` statement. Let's apply it again to the same tables above to make sure we understand it correctly

```
right join table
--------|---------|--------
name    | car_id  | model
--------|---------|--------
NULL    | 1       | Sunny
--------|---------|--------
Rashed  | 3       | Accent
```

The last type of joining is the `FULL OUTER JOIN`. In this type, all records from both tables will appear no matter what. Let's apply it again to the same tables

```
full outer join table
--------|---------|--------
name    | car_id  | model
--------|---------|--------
NULL    | 1       | Sunny
--------|---------|--------
Rashed  | 3       | Accent
--------|---------|--------
Lubna   | 5       | NULL
```

We can consider the `FULL OUTER JOIN` as a unioin of the left and right joins

## Deleting Records That Are Foreign Keys In Other Tables

Assume we want to delete the `car_id = 3` that's owned by Rashed. When trying to delete such a record. You'll face an error because the car id is used in other table. To do this, you should first assign the foreign key (`car_id` in the first table) to null or something else, then safely delete the `car_id` from the second table. You can even delete the whole record from the first table but it's not a good practice.

## Storing Query Data as a CSV File

From the psql terminal, you can use the following command

```sql
\copy (<SQL_Command>) TO 'path/to/file/name.csv' DELIMITER ',' CSV HEADER;
-- Delimiter in CSV is alwaye ",". Header means the table will include columns' names
```

Or you can click on the download icon in pgAdmin 4 that will make your life easier. I added a sample file in the attachments

---

## Serials & Sequences

We were defining our ids as a bigserial type. The `BIGSERIAL` is the same as a `BIGINT` (and by the way: `BIGINT` is the same as `INT8`) but it's auto incrementing. We can access more about the squence information by following these steps:

1. Enter the `psql` mode using either the command line, or by right click on the database name from `pgAdmin` and choosling `psql tool`
2. [If you're using the command line only] connect ot database, if you're using pgAdmin, skip this step
3. Visualize the tablles by entering the following command

```bash
\d
```

After this tep, you'll find every table duplicated twice, one as the original table and one as a sequence

```
List of relations
 Schema |         Name         |   Type   |  Owner
--------+----------------------+----------+----------
 public | best_table           | table    | postgres
 public | best_table_id_seq    | sequence | postgres
 public | car_table            | table    | postgres
 public | car_table_car_id_seq | sequence | postgres
(4 rows)
```

Now, let's try to explore the sequence tables

```sql
SELECT * FROM best_table_id_seq
```

You can use either the psql tool, the query tool or even the command line for the above command

You'll find 3 columns (`last_value`, `log_cnt` & `is_called`). The last value is the last value the id has created. The `log_cnt` is the number of `ids` chched. In postgerSQL, some values are cached for the next value retrieval, it's 32 by default. When `nextval` fucntion is called (`nextval is explained later in this section`). This count will decrease until 0, then it will take 32 and decrease again. I will explain that later on.

The `is_called` is whether the sequence is ever called (`t`) or not (`f`).

In our case, if you inserted the table like me using the `best_table.sql` file. You'll get the `last_value = 1`, `log_cnt = 0` & `is_called = 'f'`. This is because we defined all the `id`s ourselves, we didn't let the sequence generate ids for us. It's important to understand that the `last_value` & `log_cnt` are not necessarily a true reflection of the table length

Let's go into any [normal] table and explore it

```bash
\d best_table
```

The output will be something like this

```
                                      Table "public.best_table"
    Column     |          Type          | Collation | Nullable |                Default
---------------+------------------------+-----------+----------+----------------------------------------
 id            | bigint                 |           | not null | nextval('best_table_id_seq'::regclass)
 first_name    | character varying(50)  |           | not null |
 last_name     | character varying(50)  |           | not null |
 email         | character varying(100) |           |          |
 gender        | character varying(15)  |           | not null |
 country       | character varying(50)  |           | not null |
 date_of_birth | date                   |           | not null |
 car_id        | bigint                 |           |          |
Indexes:
    "best_table_pkey" PRIMARY KEY, btree (id)
Foreign-key constraints:
    "best_table_car_id_fkey" FOREIGN KEY (car_id) REFERENCES car_table(car_id)
```

You find that at the `id`, there is a default value `nextval('best_table_id_seq'::regclass)`. Let's use it

```sql
SELECT nextval('best_table_id_seq'::regclass);
-- We will get 1
-- If we run again, we will get 2, then 3, and so on...
```

Here, it's important to discuss what happened. We used `SELECT nextval('sequence_table_name')`, we can cast it to type `regclass` optionally. The `nextval` function will output the next value of the sequence, each time you run it it will increment. A similar function to it is the `currval` that will return the curernt value in a sequence. The `currval` doesn't increment like the `nextval`. To understand the `log_cnt` and how it's affected by `nextval`. See [this question on Stack Overflow](https://stackoverflow.com/questions/66456952/what-does-log-cnt-mean-in-the-postgres-sequence)

```sql
SELECT nextval('best_table_id_seq');
-- We will get 2

SELECT currval('best_table_id_seq');
-- We will get 2 also
```

If you want to get the last value without advancing the sequence, use `lastval` function, it takes no input. You can optionally specify the table, but since the sequence is in one relation, it will output the sequence for the whole database.

```sql
SELECT lastval();
```

The context of sequences will be more cleared by inserting records into our tables

```sql
INSERT INTO best_table
(first_name, last_name, gender, country, date_of_birth)
VALUES ('f_n', 'l_n', 'Male', 'Egypt', DATE '2000-10-02')
```

We will get an error because the `currval` is around 5 or 6, it will be a `duplicate key` error. To adjust the sequence value to the latest column, we will use another function `setval`. The syntax is similar.

```sql
SELECT setval('best_table_id_seq', (
  -- Find the maximum ID in your table
  SELECT max("id")
  FROM best_table
  )
);
```

We can instead use `ALTER SEQUENCE` to restart the sequence count,

```sql
SELECT "id"
FROM best_table
ORDER by "id" DESC
LIMIT 1
-- get the number of last id, in our case it's 1000

ALTER SEQUENCE best_table_id_seq RESTART WITH 1000;
```

Then, we insert our values as normal

---

## PostgreSQL Extensions

PostgreSQL as an open-source database has extensions, to have access to each extension with its brief, we write the followign command

```sql
SELECT * FROM pg_available_extensions;
```

To install an extension, we run the `CREATE EXTENSION` statement

```sql
CREATE EXTENSION IF NOT EXISTS "uuid-ossp"
```

To gain access to functions provided by extensions, we run this command in the `psql` view

```bash
\df
```

We got

```
                                List of functions
 Schema |        Name        | Result data type |    Argument data types    | Type
--------+--------------------+------------------+---------------------------+------
 public | uuid_generate_v1   | uuid             |                           | func
 public | uuid_generate_v1mc | uuid             |                           | func
 public | uuid_generate_v3   | uuid             | namespace uuid, name text | func
 public | uuid_generate_v4   | uuid             |                           | func
 public | uuid_generate_v5   | uuid             | namespace uuid, name text | func
 public | uuid_nil           | uuid             |                           | func
 public | uuid_ns_dns        | uuid             |                           | func
 public | uuid_ns_oid        | uuid             |                           | func
 public | uuid_ns_url        | uuid             |                           | func
 public | uuid_ns_x500       | uuid             |                           | func
(10 rows)
```

## UUID

UUID is Universally Unique ID, which is an id that's completely unique in the whole world. There are versions of UUID, we will be interested in UUIDv4 (random id). UUIDs are good to use as primary keys as they are secure and ensuring no collsiions during migrations to other databases.

When creating a table with UUID, we specify the primary key with UUID type, then we explicitly set the value to equal the function `uuid_generate_v4()`. Some functions like `uuid_generate_v5` requires arguments so we enter them, but our function doesn't require any imputs. **Check the UUID folder, it has the UUID version of the table creation sql files**

```sql
-- CARS TABLE
create table car_table (
	car_uuid UUID NOT NULL PRIMARY KEY,
	company VARCHAR(50) NOT NULL,
	model VARCHAR(50) NOT NULL,
	car_price MONEY NOT NULL,
	model_year INT NOT NULL
);
insert into car_table (car_uuid, company, model, car_price, model_year) values (uuid_geenrate_v4(), 'Infiniti', 'EX', '$95398.42', 2012);
insert into car_table (car_uuid, company, model, car_price, model_year) values (uuid_geenrate_v4(), 'Mazda', 'MX-5', '$28196.82', 2012);

--------------------------------------------

-- USERS TABLE
create table best_table (
	user_uuid UUID NOT NULL PRIMARY KEY,
	first_name VARCHAR(50) NOT NULL,
	last_name VARCHAR(50) NOT NULL,
	email VARCHAR(100),
	gender CHAR(20) NOT NULL,
	country VARCHAR(50) NOT NULL,
	date_of_birth DATE NOT NULL,
	car_uuid REFERENCES car_table.car_uuid UNIQUE
);

INSERT INTO best_table (user_uuid, first_name, last_name, email, gender, country, date_of_birth) VALUES (uuid_generate_v4(), 'Lilyan', 'Marlow', 'lmarlow0@forbes.com', 'Female', 'Philippines', '2002-06-17');
INSERT INTO best_table (user_uuid, first_name, last_name, email, gender, country, date_of_birth) VALUES (uuid_generate_v4(), 'Chaunce', 'Nancarrow', 'cnancarrow1@godaddy.com', 'Male', 'Thailand', '1973-12-03');
```
