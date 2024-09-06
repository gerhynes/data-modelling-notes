# Data Modelling Notes

Sources:
- Mastering Data Modeling Fundamentals

## Fundamentals
A data model is a representation of what your data is in the real world, providing insights into the character and structure of your data.

While you can reverse-engineer a data model from an existing database, you should model your data before you build a database. 

The values of a data model include:

- Abstraction from database implementation specifics
- Helps even with relational databases
- Even more valuable with non-intuitive data implementations

### Brief History of Data Modelling
Peter Chan's "The Entity-Relationship Model: Towards a Unified View of Data" (ACM TODS, March, 1976) is typically considered the starting point. 

There had been earlier "data model" and "information model" work in the late 1960s early 1970s.

In the mid-1970s there were no relational databases. Most data was stored on mainframe and minicomputer file systems. Many files were stored on tape and punch cards. Data definitions and structures were typically buried in Cobol and Fortran programs. First generation hierarchical and netowrk-style databases were much more difficult to follow and trace.

Data models allowed us to look inside the actual data regardless of how it was structured, represented and managed.

### Concepts and Terminology
A Data Model will typically include:

- Major data subjects
- Attributes of data subjects
- Relationships among data subjects
- Business rules for your data

Data Subjects:
- Commonly called "entities"
- Some methodologies use "objects" or "classes"
- Somewhat analogous to a database table
- Something that exists: instructor, student, class

Attributes:
- Analogous to database columns
- Think of a field
- Attributes are typically associated with entities: instructor has id, last_name, first_name
- Attribute types are often shared across multiple entities, for example id, first_name, last_name
- Attributes can be made more descriptive: instructor_id, student_last_name
- "Bottom up" data modelling (attributes then entities) was once semi-popular

Relationships among data subjects:
- Relationships can be named: instructor teaches class, instructor belongs to department
- Relationships can (probably should) be named bi-directionally: instructor belongs to department, department is made up of instructors
- Names of relationships can be duplicated: instructor teaches class, instructor teaches student
- Multiple relationships can exist between the same entities: instructor teaches student, instructor advises student
- Relationships can be hierarchies: instructor can be salaried or adjunct

Business Rules:
- Cardinality
- Mandatory or optional relationships
- Permissable attribute values (including NULL)
- Data change dynamics, such as referential integrity

### Data modelling compared to database design
With database design:
- you are tied to a specific database model, such as relational
- go below tables and schemas to physical storage
- consider implementation and product-specific restrictions from the beginning

With data modelling:
- start at the conceptual/semantic level
- not be constrained by RDBMS (and other) implementation rules
- closer to real-world than database design

Data modelling lifecycle
Conceptual Modelling -> Logical Modelling -> Physical Modelling

### Transactional Data Modelling vs Analytical Data Modelling
Enterprise environments typically have transactional systems and applications (production databases) as well as analytical systems and applications (data warehouse/data lake).

The data modelling **techniques** are the same but the **methodologies** are different. The diagrams may look similar but there will be different sets of rules from top to bottom.

Transactional
- Conceptual level - mirror real world
- Logical level (relational) - data normalization rules with deliberate denormalization
- Logical level (non-relational) - NoSQL, OODBMS
- Physical level - blocks/tracks, MPP (massively-parallel processing) distributions

Analytical
- Conceptual level - dimensional
- Logical level (relational) - fact and dimensional tables in accordance with best practices
- Logical level (non-relational) - cubes, columnar databases
- Physical level - blocks/tracks, MPP distributions, AWS buckets, HDFS NameNodes and DataNodes



## Building Blocks of Data Modelling
### Entities
Think of an entity as some type of real-world subject. You can also think of it as a collection of attributes (fields).

An Entity is typically a major subject area. 

Entities are not synonymous with database tables. They are roughly equivalent but sometimes a database table is artificial in nature (such as intersections between entities). 

Database tables are also sometimes duplicative of other tables (aggregates).

Identify Entities by staying focused on major subject areas. Don't broaden dimensionally: faculty, not faculty_by_department, student_loan_balances not student_loan_balances_by_student_by_major. This is an artificial construct that gets away from the real-world entity.

#### Representing Entities
Typically though a rectangle.

Normal/strong entities have squared-off corners.

Name in centre/on top of box.

#### Adding Attributes
Think of an Attribute as some sort of data field. They explode the details of an Entity.

