# Oracle PL/SQL

## Data and database

`Data` - is anything that can be stored digitally.  
`Database` - is a program that stores an organized collection of data.  
`Why databases?` - It's efficient way to store, access, manipulate and retrieve the data.
___

## DBMS

`DBMS` - Database Management System, the collection of programs that allow users to use databases.  

DBMS types:

- `Hierarchical` DBMS (uses parent-child relationship storing, like tree with nodes and branches);
- `Network` DBMS (uses many-to-many relations)
- `Relational` DBMS (`Oracle`, `MySql`, `MsServer`) - have predefined type in it.
- `Object-Relational` DBMS (`Postgres`) - you can create your own types.

___

## Why Oracle DB?

- Security (many options, and its designed to enable you to create really secure code)
- Performance (have an optimizer, tuning options)
- Scalability
- Powerful coding aka PLSQL
- Support

___

## Pluggable database

`Pluggable database` or `pdb` means multitenant architecture.  
The main layer is called `Container database` or `root database` or `cdb1`.  

`Root` contains other databases:

- pdb1
- pdb2
- ...

> There must be at least **one `pdb` inside**.  
Each `pdb` has attributes of a regular database like `users`, `objects`, `tables` and etc.  
But `cdb` doesn't have these attributes, it has only `metadata` such configuration files...  
Previously you could deploy only 1 db on to a server.
___

## Database objects

Database objects are:

- Schema objects
- Non-schema objects

`Schema` - is a collection of logical structures of data or objects for each user.

Schema Objects:

- `table`
- `view`
- `constraints`
- `index`
- `sequence`
- `synonym`
- `materialized view`
- `functions`
- `procedures`
- `triggers`
- `packages`
- `database links`

`Table` - is the base unit of the DB to store data, formatted with columns and rows.

`View`- is a virtual table that provides access to a subset of columns or some rows from one or more tables, they don't use physical space.

`Constraints` - are the rules for restricting invalid data entry into tables.

`Indexes` - are used for improving speed of data retrieval from tables.

`Sequence` - generates unique integers.

`Synonym` - is an alternative name (alias) for the DB objects. (For example, John created `employees` table, by default to access it, you need to write it as `john.employees`. But if there is a public synonym to `employees` table, it can just called without `john` - so just `employees`).

`Materialized view` - it has a real table filled by an SQL query unlike the views. The real table is truncated and refilled with a specified time frequency.

`Functions` - return value;

`Procedures` - return nothing, just runs the script.

`Triggers` - are compiled program units stored in DB and executed with a specific event (like INSERT, UPDATE, DELETE, CREATE and etc). They are executing BEFORE, ON or AFTER the main operation.

`Packages` - are consist of SQL,PLSQL codes, variables, cursors, etc to perform one or more than one operation by using functions and procedures. Package consist of functions, procedures.

`Database links` - are the connections between two physical database servers.

Non-schema object:

- `directories`
- `roles`
- `tablespaces`
- `users`

> A user can have **only one schema**. So we can say `schemas` are `users`.  

`ER diagram` - is entity relationship diagram
___

## Oracle data types
>
> Column can have only 1 type of data.

Data types:

- `VARCHAR2(size)` - Variable-length character data. VARCHAR(100)==if data 10 chars long, it wont take 100 chars of space, only 10.
- `CHAR(size)` - Fixed-length character data. CHAR(100)==will allocate always 100 chars space.
- `NUMBER(p, s)` - Variable-length numeric data. p -> precision, s -> scale. The max precision is 38 digits.
- `DATE`
- `LONG` - Variable-length character data up to 2GB. Not recommended to use.
- `RAW` and `LONG RAW` - Raw binary data. Not recommended to use.
- `BLOB` - Binary LOB (Large OBjects) for images, sounds, videos,
- `CLOB` - Character LOB, it's more flexible than LONG and it has better a faster algorithm for usage
- `BFILE` or `External LOB` - Binary data stored in an external file (up to 4 gigs), so it's just an external file in the OS. READ ONLY type.
- `ROWID` - It's base64 number and represents a physical unique address of a row in a table

## Retrieving data

> Null rows will not be counted.
___

`DESCRIBE`  
The command `DESCRIBE` or `DESC` shows you structure of a table/view/procedure.
Instead of using `DESC`, we can use `SHIFT+F4` to open describing window in `SQL Developer`.
___

`INFORMATION`  
The command `INFORMATION` or `INFO` or `INFO+` shows detailed structure of a table/view/procedure.
___

