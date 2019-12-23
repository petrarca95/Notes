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

<br>

### Inserting Tables
- Adding Data to your Tables

![](assets/markdown-img-paste-20191222193606797.png)

- Order Matters if name appears first then the value for name needs to be first


Ex:
![](assets/markdown-img-paste-20191222194603340.png)


![](assets/markdown-img-paste-20191222194620691.png)



Example:
![](assets/markdown-img-paste-20191222194945779.png)



```SQL
INSERT INTO table_name
            (column_name, column_name)
VALUES      (value, value),
            (value, value),
            (value, value);
```



Example of Multiple Inserts:
```SQL
mysql> INSERT INTO people (first_name, last_name, age)
    -> VALUES ('Linda', 'Belcher', 45),
    -> ('Phillip','Frond', 38),
    -> ('Calvin','Fischoeder',70);

```



#### Warnings
- Sometimes mySQL would show  Warnings
- If we define a column to be of type `VARCHAT(10)` and we insert a string of characters > 10, we will get a warning
- `SHOW WARNINGS` is used to display warnings
- In this case the string will get truncated to 10 characters
- Note: Warnings will be wiped after another command is run
- If we try to insert a string where a cell expects an INT then the value will be set to 0 and a warning logged
  - we can change this default


#### NULL and NOT_NULL

![](assets/markdown-img-paste-20191222201600616.png)

- NULL means   "there is no specified value"

###### What if we want to define that a coulmn should not accept nulls

```sql

CREATE TABLE cats2(
  name VARCHAR(100) NOT NULL,
  age INT NOT NULL)
)
```
- In MySQL, each column type has an "implicit default" value.
- Below we can see that Null is not allowed for either field, and that the default value is set to Null
```SQL
mysql> show columns from cats2;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| name  | varchar(10) | NO   |     | NULL    |       |
| age   | int(11)     | NO   |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+

```


Now lets try to insert a row (cat entry) with a missing column

```SQL
mysql> INSERT INTO cats2(name) VALUES ('texas');
ERROR 1364 (HY000): Field 'age' doesn't have a default value
mysql> INSERT INTO cats2(age) VALUES (7);
ERROR 1364 (HY000): Field 'name' doesn't have a default value
mysql>
```

- Note no entry was inserted
- I believe this happens only if strict mode is on
`For data entry into a NOT NULL column that has no explicit DEFAULT clause, if an INSERT or REPLACE statement includes no value for the column [and if] strict SQL mode is enabled, an error occurs ..`


![](assets/markdown-img-paste-20191222203908450.png)

<br>

#### Setting Default Values
![](assets/markdown-img-paste-20191222204135784.png)
- It is useful to specify a fall back if a record is missing a field value
- That is where Default comes in


Ex:

```SQL

CREATE TABLE cats3(
    -> name VARCHAR(20) DEFAULT 'no name provided',
    -> age INT DEFAULT 99);

```

```SQL
+-------+-------------+------+-----+------------------+-------+
| Field | Type        | Null | Key | Default          | Extra |
+-------+-------------+------+-----+------------------+-------+
| name  | varchar(20) | YES  |     | no name provided |       |
| age   | int(11)     | YES  |     | 99               |       |
+-------+-------------+------+-----+------------------+-------+

```

Lets try to insert a record with missing name

```SQL
INSERT INTO cats3(age) VALUES(10);
```

Result:

```SQL
mysql> INSERT INTO cat3(age) VALUES(10);
Query OK, 1 row affected (0.01 sec)

mysql> SELECT * FROM cat3;
+------------------+------+
| name             | age  |
+------------------+------+
| no name provided |   10 |
+------------------+------+
1 row in set (0.00 sec)

mysql>

```




<br>

![](assets/markdown-img-paste-20191222205144932.png)
- no, if we leave a field of an entry empty, it will be defaulted to the default value but of we do not explicitly include not null, there is nothing stopping SQL from inserting NULL with the following statement

```SQL
INSERT into table(name, age) VALUES('miguel',NULL);
```
On the other hand, if we try to explicitly insert null above, we get an error saying a field value can not be null


Summary:
![](assets/markdown-img-paste-20191222205454327.png)

<br>

###Primary On Keys

![](assets/markdown-img-paste-20191222211652377.png)

- Right now we can enter a bunch of cats with the same name and age
- This is a problem because we want our data to be uniquely identifiable
- Each row needs to be unique
- Each row represents a unique entity


![](assets/markdown-img-paste-20191222212254697.png)


![](assets/markdown-img-paste-20191222212341446.png)

##### How do we assign a column as a primary key in SQL?

```SQL
CREATE TABLE unique_cats (cat_id INT NOT NULL,
   name VARCHAR(100),
    age INT,
     PRIMARY KEY (cat_id));
```


```SQL

mysql> INSERT INTO unqiue_cats(cat_id,name,age) VALUES(1,"fred",23);
ERROR 1146 (42S02): Table 'bakery.unqiue_cats' doesn't exist
mysql> INSERT INTO unique_cats(cat_id,name,age) VALUES(1,"fred",23);
Query OK, 1 row affected (0.00 sec)

mysql> SELECT * from unique_cats;
+--------+------+------+
| cat_id | name | age  |
+--------+------+------+
|      1 | fred |   23 |
+--------+------+------+
1 row in set (0.00 sec)

mysql> INSERT INTO unique_cats(cat_id,name,age) VALUES(1,"MIG",232);
ERROR 1062 (23000): Duplicate entry '1' for key 'PRIMARY'
mysql>

```

Note: we can not insert an entry with a duplicate value for whatever column was defined as a primary key
