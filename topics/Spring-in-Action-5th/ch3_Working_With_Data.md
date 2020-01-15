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
- Spring JDBC support is rooted in `JdbcTemplate.java`
  - provides means by which devs can perform SQL operations performed on a relational DB without the boilerplate code usually required without Spring


##### 3.1.1 Adapting The Domain For Persistence
- when persisting objects to a DB we generally want a one field that uniquely identifies the object
- Our ingredient class has an Id but we need to add an id to `Taco` and `Order`
- It also useful to know when a taco was created

```Java
@Data
public class Taco {
private Long id;
private Date createdAt;
...
}
Because you use Lombok to automatically generate accessor methods at runtime,
there’s no need to do anything more than declare the id and createdAt properties.
They’ll have appropriate getter and setter methods as needed at runtime. Similar
changes are required in the Order class, as shown here:
@Data
public class Order {
private Long id;
private Date placedAt;
...
}
```

- The domain objects are now ready for persistence


Lets see how to use `JdbcTemplate` to read and write them to a DB
<br>
##### 3.1.2 Working with JdbcTemplate

- First lets add the dependency to our `pom`
```xml
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
```

and a DB, in memory DB H2 for now...

```XML
<dependency>
<groupId>com.h2database</groupId>
<artifactId>h2</artifactId>
<scope>runtime</scope>
</dependency>
```
<br>


###### Defining JDBC Repository Classes
our Ingredient repo needs to
1) Query all
2) Query single ingredient by Id
3) Save ingredient

<br>
Lets define an Interface

```Java
package tacos.data;
import tacos.Ingredient;
public interface IngredientRepository {
Iterable<Ingredient> findAll();
Ingredient findOne(String id);
Ingredient save(Ingredient ingredient);
}
```

Interface captures the essence, Now lets write a class to implement it

```JAVA
package tacos.data;
import
import
import
import
org.springframework.beans.factory.annotation.Autowired;
org.springframework.jdbc.core.JdbcTemplate;
org.springframework.jdbc.core.RowMapper;
org.springframework.stereotype.Repository;
import tacos.Ingredient;

@Repository
public class JdbcIngredientRepository implements IngredientRepository {

private JdbcTemplate jdbc;

@Autowired
public JdbcIngredientRepository(JdbcTemplate jdbc) {
this.jdbc = jdbc;
}
...
}
```


- `@Repository` annotation makes this class autoscanable
- Spring scans it, and registers the class as a bean
- Spring automatically registers a JdbcTemplate bean already configured with the data source, and its injected via the constructor
- Note the class has a dependency on JdbcTemplate, which will be doing the interfacing with the DB