`SELECT`  
The command `SELECT` is used to get only the needed columns, don't query * (all) the columns, it degrades the performance.
___

`QUOTE operator`

```code
-- Use single quotes for strings!
select 'My Name is Adam' as "Quote operator" from dual;

-- Escape ' with another '
select 'I''m using quote operator in SQL statements' as "Quote Operator" from dual;

-- Or just use a quote operator
select q'[I'm using quote operator in SQL statements]' as "Quote Operator" from dual;

-- instead of [ and ] we can use any other symbol, like < and > or even d
select q'<I'm using quote operator in SQL statements>' as "Quote Operator" from dual;
select q'dI'm using quote operator in SQL statementsd' as "Quote Operator" from dual;
```

___

`DISTINCT` or `UNIQUE`  
To remove duplicates.  
UNIQUE is the same as DISTINCT. It's for OLD versions for support reasons.  
Can be used for only ONE column and only for the FIRST column!

```sql
select distinct JOB_ID from EMPLOYEES;
```

___

`SYSDATE`  
Current date of OS
___

`WHERE`  

```sql
SELECT * FROM employees WHERE hire_date > '01-JUN-05';
SELECT * FROM employees WHERE hire_date = '21-MAY-07';
```

___

`BETWEEN ... AND ...`  
> both values are INCLUDED!  

```sql
SELECT * FROM employees WHERE salary BETWEEN 10000 AND 14000;
SELECT * FROM employees WHERE hire_date BETWEEN '07-JUN-02' AND '29-JAN-08';
```

___

`Rules of precedence`:

1. Arithmetic
2. Concatenation
3. Comparison
4. IS NULL, LIKE, IN
5. BETWEEN
6. !=
7. NOT operator with conditions
8. AND
9. OR

___

`ORDER BY`  
Sort result by columns
> `NULL`s will be displayed at the `END` when `ASC`  
> `NULL`s will be displayed at the `START` when `DESC`

```sql
SELECT 
    first_name, 
    last_name, 
    salary, 
    (10*(salary/5) + 3000) - 100 NEW_SALARY 
FROM 
    employees 
ORDER BY 
    NEW_SALARY
;

SELECT * FROM employees ORDER BY first_name, job_id, salary;
```

___

`ORDER BY ... NULLS FIRST or NULLS LAST`  
Control the order of the NULL values when sorting.

```sql
select first_name, salary, commission_pct from employees 
order by commission_pct;

select first_name, salary, commission_pct from employees 
order by commission_pct NULLS FIRST;

select first_name, salary, commission_pct from employees 
order by commission_pct ASC NULLS FIRST;

select first_name, salary, commission_pct from employees 
order by commission_pct DESC;

select first_name, salary, commission_pct from employees 
order by commission_pct DESC NULLS LAST;
```

___

`ROWID` (looks like `AAARzuAAEAAAIwdAAA`)  
It's a unique ID that contains the physical address of a row. Oracle automatically generates it when inserting. This ID gives information to find disk number, cylinder, block number and block offset.  
Using `ROWID` is the **FASTEST** way to access a row! `ROWID` is permanent.
___

`ROWNUM` (looks like `2`)  
It's a pseudocolumn when showing results, used to limit the number of rows returned in a query. `ROWNUM` is **temporary**, so if you change the query, the `ROWNUM` will be different for a row.

```sql
SELECT employee_id, first_name, last_name, salary, rowid, rownum from employees;
```

___

`FETCH (LIMIT)`  

```sql
[OFFSET rows_to_skip ROW[S]]
FETCH [FIRST|NEXT] [row_count | percent PERCENT] ROW[S] [ONLY | WITH TIES]
```

`OFFSET` - number of rows to SKIP BEFORE the row LIMITING starts.  
`ROW|ROWS` - use any, only for semantic.  
`FETCH` - number or percentage of rows to return.  
`FIRST|NEXT` - use any, only for semantic.  
`ONLY|WITH TIES` - ONLY returns the exact amount of rows. TIES returns extra rows for the last row sorted by ORDER BY.  

```sql
SELECT first_name, last_name, salary FROM employees 
ORDER BY salary DESC
OFFSET 1 ROW FETCH FIRST 10 ROWS ONLY;
```

___

`Substitution variables` - `&`  
If want to make a window prompt to enter the value, just for ease of typing, no benefit in performance.  
You can do it in `SQL Developer` or in `SQL*Plus`.  
`SQL*Plus` will show you verification lines.  