Attribute domains: 
- reusable general classes of descriptions
- applied to selective attributes that fit the domain
- supported by some data modelling software tools
- e.g. "Valid Business Date" between 1/1/1980 - 31/12/2199

Attribute tips:
- decompose, but carefully (street + city + state + zip each as a separate attribute/field) don't dive street into number + name + type
- Look at sample data

### Multi-valued Attributes
Some attributes, like first_name, will only have one value. But what about if someone has multiple email addresses?

Multi-valued attributes **can have** more than one possible values for each instance. And different instances don't need to have the same number of values per attribute.

Represented traditionally with a double oval.

Crow's foot notation software (which more closely aligns with logical database modelling), often doesn't allow MVAs.

Multi-valued Attributes are one of the two most obvious differentiators between conceptual and logical modelling. The other is many-to-many relationships vs database intersection tables.

Use MVAs liberally in your conceptual models. Again make use of sample data.

#### Building Relationships into Data Models
Relationships are a conceptual connection within a data model.

Classic ER Notation uses a diamond. Crow's foot uses a line.

The direction of a relationships plays into its naming. 

Single-naming views relationship in one direction: Teacher teaches Class. Double-naming views relationships in two directions: Teacher teaches Class, Class is taught by Teacher.

Relationships help you navigate around a data model.


## Adding Real-World Complexity
#### Hierarchies
Think of a hierarchy as a special type of Entity but also a special type of Relationship. Think "specialisation".

The typical use case for a hierarchy is when you have two or more entities that have a lot in common but also at least a couple of things different. For example: FacultyMember can be specialised into Tenure Class Faculty and Adjunct Instructor.

Totally separating two entities that have a lot of overlap unnecessarily complicates the database and querying. 

Hierarchies can be **inclusive** or **exclusive**. A University Employee can be both a Faculty member and an Admin Employee. A Faculty Member can be either Tenure Class Faculty or Adjunct Instructor. 

You can also have a multi-level hierarchy. University Employee -> Faculty Member -> Adjunct Instructor.

Use hierarchies as much as you can in your conceptual model.

#### Specifying Constraints for Attributes
Constraints are rules/guidelines.

Attribute-level constraints:
- data types and sizes
- whether null values are allowed
- permissable values

Data types and sizes are conceptually the same as database definitions. At the Data Model level, data types and sizes will be mapped to database-specific definitions.

Null values are used to support operational database integrity (no empty cells) but need to be used carefully. Again, sample data is your friend. Look at each field individually.

You might want to permit nulls while progressively building out database tables (work-in-progress database).

Imported third-party data sets might have null values. Certain real-world scenarios might have only minimal information, such as public health.

There are several analytic techniques to allow for missing data.

There may be a fixed range of values for any permissable values for attributes, such as age ranges. Attributes may also lists of permissable values.

Use attribute constraints liberally in your conceptual models.

#### Specifying Cross-Entity Dependencies
Set aside strong and weak entities for now. Think in terms of dependencies.

There are two types of Entities:
- Independent Entities
- Dependent Entities
	- Identification Dependency
	- Existence Dependency

A Strong Entity exists on its own terms, independently of any other entity. It doesn't require any other entity instance to help identify its own instances.

A Weak Entity needs some help, either to identify some specific instance of that Entity or it can't exist without an instance of another entity, or both.

Identification Dependency: An Entity pulls part or all of the key for any instance from some other entity.

## Adding Real-World Complexities to Relationships
There can be multiple relationships between two entities, recursive relationships of an entity to itself, ternary (three entity) relationships, as well as relationships that also seem like entities (gerunds).

For example, A Faculty Member teaches a Student, a Faculty Member advises a Student. These are two similar but conceptually different relationships. Two instances of these entities may have one but not both of these relationships.

A Faculty Member may mentor other Faculty Members in a recursive relationship.

A data modelling Gerund is a Relationship that also acts like an Entity. It's technically a Relationship with Attributes specific to the Relationship itself.

A Session at an academic conference covers a Topic, is held in a specific Room, and also has a Leader. A Session is the relationship between these three entities. Session can have a date/time attribute.

#### Defining Maximum Cardinality
Cardinality means the number of something.

In entity-relationship modelling, every relationship has two cardinalities: maximum cardinality and minimum cardinality.

Maximum cardinality is the number of instances of both sides of a relationship. Typically 1 or M (many). It could also be a specific numeric value, though this is uncommon.

Bidirectional naming of relationships is very helpful here.

