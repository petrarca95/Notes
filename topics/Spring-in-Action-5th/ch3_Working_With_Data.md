# Chapter 3: Working with Data

- In Taco App we need to be able to maintain info about ingredients, tacos, and orders
- We will ad persistence to our app
- We'll use Spring support for JDBC to eliminate boilerplate code
- Then we will rework the data repositories to work with the JPA (Java Persistence API), eliminating even more boiler plate code.


#### 3.1 Reading and Writing Data With JDBC
- Relational DBs and SQL are the leading choice for data Persistence
- Java developers have two options when it comes to working with relational databases

1. JDBC
2. JPA

- Spring supports both and makes things easily


###### Lets look at Spring JDBC support
- rooted in `JdbcTemplate.java`
  - provides means by which devs can perform SQL operations performed on a relational DB without the boilerplate code usually required without Spring
