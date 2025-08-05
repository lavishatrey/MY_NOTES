

# DBMS Full Notes

## LEC-1: Introduction to DBMS

### 1. What is Data?

- Data is a collection of raw, unorganized facts and details like text, observations, figures, symbols, descriptions, etc.
- Data does not have any specific purpose or significance by itself.
- Data is measured in terms of bits and bytes – basic units of information in computer storage and processing.
- Data can be recorded but has no meaning unless processed.

### 2. Types of Data

- **Quantitative**
  - Numerical form.
  - E.g., Weight, volume, cost.
- **Qualitative**
  - Descriptive, not numerical.
  - E.g., Name, gender, hair color.

### 3. What is Information?

- Information is processed, organized, and structured data.
- Provides context and enables decision-making.
- Data makes sense when analyzed and interpreted.
- E.g., Data of people in locality → info such as "100 senior citizens", "sex ratio is 1.1", "newborns are 100".

### 4. Data vs Information

- Data: collection of facts, raw, unorganized, individual, unrelated, meaningless on its own.
- Information: context, organized, big-picture, meaningful.
- Data does not depend on information; information depends on data.
- Data: graphs, numbers. Information: words, thoughts, ideas.
- Data isn’t sufficient for decision-making, but information is.

### 5. What is Database?

- Database is an electronic system where data is stored for easy access, management, and updating.
- Database Management Systems (DBMS) are required to make real use of data.

### 6. What is DBMS?

- DBMS is a collection of interrelated data and programs to access those data.
- Contains information relevant to an enterprise.
- Main goal: convenient and efficient storage/retrieval.
- DBMS contains the database and all software/functionality for operations (add, access, update, delete).

### 7. DBMS vs File Systems

- File-processing systems have disadvantages:
  - Data Redundancy & Inconsistency
  - Difficulty in accessing data
  - Data Isolation
  - Integrity problems
  - Atomicity problems
  - Concurrent-access anomalies
  - Security problems
- The above are the advantages of DBMS!

## LEC-2: DBMS Architecture

### 1. View of Data (Three Schema Architecture)

- DBMS provides users with an abstract view (hides storage/maintenance details) through three levels of abstraction:
  1. **Physical/ Internal Level**
     - Lowest level.
     - Describes how data is stored.
     - Data structures, allocation, compression, encryption.
  2. **Logical/ Conceptual Level**
     - Design of the database at conceptual/logical level.
     - What data is stored, relationships among them.
     - Used by DBA.
  3. **View/ External Level**
     - Highest level.
     - Personalized user interface, hides the rest of DB.
     - Multiple schemas (subschemas/views).
     - Also used for security.

### 2. Instances and Schemas

- **Instance**: Data in the DB at a particular moment.
- **Schema**: Overall design/structure of DB.
  - Three types: Physical, Logical (most important), several View schemas (subschemas).
  - Schema changes rarely; data changes frequently.
  - Physical data independence: physical changes don't affect logical schema/programs.

### 3. Data Models

- Tools for describing design of a database at logical level.
- Includes data, relationships, semantics, and constraints.
- E.g., ER model, Relational Model, Object-Oriented Model, Object-Relational Model.

### 4. Database Languages

- **DDL (Data Definition Language)**: defines schema, constraints.
- **DML (Data Manipulation Language)**: read/modify data.
- SQL combines both.

#### DML Operations:

1. Retrieve information
2. Insert new information
3. Delete information
4. Update information

### 5. Database Access from Applications

- Application programs (in C/C++, Java) access DB via DDL/DML statements.
- APIs used:
  - ODBC (for C)
  - JDBC (for Java)

### 6. Database Administrator (DBA)

- Central control over data and programs.
- Functions:
  1. Define schema
  2. Manage storage/access methods
  3. Modify schema/organization
  4. Authorization control
  5. Routine maintenance - backups, security, upgrades

### 7. DBMS Application Architectures

