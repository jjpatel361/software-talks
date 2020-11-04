
# NoSQL Story

Based on talk **GOTO 2012 Introduction to NoSQL - Martin Fowler**

### SQL Databases
- Default language for developers famous in 90's
- We split an object into multiple tables and then try to join them 
- ^ Normalization. can cause impedance mismatch problem - fancy way to say data mismatch
- Companies like Google had problems with big database. SQL was not solution to them because
  SQL was designed to run on single machines. And Google was running cluster of machines.
- Soon companies started coming up their solution. Google - BigTable and Amazon - DyanmoDB
- NoSQL name doesn't mean anything. Just a # on twitter that got trended has no meaning to it. 

### Data Models

- Key Value
  - A consistent disk based hashmap with KV pairs
  - Value can be anything image/json/document/blob db doesn't care
  - You can also attach meta data to KVs that tell more about the value stored in key. Helpful for querying and filtering
  
- Document Based
  - Documents are stored. They can be nested embedded ones. 
  - Commonly used JSON. Could be XML, YAML or any other spec that supports nested data model 
  - usually said schema less but it isn't 100% schemaless per se.
 
- Graph 
  - Graph databases are radically different then traditional databases
  - still fall under schemaless patterns
  - nodes have relationships with other nodes. and the graph database provides an efficient api to run through all nodes. 
  - really good if you want to do recursive queries or travel a network. 
  
- Columnar 
  - Big table is an example.
  - database is optimized for columnar storage.
  - select fewer columns to get user information. records might look like <user_id (col1), phonenumber(col2)>, <user_id(col1), email(col2)> 
  
## The ACID debate 

- Since schema dbs need to create records in multiple tables spread across they need the concept of transactions to wrap around queries
- So if you have a distributed data model (data is distributed among tables) the updates will need ACID properties 
- Schemaless DBs (KV, Document, Columnar) most of them don't have this distributed data foreign key relationships etc. so there is lesser need for them to be ACID
- Most of the data is at single place. 
  - KV : values are just a row 
  - document : entire document is decomposed as embedded/sub documents
- Graph dbs are exception, since the nodes are connected via relationships (data is decomposed in form of nodes) it does have ACID properties (transactions are possible) 
- so argument that NoSQL don't have ACID properties, is somewhat wrong. they have lesser need to support it. Graph definitely needs it so supports ACID 


## CAP theorem

**C** - **C**onsistency 
**A** - **A**vailability 
**P** - **P**artition Tolerance 

- General knowledge is, you can only get 2 items from the above three things right. You can either design for CA, AP etc
- Compared to general wisdom, real differentiator is P 
- You can then fine tune how much of system is towards C or A. This design decision is based on the business, if you are ok to be less consistent and more available e.g Amazon Shopping cart, it is ok if you have less consistent data but availablity drives the business. 
e.g (Personal one) Stock exchanges : it is ok to not available then to be in consistent. In consistentcy might lead to a wrong trade executed or decisions made 
- Consistency in NoSQL is different then consistency of SQL DB's

## When and Why to use NoSQL DB 

- easier development 
- large scale data - else you end up with large SQL tables 
- data analytics / data warehousing etc
 
 
 ## Takeway 
 
 NoSQL is still maturing and looks maturing
