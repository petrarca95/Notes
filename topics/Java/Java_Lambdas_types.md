info aggregation of Java masterclass and other sources



### Functional Interface
- interface with **one and only one** abstract method
  - This Method specifies the purpose of the interface
- `@FunctionalInterface` not required, but it does ensure that compiler makes sure that interface only has one abstract method

Example:

`RowMapper.java`

```java
@FunctionalInterface
public interface RowMapper<T> {

	/**
	 * Implementations must implement this method to map each row of data
	 * in the ResultSet. This method should not call {@code next()} on
	 * the ResultSet; it is only supposed to map values of the current row.
	 * @param rs the ResultSet to map (pre-initialized for the current row)
	 * @param rowNum the number of the current row
	 * @return the result object for the current row (may be {@code null})
	 * @throws SQLException if an SQLException is encountered getting
	 * column values (that is, there's no need to catch SQLException)
	 */
	@Nullable
	T mapRow(ResultSet rs, int rowNum) throws SQLException;

}

```
<br>


### Anonymous Class
- A local Class without a name
- One time use to implement a functional interfaces method
- usually extends subclasses or **implements  interfaces**



```java
public class EmployeeDao {  
private JdbcTemplate template;  

public void setTemplate(JdbcTemplate template) {  
    this.template = template;  
}  

public List<Employee> getAllEmployeesRowMapper(){  
 return template.query("select * from employee",

////Anonymous class
 new RowMapper<Employee>(){  
    @Override  
    public Employee mapRow(ResultSet rs, int rownumber) throws SQLException {  
        Employee e=new Employee();  
        e.setId(rs.getInt(1));  
        e.setName(rs.getString(2));  
        e.setSalary(rs.getInt(3));  
        return e;  
    }  
    }
////End of anonymous class    
    );  
}  
}  
```

<br>

### Lambdas

#### Introduction
- New feature in Java 8


#### Use
- provide an easier way to work with functional interfaces
  - Now we can define the implementation of a FI with a lambda
  - Since a FI only has one abstract method, the Lambda is matched to that abstract method.
- Used to replace the need to instantiate an anonymous class when we want to implement a FI
  - this reduces boilerplate code

#### Syntax

```java

(//argument list) -> {//Implementation of abstract method body}
```
<br>

**Ex: implementing a FI using a Lambda instead of an anonymous class**

```java
public class EmployeeDao {  
private JdbcTemplate template;  

public void setTemplate(JdbcTemplate template) {  
    this.template = template;  
}  

public List<Employee> getAllEmployees(){  
 return template.query("select * from employee",
//Start of lambda definition, which is in this case, the implementation of the MapRow method, the abstract method in the functional interface RowMapper.java
    (rs, rownumber) -> {
      Employee employee =  new Employee();
      employee.setId(rs.getInt(1));
      employee.setName(rs.getString(2));
      employee.setSalary(rs.getInt(3));
      return employee;
    }
////End of lambda definition  
    );  
}  
}  
```
<br>

#### What does Compiler do when it sees a Lambda in the above example?
1. sees that the lambda is passed to the query method.

2. checks to see the JdbcTemplate's query method, sees that one of the implementations accepts RowMapper<T> type, which has an abstract method which returns an object of type T, Employee type in this case, and whose aparameters (abstract method's parameters) are of type ResultSet and int
3. checks to see lambda returns an Employee type and has two parameters (does type inference on argument list)
4. If conditions in 3 are met, lambda is matched to the abstract method in the FI