- **T1 (single-tier):** Client, server, DB all on one machine.
- **T2 (two-tier):** Client/server interact (API standards like ODBC/JDBC).
- **T3 (three-tier):** Client (UI) → App Server (business logic) → DBMS. Best for WWW.

## LEC-3: Entity-Relationship Model

### 1. Data Model

- Collection of tools for describing data, relationships, semantics, and constraints.

### 2. ER Model

- High-level modeling of real world: entities & their relationships.
- **ER Diagram**: graphical representation ("blueprint").

#### Key concepts:

- **Entity:** Distinguishable object in the real world with physical (or sometimes conceptual) existence. E.g., Student.
- **Strong Entity:** Can be uniquely identified.
- **Weak Entity:** Cannot be uniquely identified without depending on another (strong) entity.
- **Entity Set:** Group of similar entities.
- **Attributes:** Properties of an entity (e.g., Student_ID, Name, Address).
  - **Simple:** Indivisible.
  - **Composite:** Divisible into subparts (e.g., Name: first-name, last-name).
  - **Single-valued:** One value.
  - **Multi-valued:** More than one value (e.g. phone numbers).
  - **Derived:** Value can be derived (e.g., Age from Date of Birth).
  - **NULL:** No value, not applicable, or unknown.
- **Relationships:** Associations among entities (e.g., Student takes Course).
  - **Strong/Weak Relationship:** Weak relationship connects weak entity to its owner.
  - **Degree:** Number of entities (Unary, Binary-most common, Ternary...).
- **Relationship Constraints:**
  - **Mapping Cardinality:**
    - One-to-One, One-to-Many, Many-to-One, Many-to-Many.
  - **Participation Constraints:** Minimum cardinality.
    - Partial: Not all participate.
    - Total: All must participate (e.g., weak entities always have total participation).

## LEC-4: Extended ER Features

### 1. Specialisation

- Top-down: Split an entity set into more specific entity sets ("is-a" relationship).
- E.g., Person → Student, Customer, Employee (Person is superclass, others are subclasses).

### 2. Generalisation

- Bottom-up: Combine similar entity sets into one general entity set.
- E.g., Car, Jeep, Bus → Vehicle.
- "is-a" relationship between subclass and superclass.

### 3. Attribute/Participation Inheritance

- Subclasses inherit attributes and participation in relationships from superclass.

### 4. Aggregation

- Treat relationships as higher-level entities.
- Used to model relationships among relationships.

## LEC-7: Relational Model

### 1. Structure

- Data is organized as relations (tables).
- Each table/relation has a unique name.
- Each row = tuple (unique record).
- Each column = attribute.

### 2. Schema

- Design/structure of a relation: relation name + columns/attributes.
- Degree: Number of attributes.
- Cardinality: Number of tuples.

### 3. Table Properties

- Unique relation names.
- Atomic values (cannot be broken down).
- Unique column names.
- Unique tuples (rows).
- No significance to row or column ordering.
- Must conform to integrity constraints for consistency.

### 4. Relational Keys

- **Super Key (SK):** Any attribute set that can uniquely identify a tuple.
- **Candidate Key (CK):** Minimal SK (no redundant attributes).
  - CK can't be NULL.
- **Primary Key (PK):** Chosen CK, minimum attributes.
- **Alternate Key (AK):** All CKs except PK.
- **Foreign Key (FK):** Attribute(s) referencing PK of another table.
  - FK creates relationships between tables.
- **Composite Key:** PK with at least two attributes.
- **Compound Key:** PK formed using two FKs.
- **Surrogate Key:** Synthetic/DB-generated PK.

### 5. Integrity Constraints

- Rules to keep the DB consistent.

#### Examples:

