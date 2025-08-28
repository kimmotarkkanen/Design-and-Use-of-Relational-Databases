# Content and goals

This tutorial covers implementing a database and managing the data. In the previous tutorial, you learned basics of how to query data in the database.

**SQL DDL** is Data Definition Language that is used to define data structures. For example, CREATE TABLE and ALTER TABLE are commands in SQL for creating and modifying tables and their columns. **SQL DML** is Data Manipulation Language that is used to manipulate data itself. For example, INSERT, UPDATE, and DELETE are commands in SQL for adding new or modifying existing data values.

Finally, **SQL VIEWs** are discussed, which can be thought of as virtual tables that combine information from several tables in the database.

This tutorial practices the use of SQL statements:

- DDL statements: CREATE, ALTER, DROP
- Primary key and foreign key definitions in tables
- Constraints and checks
- DML statements: INSERT, UPDATE and DELETE
- Creating views
Due to our educational platform, executing DDL commands are limited.

Our database management system in Ville is called SQLite. It is much used all over the world, and you can find more information about its specification here: [https://www.sqlite.org/](https://www.sqlite.org/). Very informative tutorial to SQLite specific commands is here: [https://www.sqlitetutorial.net/](https://www.sqlitetutorial.net/). However, we try to use as much standard SQL as possible. Very good source for more standard SQL commands (although with MySQL, Microsoft and Oracle emphasis) is here: [https://www.w3schools.com/sql/default.asp](https://www.w3schools.com/sql/default.asp)



# SQL DDL statements

To implement a relational database, we need to select DBMS and execute SQL DDL (Data Definition Language) statements.

**DDL: Data Definition Language**

SQL DDL allows database objects such as databases, domains, tables, views, and indexes **to be created, destroyed and modified**. There are three commands

- **CREATE**

	
		Create new database, table, view or index
- **DROP**
	
		Delete database, table, view, index
- **ALTER**
	
		Change column-level definitions: add, modify or delete columns, primary and foreign keys, constraints, triggers, checks and defaults etc.
Note! These statements are **not to manage data values** (like INSERT, UPDATE and DELETE). Yet, dropping a whole table deletes all its data as well!

**Main SQL DDL statements are:**

CREATE DATABASE,

DROP DATABASE

CREATE TABLE,

DROP TABLE,

ALTER TABLE

CREATE VIEW,

DROP VIEW,

CREATE INDEX,

DROP INDEX

## CREATE DATABASE and DROP DATABASE

**CREATE DATABASE**

This statement is used to create a database with unique name.

General format:

CREATE DATABASE databasename;

**Example:** Create an empty database called Company

CREATE DATABASE Company;

**Example:** Create database with table definitions.

CREATE DATABASE Company **** (*all table and column definitions inside the brackets*);

See detailed description for table and column definitions in CREATE TABLE section.

Note! SQLite do not implement the above command related to database creation. In SQLite, a database is created as a file.

**DROP DATABASE**

This statement is used to delete a database. It deletes all tables and data.

General format:

DROP DATABASE *databasename*;

**Example:** Delete a database called Company.

DROP DATABASE IF EXISTS Company;

Above we use optional clause IF EXISTS, which indicates that no errors are raised if the Company database doesn’t exist.

Foreign keys may restrict deletion (see the referential integrity) and these should be disabled first.

In SQLite, we would write for example:

PRAGMA foreign_keys = OFF

DROP DATABASE IF EXISTS Company;

Again, exact commands depend on your DBMS in use.

## CREATE TABLE

**CREATE TABLE**

This command is used for creating tables and defining column data types and other criteria inside the table. The most simple version of the command is the following.

**General format:**

CREATE TABLE tablename (

ColumnName1 datatype,

ColumnName2 datatype,

…

ColumnNameN datatype,

);

**Example**: Create a table called Works_on to the Company database

CREATE TABLE Works_on (

Essn INTEGER,

Pno INTEGER,

Hours REAL

);

Above, we define columns with data types as planned in the relational model. Column definitions are separated with comma. Note, that data types can have different names in different database systems and not all types are implemented in all DBMSs. For example, SQLite implements only five types (see e.g. [https://www.sqlite.org/datatype3.html](https://www.sqlite.org/datatype3.html)) while MySQL has much more (see e.g. [https://www.techonthenet.com/mysql/tables/create_table.php](https://www.techonthenet.com/mysql/tables/create_table.php)).

Some of the most used data types are:

- VARCHAR(*lenght*)

		
			For text content
			Alternatives: TEXT, STRING, CHAR
		
		
		INTEGER (*size)*
		
			For positive or negative natural numbers
			Alternatives: INT
		
		
		DECIMAL (*max. total length*, *no. of decimals on the right*)
		
			For numeric values with decimals
			Alternatives: NUMERIC, FLOAT, DOUBLE, REAL
		
		
		DATETIME
		
			For recording the date and time of event type of objects.
			Alternatives: DATE, TIME
Above, the simple example misses, among others, important primary and foreign key definitions of columns (essn and pno are both primary and foreign keys). There are several additional definitions for columns which are discussed next. We enhance the general format of CREATE TABLE as follows:

**General format:**

CREATE TABLE TableName (

{ColumnName dataType [NOT NULL] [UNIQUE] [DEFAULT defaultOption] [CHECK searchCondition] [, ...]}

[PRIMARY KEY (listOfColumns),]

{[UNIQUE (listOfColumns),] […,]}

{[FOREIGN KEY (listOfFKColumns)

REFERENCES ParentTableName [(listOfCKColumns)],

[ON UPDATE referentialAction]

[ON DELETE referentialAction ]] [,…]}

{[CHECK (searchCondition)] [,…] }

);

**Example**: Create a table called Works_on to the Company database. Define the primary key and set default hours as 0 and a check that hours must not be less than 0.

CREATE TABLE Works_on (

Essn INTEGER NOT NULL,
Pno INTEGER NOT NULL,
Hours REAL NOT NULL DEFAULT 0,
PRIMARY KEY (essn, pno),
CHECK (Hours >= 0)

);

Above, we define all columns as NOT NULL meaning that the value must always exist. Primary key columns are always defined as NOT NULL. Then DB engine rejects any attempt to insert a null value in the key column. With CHECK, we can define a CONSTRAINT to a column value. However, use checks and other constraints, triggers and transactions sparingly at the database structure level. Consider if such logic is better to implement with higher level programming language (Python, C++, JavaScript) at different part of the software architecture.

While primary key contains a unique value in each row, a word UNIQUE works similarly without the key feature. For example, we could want mobile numbers to be unique without being a primary key of a table:  UNIQUE (MobileNumber). AUTOINCREMENT definition is used many times in ID columns. It means that next free number (1,2,3,…,n) is automatically, without a user action, input to the column. In SQLite AUTOINCREMENT definition must come in the following order: *columnname*  INTEGER PRIMARY KEY AUTOINCREMENT.

See a detail syntax of SQLite CREATE TABLE command [in this link](https://www.sqlite.org/lang_createtable.html)

## DROP TABLE

**DROP TABLE**

Removes the named table and all rows within it.

DROP TABLE *TableName*

Depending on DBMS, there can be options like

- RESTRICT, if any other objects depend for their existence on continued existence of this table, SQL does not allow request.
- CASCADE, SQL drops all dependent objects (and objects dependent on these objects).



## ALTER TABLE

ALTER TABLE command is used for changing definitions of an existing table. For example, one can add a column, drop a column, rename a column, and change any existing definition.

**Example:** Add new column LastUpdated to Works_on table, and a constraint that checks the value is not in the future.

ALTER TABLE Works_on

ADD COLUMN LastUpdated DATETIME,
ADD CONSTRAINT notFutureValue CHECK (

LastUpdated <= DATE(“now”)

);

## FOREIGN KEY definitions in tables

As a recap,

- Foreign Key (FK) is column or set of columns that link each row in a child table containing FK to a row of parent table containing matching Primary Key (PK).
- Referential integrity means that if FK contains a value, that value must refer to existing row in the parent table.
- ISO standard supports definition of FKs with FOREIGN KEY clause in CREATE and ALTER TABLE
General format:

FOREIGN KEY (fk_columnname) REFERENCES pk_tablename (pk_columnname)

For example,

FOREIGN KEY (pno) REFERENCES Project (Pnumber);

## Referential Actions related to FOREIGN KEY definitions

Any INSERT or UPDATE attempting to create FK value in child table **without matching value in parent** **is rejected.**

However, action taken attempting to UPDATE or DELETE a value in parent table with matching rows in child **is dependent on referential action specified**. The options are:

- CASCADE
		SET NULL
		SET DEFAULT
		NO ACTION
		RESTRICT
CASCADE: Delete row from parent and delete matching rows in child, and so on in a cascading manner for the whole database.

SET NULL: Delete row from parent and set FK column(s) in child to NULL. Only valid if FK columns are set NULL.

SET DEFAULT: Delete row from parent and set each component of FK in child to specified default. Only valid if DEFAULT specified for FK columns.

NO ACTION: Reject delete from parent. Default.

RESTRICT: Reject delete from parent.

General format:

FOREIGN KEY (fk_columnname) REFERENCES pk_tablename (pk_columnname)
ON UPDATE *referential_action* ON DELETE *referential_action*

For example,

FOREIGN KEY (pno) REFERENCES Project (Pnumber)
ON UPDATE CASCADE ** ON DELETE RESTRICT;

**Example:** Create foreign keys to the table Works_on. According to the relational model, the column Essn refers to Employee table and the column Pno refers to Project table.

ALTER TABLE Works_on
ADD CONSTRAINT fk_emp_works FOREIGN KEY essn REFERENCES Employee(Ssn) ON UPDATE CASCADE ON DELETE CASCADE,
ADD CONSTRAINT fk_proj_works FOREIGN KEY pno REFERENCES Project(Pnumber) ON UPDATE CASCADE ON DELETE RESTRICT;

Now that the table Works_on is already created, we must use ALTER TABLE clause. For the referential actions we choose to CASCADE on update operations and RESTRICT on delete operations. The former means that the value in Pno column is updated according to changes in the referenced column in the Project table (Pnumber). The latter action means that, if one would try to delete a parent row with Pnumber value X and there were child rows with the same Pno value X, the deletion would not take place. The deletion of Pnumber X would be possible, if all child rows with the same value were deleted first. These actions are justified by the real life use context of the database. For example, restricting the deletion of a project is necessary, so that we do not lose the hours of employees working in that project. On the other hand, if the project number changes for some reason, but the project itself stays the same, we like to update the number to all child rows referencing to that project.

In the above example, foreign keys were added as named constraints with ADD CONSTRAINT clause. Naming allows us easily to delete the constraint (DROP CONSTRAINT *name)* if it is not needed any more.

**Unfortunately, the example above does not work in the SQLite.** In SQLite you must create foreign keys already when creating a table. SQLite does not support many ALTER TABLE commands that are presented in the SQL-standard, including **** adding a foreign key constraint separately after the table is created. See the SQL features that SQLite does not support: **** [https://www.sqlite.org/omitted.html](https://www.sqlite.org/omitted.html) **.** In this case, the only option with SQLite is to rename the original table and create a new table with correct foreign key definitions (plus extract and load data content if there is data already).

Let’s imagine we had not yet added the column Pno to the Works_on table. Then, we could alter the table and include the foreign key definition in the ADD COLUMN clause (which is supported).

In SQLite, for example,

ALTER TABLE Works_on
ADD COLUMN Pno INTEGER REFERENCES Project(Pno) ON UPDATE CASCADE ON DELETE RESTRICT;

CASCADE is a good option in maintaining the referential integrity of the relational database (i.e. correct data), yet it is good to understand that changes are done throughout the database.

- ON UPDATE CASCADE: Useful especially if you have ”a natural” primary key, like user name (not ID or similar), which can change frequently (people change their names and user names). The new name is changed to everywhere in the DB with on operation only.
- ON DELETE CASCADE: **** Deletes all child rows in a hierarchy.  You will be guaranteed that this deletes all the data in the DB and you don’t have to execute many SQL clauses (or even know where the data is).
Instead of CASCADE and real deletion of a row, one option is to use “a logical deletion”, which means that one of the column values in the row is set to mean “deleted”. In some cases, this can be used to preserve history data.

For example,

ALTER TABLE Works_on
ADD COLUMN isDeleted BOOLEAN DEFAULT = False;


## CREATE VIEW

A SQL VIEW refers to a single table whose content is derived based on other tables in the database. Unlike the relational tables discussed so far, views do not need to be physically stored. Views are often thought of as so-called into virtual whiteboards.

The virtual nature of the views limits the possibility of performing update operations on the views, but it in no way limits the possibility of executing queries based on them. SQLite does not support ALTER VIEW clause, only DROP and CREATE.

A new view can be created with the command CREATE VIEW. In connection with the command, the view is given a name that aptly describes it. After this, a query is presented, the result table of which the view is to be defined.

General format:

CREATE VIEW *ViewName* AS
SELECT *columname*
FROM *tablename;*

Establishing a view becomes necessary especially when a certain query has to be repeated often. Let's say, for example, that you often have to find out the names of the projects for which the company's employees perform work. This would require, without the existence of views, a query that joins the tables EMPLOYEE, WORKS_ON and PROJECT to each other. So that you don't have to repeat the query constantly, you can define a VIEW that is the result of the above connections.

Example: Create a view that collects the first and last name of each employee, the projects in which the employee works and the hours worked on them:

CREATE VIEW Works_on_View
AS SELECT Fname, Lname, Pname, Hours
FROM Employee, Project, Works_on
WHERE Ssn = Essn AND Pno = Pnumber;

Showing the VIEW contents:

SELECT *
FROM Works_on_View;

In the previous example, the attributes of the view are stored with the same name as their corresponding attributes in the original tables.

SQL VIEWs are useful for

- Structure data with VIEWs in a way that users find natural and intuitive (real tables in very dispersed and normalized format are not the most easy to understand).
- Restrict access to the data through VIEWs
	
		User can see and modify exactly what they need and no more.
- 
- Summarize data from various tables to VIEWs
	
		To generate reports and content to be presented in user interfaces


# SQL DML statements

## INSERT

The three SQL commands for updating database content are INSERT (insertion), UPDATE (change) and DELETE (deletion).

**INSERT ‒ insertion command**
To add a new complete record, you must set values for all its attributes in the order in which the columns were written during the CREATE TABLE command. The insertion command for a complete record (a value is entered for all columns) is of the

General format:

INSERT INTO tablename VALUES (Value1, Value2, ..., ValueN);

The input order can be changed if the input order used, different from the original one, is mentioned after the table by introducing a rearranged column list inside the brackets.

For example,

INSERT INTO tablename (column2, column5) VALUES (Value2, Value5);

Moreover, if you want to leave some of the attributes unentered, after the presentation of the target table (e.g. EMPLOYEE), you must tell in which fields the values to be entered are placed. Above, we entered only two values. The other fields are given (automatically) by the DEFAULT value or AUTOINCREMENT when defining the table, or if there is no such value, the value is set to missing (NULL). Note, that columns with NOT NULL definition can not be empty and must be given either manually or automatically.

**Example**: Add a new record to the table EMPLOYEE with all values:

INSERT INTO Employee
VALUES ('Richard', 'K', 'Marini', '653298653', '1962-12-30', '98 Oak Forest, Katy, TX', 'M', 37000, '987654321', 4 );

**Example**: Add a new record to the table EMPLOYEE with only some values:

INSERT INTO Employee (Fname, Lname, Ssn, Dno)
VALUES ("Will", "Worker", "12345678", 2);

Adding more than one row with one statement requires separation with comma. Also NULL values can be inserted (if accepted in column DDL).

**Example:** Add three new employees.

INSERT INTO Employee (Fname, Lname, Ssn, Dno)
VALUES ("Mike", "Mechanic", "9876543", 2), ("Alya", "Assistant", "1111122222", NULL), ("Mike", "Master", "91222543", NULL);

Adding new data can be based on a query (fully or partly):

**Example:** Our employee Mike Smith has married. Add him a spouse named Maria.

INSERT INTO Dependent (Essn, Dependent_name, Relationship) 
VALUES ((SELECT Ssn FROM Employee WHERE Fname="Mike" AND Lname = "Smith"), "Maria", "Spouse");

## DELETE

**DELETE ‒ removing data**
The DELETE command removes records from the database. One command can be used to delete either one or more records at a time.

General format:

DELETE FROM tablename WHERE condition;

If no condition has been set for the delete command, it will delete all the records in the table. The table itself will still be preserved. Be careful!

**Example**: Remove all employees, who work in the department number 2.

DELETE FROM Employee
WHERE Dno = 2;

## UPDATE

Changing the content of the records is done with the UPDATE command.

Command: UPDATE table SET changes WHERE condition;

Example: The location of project 10 and the department responsible for it are changed.

UPDATE PROJECT
SET Plocation = 'Bellaire', Dnum = 5
WHERE Pnumber = 10;

Example:. The salaries of the employees of the research department will be increased by 10 percent:

UPDATE EMPLOYEE
SET Salary = Salary * 1.1
WHERE Dno IN (SELECT Dnumber
               FROM DEPARTMENT
               WHERE Dname = 'Research');



## Example: Zoo database implementation

Above is the normalized relational model of the Zoo example. It corresponds with the following SQL statements. SQL statements were generated with erdplus.com tool. Check the video in the end of the page how to do it.

CREATE TABLE Species
(
  Species VARCHAR(100) NOT NULL,
  Wingspan_max INT NOT NULL,
  PRIMARY KEY (Species)
);

CREATE TABLE Employee
(
  Email VARCHAR(100) NOT NULL,
  Salary_€/month INT NOT NULL,
  EmpName INT NOT NULL,
  PRIMARY KEY (EmpName)
);

CREATE TABLE PhoneNumber
(
  Phonenumber INT NOT NULL,
  EmpName INT NOT NULL,
  PRIMARY KEY (Phonenumber, EmpName),
  FOREIGN KEY (EmpName) REFERENCES Employee(EmpName) ON UPDATE CASCADE ON DELETE CASCADE
);

CREATE TABLE Food
(
  Foodname VARCHAR(100) NOT NULL,
  Foodprice_€/kg INT NOT NULL,
  PRIMARY KEY (Foodname)
);

CREATE TABLE Bird
(
  Name VARCHAR(100) NOT NULL,
  Health_status INT NOT NULL,
  Species VARCHAR(100) NOT NULL,
  Foodname VARCHAR(100) NOT NULL,
  PRIMARY KEY (Name, Species),
  FOREIGN KEY (Species) REFERENCES Species(Species) ON UPDATE CASCADE ON DELETE CASCADE,
  FOREIGN KEY (Foodname) REFERENCES Food(Foodname) ON UPDATE CASCADE ON DELETE CASCADE
);

CREATE TABLE Color
(
  Color VARCHAR(100) NOT NULL,
  Name VARCHAR(100) NOT NULL,
  Species VARCHAR(100) NOT NULL,
  PRIMARY KEY (Color, Name, Species),
  FOREIGN KEY (Name, Species) REFERENCES Bird(Name, Species) ON UPDATE CASCADE ON DELETE CASCADE
);

CREATE TABLE FeedingEvent
(
  ID INT AUTOINCREMENT NOT NULL,
  Duration_of_event INT NOT NULL,
  Date DATE NOT NULL,
  Amount_of_food INT NOT NULL,
  FeederName INT NOT NULL,
  BirdName VARCHAR(100) NOT NULL,
  BirdSpecies VARCHAR(100) NOT NULL,
  PRIMARY KEY (ID),
  FOREIGN KEY (FeederName) REFERENCES Employee(EmpName) ON UPDATE CASCADE ON DELETE CASCADE,
  FOREIGN KEY (BirdName, BirdSpecies) REFERENCES Bird(Name, Species) ON UPDATE CASCADE ON DELETE CASCADE
);

Next, we would begin adding data to the Zoo database with INSERT statements. First, addding data to Species, Employee and Food tables as those are referenced (parents) by foreign keys of other tables (child).

An inserstion statement would look like, for example

INSERT INTO Food VALUES ("Peanuts", 12);
