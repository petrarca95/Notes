# What is a Database
- A collection of data
- A method for accessing and manipulating the data
- A structured set of data with an interface


An example of a "database" is a phone book.
- With a phone book we can look up people

![](assets/markdown-img-paste-20191221221726786.png)



## Database vs DBMS

![](assets/markdown-img-paste-20191221221901701.png)

- A DBMS =/= Database
- A DBMS is the program (software) that allows us to interface with our Databases

![](assets/markdown-img-paste-20191221222028509.png)
- People often refer to a Database as the DBMS and DB together
- All the names above are DBMS not Databases

## MySQL vs SQL
- SQL is the language we use to talk to out DB
- We could do the following things with SQL
  - find all users
  - find all users who are 18 years old
  - add new user named Miguel

![](assets/markdown-img-paste-20191221222433843.png)
<br>
![](assets/markdown-img-paste-20191221222611474.png)

#### Different Relational DBs

![](assets/markdown-img-paste-20191221222515707.png)


<br>

![](assets/markdown-img-paste-20191221222635408.png)
<br>
![](assets/markdown-img-paste-20191221222653349.png)

- There is a standard to SQL
- SQL may vary slightly

<br>

![](assets/markdown-img-paste-20191221222741535.png)
Features that may be different:
- speed
- security



#### Section 3: Creating Databases and Tables
- Currently we have a mySQL Database server which comes with the installation of MySQL
- We ran the server by `sudo service mysql start`


![](assets/markdown-img-paste-20191222095308133.png)
- Inside the DB server we can have multiple Databases


**Lets zoom in inside two of our DBs**

![](assets/markdown-img-paste-20191222095501507.png)

- DB Dog Walker and Soap Shop DB both store users and payments
- these must be kept separate

<br>

#### Showing databases

- The following command allows us to see all DBs inside the DB server

![](assets/markdown-img-paste-20191222095659826.png)

![](assets/markdown-img-paste-20191222095728350.png)

<br>

#### Creating databases


![](assets/markdown-img-paste-20191222095919356.png)

Ex:

![](assets/markdown-img-paste-2019122210005232.png)


#### Dropping Databases
`DROP DATABASES <name>`
- DROP is the SQL word for delete
- used to delete DBs and Tables .. maybe others

Ex:

![](assets/markdown-img-paste-20191222100338346.png)


##### Let's actually Use DBs

![](assets/markdown-img-paste-20191222100424575.png)
- This command tells MySQL which DB we want to work with
- Needed because DB server has multiple DBs


![](assets/markdown-img-paste-20191222100638264.png)

- This command is used to tell you which DB you are currently using, if any

![](assets/markdown-img-paste-20191222100855441.png)



##Intro to Tables (in Relational DBs)
- a DB contains a collection of tables
- Table:
  - a collection of structured related data

**Anatomy:**
Columns : headers
Rows: Each entry/record

<br>

**Example of a Table:**
![](assets/markdown-img-paste-20191222101311288.png)

<br>

![](assets/markdown-img-paste-20191222101244293.png)
- usually not just One
- usually we will have, an a Pet sitting app
- we would have to keep track of owners, payments, dogs, etc


### Data Types
- when creating a new Table, we have to specify the Data Type of each column


Reason:
![](assets/markdown-img-paste-20191222101700616.png)


![](assets/markdown-img-paste-20191222101733906.png)

**lets focus on two Types**

![](assets/markdown-img-paste-20191222101810980.png)

![](assets/markdown-img-paste-20191222101838807.png)

![](assets/markdown-img-paste-20191222101919948.png)
- varchar(number)
- number represents the maximum number of characters allowed

<br>

### Creating Tables

- table names should be pluralized
![](assets/markdown-img-paste-20191222102606722.png)

```SQL
CREATE TABLE tablename
  (
    column_name data_type,
    column_name data_type
  );
CREATE TABLE cats
  (
    name VARCHAR(100),
    age INT
  );
```

**How do we know it worked?**

`SHOW TABLES`

`SHOW COLUMNS FROM cats`

`DESC cats`

Ex:

![](assets/markdown-img-paste-20191222102925333.png)


### Deleting tables
`DROP TABLE cats`

<br>

###Lets Practice

![](assets/markdown-img-paste-20191222103158666.png)



![](assets/markdown-img-paste-20191222103747909.png)