- **Domain Constraint:** Valid data types, values.
- **Entity Constraint:** Each table must have a PK; PK can't be NULL.
- **Referential Constraint:** FKs must reference existing PKs or be NULL.
- **Key Constraints:**
  - NOT NULL
  - UNIQUE
  - DEFAULT
  - CHECK
  - PRIMARY KEY
  - FOREIGN KEY

## LEC-8: Transform - ER Model to Relational Model

- ER diagrams can be converted to relations (tables).

### Conversion Rules:

1. **Strong Entity**
   - Becomes a table.
   - Attributes = columns.
   - PK is the entity's PK.
2. **Weak Entity**
   - Table with all attributes.
   - PK of strong entity becomes FK.
   - Composite PK: {FK + partial discriminator key}.
3. **Single-value Attributes**
   - Directly as columns.
4. **Composite Attributes**
   - Each subpart as a separate column.
5. **Multivalued Attributes**
   - New table.
   - PK of entity as FK in new table.
   - New table PK: {FK + multi-valued attribute}.
6. **Derived Attributes**
   - Not considered in tables.
7. **Generalisation**
   - Method 1: Table for parent and separate for child entities (child tables include parent PK as FK).
   - Method 2: If disjoint/complete, one table per child entity with all parent and child attributes.
8. **Aggregation**
   - Relationship set forms a table with:
     - PKs of participating entities
     - Any descriptive attribute of the relationship

## LEC-9: SQL in 1-Video

### Basics

- **SQL**: Structured Query Language (not a DBMS, just a language).
- CRUD operations:
  - CREATE: Insert new tuple
  - READ: Retrieve data
  - UPDATE: Modify data
  - DELETE: Remove data
- **RDBMS**: Relational DBMS (MySQL, Oracle, MS SQL, IBM, etc.), uses SQL for data ops.
- MySQL is open-source RDBMS, uses SQL, client-server model.

### SQL Data Types (Some Examples)

| Data Type   | Description             |
|-------------|-------------------------|
| CHAR        | string (0-255)          |
| VARCHAR     | variable string (0-255) |
| INT         | -2^31 to 2^31-1         |
| FLOAT       | decimal (up to 23 digits)|
| DOUBLE      | decimal (24-53 digits)  |
| DATE        | YYYY-MM-DD              |
| DATETIME    | YYYY-MM-DD HH:MM:SS     |
| ENUM        | preset values           |
| BOOLEAN     | 0/1                     |

### SQL Commands Overview

- **DDL** (Data Definition Language)
  - CREATE (table, DB, view)
  - ALTER TABLE
  - DROP (table, DB, view)
  - TRUNCATE (delete all rows)
  - RENAME
- **DRL/DQL** (Data Retrieval/Query Language)
  - SELECT
- **DML** (Data Modification Language)
  - INSERT
  - UPDATE
  - DELETE
- **DCL** (Data Control)
  - GRANT
  - REVOKE
- **TCL** (Transaction Control)
  - START TRANSACTION
  - COMMIT
  - ROLLBACK
  - SAVEPOINT

### Managing DB (DDL)

```sql
CREATE DATABASE IF NOT EXISTS db_name;
USE db_name;
DROP DATABASE IF EXISTS db_name;
SHOW DATABASES;
SHOW TABLES;
```

### Data Retrieval (SELECT...FROM...)

- Syntax: `SELECT  FROM ;`
- WHERE, BETWEEN, IN, AND, OR, NOT, IS NULL, LIKE `%`, `_`, ORDER BY, GROUP BY, DISTINCT, HAVING

### Constraints in SQL (DDL)

- **PRIMARY KEY**: Not NULL, unique, only one per table.
- **FOREIGN KEY**: Refers to PK of another table.
- **UNIQUE**: Unique values, can be NULL, multiple unique per table.
- **CHECK**: e.g., `CHECK (age > 12)`
- **DEFAULT**: e.g., `saving_rate DOUBLE NOT NULL DEFAULT 4.25`

### ALTER Table

- ADD: new column
- MODIFY: change datatype
- CHANGE COLUMN: rename
- DROP COLUMN: remove
- RENAME: rename table