**1:1 Relationship** - An instance from each side of the relationship is related to exactly 1 instance from the other side. For example, a university has exactly one president and a person can only be president of one university.

**1:M Relationship** - An instance from one side of the relationship is related to one or more instances from the other side. For example, a faculty member can advise one or many students.

**M:M (M:N) Relationship** - Any instance from either side of the relationship can be associated with one or many instances from the other side. For example, a student can enroll in one or many classes. A class can have one or many students enrolled.

Specific number for maximum cardinality - An instance from one side of a relationship can be related to at most some number from the other side. Can be one-directional or bi-directional. For example, a student can take no more than 7 classes per semester, or a class can enroll up to 300 students.

#### Defining Minimum Cardinality
Minimum cardinality is sometimes referred to as a "participation constraint" with two possible values:
- total participation -> minimum cardinality = 1
- partial participation -> minimum cardinality = 0

Minimum cardinality is also known as a mandatory vs optional relationship. A mandatory relationship has a minimum cardinality or 1, while an optional relationship has a minimum cardinality of 0.

Minimum cardinality can also be some explicit number of minimum instances. For example, a full professor must advise at least two other faculty members. Rarely used in the real-world.

## Harmonising Different Levels
#### Relational Database Normalisation
Normalisation predates RDBMSs. Defined as part of the relational model by Edgar Codd at IBM (early 1970s).

Mnemonic for 1st, 2nd and 3rd normal form "The key, the whole key, and nothing but the key... so help me Codd."

**1st Normal Form**
- Every row (tuple) must be unique
- No repeating groups

**2nd Normal Form**
- Data must be in 1st normal form
- No partial key dependencies. Applies to composite primary keys.

**3rd Normal Form**
- Data must be in 2nd normal form
- No non-key dependencies

At the conceptual level there are no real issues with violating normalisation. It should be addressed at the logical level.

Sometimes you will deliberately violate normalization at the physical level (de-normalization). 

#### Forward-Engineering Conceptual Data Models
The data model lifecycle moves from conceptual modelling to logical modelling to physical modelling. Eventually every data model needs to get real.

Conceptual data models are typically not directly implementable. You need to move from the conceptual to the logical level, applying additional constraints and rules.

Typical conceptual to logical transformations are:
- Address violations of normalization
- Transform M:M relationships
- Add foreign keys

For 1NF violations (repeating groups), move the offending data to separate tables.

For 2NF violations (partial key dependencies), move the offending attributes to another entity (table).

For 3NF violations (non-key dependencies), move the offending attributes to the correct entity (table).

For many-to-many relationships, add an intersection table/entity (associative/bridge entity). This decomposes M:M relationships into "semantically equivalent" but artificial relationships. For example, a Class-Enrollment entity with both class_id and student_id making up the primary key.

At the logical level, you need to add foreign keys (usually not included at the conceptual level). 

At the physical level, you might denormalize records for performance reasons. You might also create aggregates, when you report data at a higher level of abstraction. You might also create materialized views, a join that materializes the result of a query.

At the physical level, you might also use optimized storage placement (partitioning) or database indices for common access patterns.

Your end result should be a well-designed, high performing database.

#### Reverse Engineer a Physical Model
You might need to begin with an actual database and reverse engineer back to a conceptual model. 

You need to perform "semantic reversal"
- reconstruct relationships and max/min cardinality
- reconstruct identity and existence dependencies
- reconstruct hierarchies
- reconstruct multi-valued attributes

Reverse engineering uses
- Primary and foreign keys
- Database constraints
	- NOT NULL
	- CHECK clauses
	- Referential integrity
- "Artificial looking" database table definitions

Reconstructing a M:M relationship through the combination of the primary and foreign keys.

For reverse engineering large and complex databases, you will want to use data modelling software, override as necessary, and validate through sample data.

## Software for Data Modelling
There are both manual and software-assisted approaches for data modelling.

You could use a free-form drawing/presentation tool such as Powerpoint, which gives you more freedom and flexibility. For example, derived attributes may not be otherwise supported. 

The challenges are that you:
- don't have rules/guidelines to constraint you
- drawings can become unwieldily
- you have no assistance with forward/reverse engineering.

For real-world uses, you want to use data-modelling software.

The tools help
- enforce methodologies, techniques, and notation rules
- "real-estate" management of diagrams, allowing for better organization
- fully or semi-automated forward and reverse engineering

Options include Microsoft Viseo, CA ERwin, and ER/Studio Data Architect.