```sql
SELECT employee_id, first_name, last_name, &column_name
FROM &table_name
WHERE &condition
ORDER BY &order_by_clause;

SELECT employee_id, first_name, last_name, department_id
FROM employees 
WHERE department_id = &department_no;

SELECT employee_id, first_name, last_name, department_id
FROM employees 
WHERE first_name = '&name';
```

___

`SQL*Plus`  
Usage in console:

```bash
sqlplus username/password@orclpdb
sqlplus hr/hr@pluggable_db_name
```

___

`DEFINE` and `UNDEFINE`  

```sql
SELECT employee_id, first_name, last_name, salary
FROM employees 
WHERE salary BETWEEN &sal AND &sal + 1000; 

SELECT employee_id, first_name, last_name, salary
FROM employees 
WHERE salary BETWEEN &&sal AND &sal + 1000; 

SELECT employee_id, first_name, last_name, &&column_name 
FROM employees
ORDER BY &column_name;

SELECT &&column_name 
FROM employees
GROUP BY &column_name
ORDER BY &column_name;

DEFINE emp_num = 100;
SELECT * FROM employees WHERE employee_id = &emp_num;

DEFINE emp_num = 200;
DEFINE column_name = 'first_name';

UNDEFINE emp_num;

DEFINE;
DEFINE column_name;

-- short names
UNDEF column_name;
DEF column_name; 
```

___

`ACCEPT` and `PROMPT`  
The most reliable and robust method for getting input from the user is to explicitly prompt for values using the ACCEPT and PROMPT commands.
ACCEPT emp_id PROMPT 'Please Enter an Employee ID:';

`ACCEPT` takes input from the user and stores it in a user variable.  
`PROMPT` is used to display a message to the user for supplying a brief explanation of what your script is going to accomplish.
The variable is stored until this session is terminated.
___

`SET VERIFY ON` and `SET VERIFY OFF`  
`VERIFY` - displays the status of the variable before and after the substitution.
So `VERIFY` does the same verification as in `SQL PLUS`.
___

`SET DEFINE OFF`
> By default SET DEFINE is ON!
Will be needed to use & in strings, otherwise it will prompt for a variable value.

```sql
SELECT * FROM departments WHERE department_name = 'R&D';
```

## Functions or Stored Functions

Functions are created for manipulating the data and returning a value.
Functions must be created before calling them, otherwise:
    `ERROR: Function has not been defined yet!`

In Oracle there are 2 types of functions:

- `Single-Row` functions;
- `Multiple-Row` functions or Group functions;

`Single-Row` functions - receive only one input and return output.  
`Multiple-Row` functions or Group functions - receive more than 1 row of input and return output.

`Single-Row` functions types:

- Character - receive character values, return character of numeric values.
- Numeric - receive numeric values, return numeric values.
- Date - receive date values, return date values.
- General - convert one data type to another
- Conversion - receive any type of data, but mainly they are for dealing with `NULL`s.

> IMPORTANT: In Oracle indexes are starting from 1!
___

Character functions:

- Case conversion:
  - `UPPER('Adam SMITH')` -> `"ADAM SMITH"`
  - `LOWER('Adam SMITH')` -> `"adam smith"`
  - `INITCAP('Adam SMITH')` -> `"Adam Smith"`