### DML

- **INSERT**: 
  ```sql
  INSERT INTO table_name(col1, col2) VALUES (v1, v2);
  ```
- **UPDATE**: 
  ```sql
  UPDATE table_name SET col1 = v1 WHERE id = 1;
  ```
- **DELETE**:
  ```sql
  DELETE FROM table_name WHERE id = 1;
  ```

## LEC-11: Normalisation

1. **Goal:** DB optimization by eliminating redundancy.
2. **Functional Dependency (FD)**
   - Relationship: X → Y (X = Determinant, Y = Dependent)
   - **Trivial FD:** If Y is a subset of X, it's trivial.
   - **Non-trivial FD:** Y is not a subset of X.
3. **Armstrong’s axioms**
   - Reflexive: A ⊇ B, so A → B
   - Augmentation: If A → B, then AX → BX
   - Transitivity: A → B and B → C, so A → C
4. **Anomalies due to Redundancy**
   - Insertion: Can't insert data without other data.
   - Deletion: Deleting data causes loss of needed data.
   - Update: Multiple changes needed; risk inconsistency.
5. **Normal Forms**
   - **1NF:** Atomic values only.
   - **2NF:** 1NF + no partial dependency.
   - **3NF:** 2NF + no transitive dependency between non-prime attributes.
   - **BCNF:** 3NF + every determinant is a super key.
6. **Advantages**
   - Minimize redundancy, better organization, consistency.

## LEC-12: Transaction

1. **Transaction:** Unit of work on DB, logical sequence.
2. All operations must **commit** (all succeed) or **rollback** (none succeed).
3. **ACID Properties:**
   - **Atomicity:** All or nothing.
   - **Consistency:** DB must be consistent before and after transaction.
   - **Isolation:** Concurrent transactions must not affect each other.
   - **Durability:** Once committed, changes persist.

**Transaction States:**
- Active
- Partially committed (changes saved in memory buffer)
- Committed (changes permanent, can't rollback)
- Failed (error during transaction)
- Aborted (after rollback)
- Terminated (after commit or abort)

## LEC-13: Atomicity & Durability Implementation

### 1. Shadow-copy Scheme

- Keep a shadow copy of DB.
- If transaction aborts, use shadow copy to restore.
- On commit, update pointer to new DB copy.
- **Efficient for small DBs; inefficient for large (copies everything).**

### 2. Log-Based Recovery

- Every operation logged to stable storage (before applying to DB).
- In case of crash:
  - Can redo/undo by log.
- **Deferred Modification:** 
  - All writes go to log, then actual DB (if crashed before commit, ignore).
  - Redo needed if crash during commit.
- **Immediate Modification:** 
  - Writes can go to DB before commit; log maintains old/new values.
  - Undo uses old value; redo uses new value.

## LEC-14: Indexing in DBMS

1. **Indexing**: Improves query performance by minimizing disk access.
2. **Index**: Data structure (usually B+ tree, etc.), quickly finds data using search key.

**Components:**
- **Search Key:** Copy of PK or other attribute.
- **Data Reference:** Pointer/address to disk block where record is stored.
- Index increases retrieval speed but isn't the primary data access means.

### Types:

- **Primary Index (Clustering Index):**
  - Ordered data file (e.g., by PK).
  - Sparse Index for PK, Dense for non-key.
- **Dense Index:** One index record for every search key value.
- **Sparse Index:** Index entries for only some search key values.
- **Multi-level Index:** Indexes within indexes (like tree).
- **Secondary Index (Non-clustering):**
  - For unsorted data.
  - Can be done on key or non-key attributes.
  - Always dense (one index per record).

### Advantages

- Faster access/retrieval
- Less I/O

### Limitations

- Extra storage for index table
- Slower INSERT, DELETE, UPDATE due to index maintenance

**End of LEC-14**
