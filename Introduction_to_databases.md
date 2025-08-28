## Introduction to databases

**What is data?**

- Ontology: What exists? Epistemology: How do we know that?
- Data = Collection of symbols
	
		Values like 21, 28, 29, 33
- Information = Data with interpretation
	
		Weather measurements: Temperature in Celsius scale --> We know that temperatures of this week are raising
- Knowledge = Information with meaning in the context
	
		"It is quite hot in Finland in the end of August"
		”Don’t wear long trousers today”
- Database stores data. Database queries (e.g. with SQL) fetch that data, combines, groups etc. which can help us answer questions we have
**What is database?**

Simple definition: Set of interconnected data

Long definition**:** Shared collection of logically related data (and a description of this data), designed to meet the information needs of an organization.

- Shared: Several applications can use.
	
		à Separated from the individual application (its own layer)
- Collection: Data forms a coherent whole.
- Data: data, not information or knowledge.
- Logically related: How pieces of data relate to each other and form the collection, and how single data can be iodentified
- Description of data: Metadata i.e. data about data. What is the type, format and length of data etc.?
- Meet the information needs: A purpose for designing and implementing the database: reliable, valid, accurate, relevant data for real use
- Organization: Context which the database serves, is used and implemented in
Database management system (DBMS) is the system to manage and query that data (creating the database, giving user rights, indexing, etc.).

**Example of a database**

- Database for student administration
	
		A collection of data is needed about
		
			Student's basic information, enrollments, credits
			Courses
			Classrooms, premises
			Teachers …
- We need a database management system (DBMS), e.g. MySQL, PostgreSQL, SQLite. MS SQL Server, in which the database is created
- We need a software application that uses this information (PEPPI, It’s Learning, Moodle...)
- There can be several views to the same information.
	
		The teacher can see the grades of all students and can enter them
		The student can see his own information and performance
**Characteristics of a database**

Database needs to…

- Be self-descriptive (containing data + metadata = description of data e.g. date created, owner,...)
- Support multiple users
	
		Transactions
- Provide different views to same data
	
		Three-Schema Architecture: Internal, conceptual and external level
		
			Internal: describes physical storage structure
			Conceptual: e.g. ER-model
			External: part of the data is available to a particular user group and the rest is hidden (e.g. Views)
- Be separated from the applications that use its data
**Problems of using files for storing data**

- Each application has its own data
	
		All necessary data may not be available to other applications
- Multiplication of information
	
		The same data in several places
		For example, customer data
		Are you sure they are up to date?
- Dependencies
	
		File structure
- Incompatible file formats
	
		Language dependency
- Standardized surveys and contents.
	
		Making changes is tedious for both to the application and to the information content
**When to use a database instead of files for storing data?**

- If the data is local and static
	
		Saving data to a file could work OK
		Example: several software have a config.xml or a similar file.
- If you have any information that is
	
		Needed elsewhere than in one application (reading and/or updating)
		It will be changed (often) and
		It must remain reliable and consistent
		There's a lot of data to be stored
		You need to search and query data under (changing) conditions
- --> Then the database is the right choice

## Overview of a software application architecture

![](kuvat/Introduction_to_databases/1.png)

## Database design aims and phases

Introduction to database design aims, phases and ER modelling

[](https://youtu.be/I5O8u99WiVM)

[](https://youtu.be/O1m48hmYB9Y)