- Character manipulation:
  - `SUBSTR()` get a substring by positions, example:
    - `SUBSTR('Sql Course', 1, 3)` -> `'Sql'`
    - `SUBSTR('Hi', 3, 6)` -> `NULL`
  - `LENGTH()` get length, example:
    - `LENGTH('Sql Course')` -> `10`
  - `CONCAT()` concatenate only TWO strings, example:
    - `CONCAT('Sql', 'Course')` -> `'SqlCourse'`
  - `INSRT()` return position if the substring is in the string, example:
    - `INSRT('Sql Course', 'o')` -> `6`
  - `TRIM()` remove spaces or other symbol from the START and from the END of the string, example:
    - `TRIM('  Sql Course   ')` -> `'Sql Course'`
  - `LTRIM()` remove spaces or other symbols from the START of the string, example:
    - `LTRIM('  Sql Course   ')` -> `'Sql Course   '`
  - `RTRIM()` remove spaces or other symbols from the END of the string, example:
    - `RTRIM('  Sql Course   ')` -> `'  Sql Course'`
  - `REPLACE()` replace the found substring with emptiness or with the specified string, example:
    - `REPLACE('Sql Course', 's', '*')` -> `'Sql Cour*e'`
  - `LPAD()` add symbol to the START till the limit value or truncates the long string to the limit value length, example:
    - `LPAD('sql', 10, '-')` -> `'-------sql'`
  - `RPAD()` add symbol to the END till the limit value or truncates the long string to the limit value length,  example:
    - `RPAD('sql', 10, '-')` -> `'sql-------'`

    ```sql
    -- INSTR
    SELECT INSTR('I am learning how to use functions in Oracle', 'o', 17, 3) FROM dual; 
    SELECT INSTR('I am learning how to use functions in Oracle', 'o', 1, 3) FROM dual;
    SELECT INSTR('I am learning how to use functions in Oracle', 'o', -1, 3) FROM dual;
    SELECT INSTR('I am learning how to use functions in Oracle', 'o', -1, 1) FROM dual;
    SELECT INSTR('I am learning how to use functions in Oracle', 'in', -1, 1) FROM dual;
    SELECT INSTR('I am learning how to use functions in Oracle', 'in', 1, 1) FROM dual;
    SELECT first_name, INSTR(first_name,'a') from employees;

    -- TRIM
    SELECT TRIM ('     My Name is Adam   ') trimmed_text from dual;
    SELECT TRIM (' ' FROM '     My Name is Adam   ') trimmed_text from dual;
    SELECT TRIM (BOTH ' ' FROM '     My Name is Adam   ') trimmed_text from dual;
    SELECT TRIM (LEADING ' ' FROM '     My Name is Adam   ') trimmed_text from dual;
    SELECT TRIM (TRAILING ' ' FROM '     My Name is Adam   ') trimmed_text from dual;
    SELECT TRIM (TRAILING 'm' FROM '     my Name is Adam   ') trimmed_text from dual;
    SELECT TRIM (TRAILING 'm' FROM 'my Name is Adam') trimmed_text from dual;
    SELECT TRIM (TRAILING 'm' FROM 'my Name is Adammmmm') trimmed_text from dual;
    SELECT TRIM (LEADING 'm' FROM 'my Name is Adam') trimmed_text from dual;
    SELECT TRIM (BOTH 'm' FROM 'my Name is Adam') trimmed_text from dual;
    SELECT TRIM ('m' FROM 'my Name is Adam') trimmed_text from dual;
    SELECT TRIM ('m' FROM 'my Name is Ada') trimmed_text from dual;
    SELECT TRIM (TRAILING 'm' FROM 'my Name is Ada') trimmed_text from dual;
    SELECT TRIM (TRAILING 'my' FROM 'my Name is Ada') trimmed_text from dual;s
    
    -- RTRIM and LTRIM
    SELECT RTRIM ('  my Name is Adam  ') r_trimmed_text from dual;
    SELECT LTRIM ('  my Name is Adam  ') l_trimmed_text from dual;
    SELECT LTRIM ('my Name is Adam', 'my') l_trimmed_text from dual;
    SELECT RTRIM ('my Name is Adam', 'my') r_trimmed_text from dual;
    SELECT RTRIM ('my Name is Adammmm', 'my') r_trimmed_text from dual;
    SELECT LTRIM ('www.yourwebsite.com', 'w.') l_trimmed_text from dual;
    SELECT RTRIM(LTRIM('www.yourwebsitename.com', 'w.'),'.com') trimmed_text from dual;
    SELECT LTRIM('1237982434www.yourwebsitename.com', '0123456789') trimmed_text from dual;

    -- REPLACE
    SELECT first_name, REPLACE(first_name,'a') rpl FROM employees;
    SELECT first_name, REPLACE(first_name,'a','-') rpl FROM employees;
    SELECT first_name, REPLACE(first_name,'le','-') rpl FROM employees;
    SELECT first_name, REPLACE(first_name,'und','-') rpl FROM employees;

    -- LPAD and RPAD
    SELECT first_name, lpad(first_name,10,'*') pad FROM employees;
    SELECT first_name, rpad(first_name,10,'*') pad FROM employees;
    SELECT first_name, rpad(first_name,6,'*') pad FROM employees;
    SELECT first_name, lpad(first_name,6,'*') pad FROM employees;
    SELECT first_name, lpad('My name is ',20,'-') pad FROM employees;
    SELECT first_name, lpad('My name is '||last_name ,20,'-') pad FROM employees;
    ```

___

Numeric functions:

- `ROUND()` takes in a number and rounds it to the specified number of decimal places.
- `TRUNC()` truncates values to the specified number of decimal places.
- `CEIL()` rounds UP.
- `FLOOR()` rounds DOWN.
- `MOD()` remainder of division.
