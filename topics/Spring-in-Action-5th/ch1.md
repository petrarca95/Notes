
# Part 1: Foundational Topics of Building Spring Applications

# Ch 1: Getting started with Spring
- Chapter 1: overview of spring and springboot
- Chapter 2: Spring MVC, how to present model data in browser, how to process and validate input
- Chapter 3: Adding data persistence to the app
- Chapter 4: Security
- Chapter 5: Configuration properties, learning how to fine-tune autoconfigured beans, apply configuration properties to application components, and work with Spring profiles.

## 1.1 What is Spring?
- Any non trivial app is composed of many components each responsible for its own piece of the overall app's functionality, coordinating with other app elements to get the job done
- when the app is run, the components somehow need to be created and introduced to each other
- The Spring container is at the core of the Spring framework, Spring offers a **container**, often referred to as the **Spring application context** that creates and manages these components of the app
- these components, or **beans** are wired together inside the Spring application context (container) to make a complete application
- the act of wiring beans together is based on a pattern known as **dependency injection**
- The container gets its instructions on what objects to instantiate, configure, and inject by reading the configuration metadata provided.
- The config data can be represented in XML, Java annotations, or Java code.


High level view of how Spring Works
![](assets/markdown-img-paste-20191106140521735.png)




### Main Idea of Dependency Injection
- instead of having components create and maintain the lifecycle of other components that they depend on, a dependency-injected application relies on a separate entity (the container) to create and maintain all components and inject those into the beans that need them.
- This injection is typically done through constructor arguments or property accessor

Example:

![](assets/markdown-img-paste-20191102122812835.png)


### Three ways to tell the ApplicationContext (Container) to wire beans together (Inject beans)

  #### 1. XML
  - The older way to wire beans together was using one or more XML files that describe the components (beans) and their relationship to other beans

  Ex

  -  following XML declares two beans `InventoryService` and `ProductService`
  -  The XML also injects InventoryService  into ProductService

```xml
<bean id="inventoryService"
class="com.example.InventoryService" />
<bean id="productService"
class="com.example.ProductService" />
<constructor-arg ref="inventoryService" />
</bean>
```



  #### 2. Annotation (Java-based configuration)
- The following configuration class is equivalent to the XML configuration:

```Java
@Configuration
public class ServiceConfiguration {
@Bean
public InventoryService inventoryService() {
  return new InventoryService();
  }

@Bean
public ProductService productService() {
  return new ProductService(inventoryService());
  }
}
```

- The `@Configuration` annotation tells spring that this is a configuration class that will provide beans to the Spring Application Context
   - `@Configuration` is the equivalent of XML's `<beans> </beans>` element
- The configuration class's methods are annotated with `@Bean`
  - This indicates that the objects they return should be added as beans to the application context
    - _By default, the bean id is the name of the method_
   - `@Bean` is the equivalent of XML's `<bean> </bean>`



### Creating an ApplicationContext (container) that contains the beans in the class annotated with @Configuration and performing DI

- `ApplicationContext` is an interface implemented by multiple classes
- the `AnnotationConfigApplicationContext` implementation is used to create a container containing the beans in the configuration class
- `FileSystemXmlApplicationContext` and `ClassPathXmlApplicationContext` load bean definitions from an XML file
<br>
<br>

`AppConfig.Java`

```JAVA
package com.example.demo;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

//the configuration annotation tells Spring this class will be used to create beans for the application context
@Configuration
public class AppConfig {

    //in this class we define multiple beans
    @Bean
    public InventoryService inventoryService(){
        return new InventoryService();
    }

    @Bean
    public ProductService productService(){
        return new ProductService(inventoryService());
    }


}

```
`InventoryService.Java`

```JAVA
package com.example.demo;

public class InventoryService {

    public int getProducts(){
        return 5;
    }

}

```


`ProductService.Java`

```JAVA
package com.example.demo;

public class ProductService{

    private InventoryService inventoryService;

    //DI occurs using constructor, if we get rid of constructor, inventoryService instance variable will be null
    public ProductService(InventoryService inventoryService) {
        this.inventoryService = inventoryService;
    }

    public String greet(String name){
        //NOTE HOW WE NEVER INSTANTIATED AN InventoryService OBJECT !!!! IOC AT ITS FINEST (AND DI)
        return "hello "+name + inventoryService.getProducts();
    }

    public InventoryService getInventoryService() {
        return inventoryService;
    }

    public void setInventoryService(InventoryService inventoryService) {
        this.inventoryService = inventoryService;
    }
}


```

`Main.Java`

```JAVA
package com.example.demo;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Main {
    public static void main(String[] args) {
        //this implementation of ApplicationContext is used to create a container which contains the beans from a user defined,annotation based, configuration class
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);

        //we get the bean as a general object, which we have to downcast for it to be useful
        ProductService productService = (ProductService)  applicationContext.getBean("productService");
        System.out.println(productService.greet("Miguel "));

        //if null pointer exception occurs, it is due to the instance variable of type InventoryService of the ProductService class being null. A nullpointer exception means  DI did not occur
        System.out.println("number of products is " +productService.getInventoryService().getProducts());

    }
}
```
```Java
Output:

hello Miguel 5
number of products is 5

```




Java-based configuration offers greater type safety and improved refactorability but _**EXPLICIT CONFIGURATION WITH EITHER JAVA OR XML IS ONLY NECESSARY IF SPRING IS UNABLE TO AUTOMATICALLY CONFIGURE THE COMPONENTS**_




#### 3. Automatic Configuration
- has its roots in spring techniques known as **autowiring** and **component scanning**
- with **component scanning** spring can automatically discover components from an application's classpath and create them as beans in the spring container
- With **autowiring** spring automatically injects the components with the other beans that they depend on
- `@Component` is used to indicate this class is an auto scan component

More information: https://www.mkyong.com/spring/spring-auto-scanning-components/



## Review Questions:

Source for IoC, container (ApplicationContext), and DI: https://www.baeldung.com/inversion-control-and-dependency-injection-in-spring
1. What is **Inversion Of Control** (IoC)
   - A principle by which the control of objects or portions of a program is given to a container or framework
   - In an app that does not use IoC, our custom code makes calls to libraries
   - In app that uses IoC, the framework takes control of the flow of the program and makes calls to our custom code
2. What is **Dependency Injection**?
   - one of the mechanisms that aids in achieving IoC
   - The control of creating and setting objects dependencies is given to a container, instead of the objects themselves doing this

Traditional way of creating and setting/injecting a dependency:
- Store objects have a dependency on Item objects
-In the example above, we need to instantiate an implementation of the Item interface within the Store class itself.

```JAVA
public class Store {
    private Item item;

    public Store() {
        item = new ItemImpl1();    
    }
}

```
- By using DI, we can rewrite the example without specifying the implementation of Item that we want:

```Java
public class Store {
    private Item item;
    public Store(Item item) {
        this.item = item;
    }
}
```
- Now we do not have to hard code the specific implementation of the item interface
- The specific implementation is defined in other places such as a .xml file or a class annotated with `@Configuration` which contains bean definitions

3. What is a **Spring container**?
   - An entity in charge of the whole lifecycle of beans, such as instantiating, and wiring (injecting) beans together, and destroying them.
4. What is **ApplicationContext**?
   - an interface which represents, or models, a Spring container
   - Multiple classes implement this interface, such as, `AnnotationConfigApplicationContext()`, which can create and manage beans from a class containing bean definitions. The class is annotated with `@Configuration` at the class level and `@Bean` at the method level
5. When are beans instantiated?
   - The default behavior for ApplicationContext implementations is to eagerly pre-instantiate all singleton beans when the context is started-up
   - For other scopes, beans will be instantiated whenever they are requested



## 1.2 Initializing a Project using Spring Initializr
<br>


#### Examining the Spring Project Structure


![](assets/markdown-img-paste-2019110714403504.png)

- The project has the typical Maven Structure
- `mvnw` and `mvnw.cmd` are maven wrapper scripts , these can be use to build project even if maven is not installed.
- `pom.xml`: describes your Project
- `TacoCouldApplication.java`: the spring boot main class that boot straps the Project
- `application.properties`: offers a place where you can specify configuration properties
- `static`: a folder where you get any static content (images, stylesheets, js, etc)
- `Templates`: folder containing template files that will be used to render content to browser. (files containing thymeleaf)
- `TacoCouldApplicationTests.java`: a simple test class that ensures that the Spring application context loads successfully. More tests will be added as we develop the app.

#### Exploring the pom.xml
- Since we chose Maven as the build tool, a pom.xml file is generated

![](assets/markdown-img-paste-2019110809423459.png)

![](assets/markdown-img-paste-20191108094255256.png)

#### `<packaging>`

- The `<packaging` element specifies the output type of your project once it is build
- traditional web apps are packaged as WAR
- The choice of JAR is a cloud minded choice
- WAR are suitable for deploying to a traditional Java app server, not a natural fit for most cloud platforms

#### `<parent>`
- This specifies that your project has spring-boot-starter-parent as its parent
POM


#### `<dependencies>`

- You may also notice that all three dependencies have the word starter in their arti-
fact ID. Spring Boot starter dependencies are special in that they typically don’t have
any library code themselves, but instead transitively pull in other libraries

#### `<build>`

![](assets/markdown-img-paste-20191108095634672.png)


#### TacoCouldApplication.java
![](assets/markdown-img-paste-20191108095820964.png)

- `@springBootApplication` made up of 3 tags:
  - `@SpringBootConfiguration`
      - a specialized version of @configuration. Designates this class as a configuration class. You can add java based configuration if you need to.
  - `@EnableAutoConfiguration`
    - Tells Spring boot to automatically configure any components it thinks you will need
  - `@ComponentScan`
    - enables component scanning **in the current package containing the @SpringBoot main class and its sub packages** allowing you to declare other classes with @Component, @Service, @Controller, etc. To have spring automatically discover them and register them as components in the Spring application context
    - **Note: It is recommended that you locate your main application class in a root package above the component classes of the application**
- `main()`
  - the method that will be run when the JAR is executed

## 1.3: Writing a Spring Application
- let's add a homepage.
- we need two things to make make our homepage function
  - Controller class
  - A view template

#### 1.3.1 Handling web requests
- Spring MVC is a web framework within Spring
- core concept is a **_controller_**, a class that handles requests and responds with information.
- Let's write a Controller that handles requests for the root URI "/"


![](assets/markdown-img-paste-20191108103151385.png)

- `@Controller`
  - primary goal is to identify class as a component
  - This allows the class to be identified as a component by Springs automatic scanning
  - Other annotations such as @Component can be used instead, @Controller should be used because it is more restrictive
- `@GetMapping("/")` or alternatively, `@RequestMapping(value='/', method = RequestMethod.GET)`
  - defines the annotated method as a handler method capable of handling `GET` requests to the root URI
- **Note, the handler method handles the GET request by returning the name of the view, we can use different libraries/frameworks to implement the view, we will use Thymeleaf**

#### 1.3.2 Defining the view

In the interest of keeping your homepage simple, it should do nothing more than wel-
come users to the site. The next listing shows the basic Thymeleaf template that
defines the Taco Cloud homepage.


```HTML
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml"
xmlns:th="http://www.thymeleaf.org">
<head>
<title>Taco Cloud</title>
</head>
<body>
<h1>Welcome to...</h1>
<img th:src="@{/images/TacoCloud.jpeg}"/>
</body>
</html>
```

- the `<img>` tag uses `th:src` attribute and an `@{..}` expression to reference the image with a context-relative path
- **static content such as images should be kept in static directory**
- lets define an images directory inside static and add our image to match the src thymeleaf attribute

#### 1.3.3 Testing the Controller
- Lets test two things

1. the view name returned by the handler method in charge of handling GET requests to "/" returns a string with a value "index"
2. the content of the response has the phrase "Welcome to..."


#### 1.3.5 SprintBoot DevTools
- follow steps in link below to integrate with IntelliJ
https://dev.to/suin/spring-boot-developer-tools-how-to-enable-automatic-restart-in-intellij-idea-1c6i

**Benefits:**
- Automatic application restart when code changes
- Automatic browser refresh (need to install an extension)
- Automatic disable of template cache
  - Cached templates are great for prod but not while developing
  - Cached templates make it impossible to make changes while app is running and to see changes after refreshing browser
- Built in H2 console
  - http://localhost:8080/h2-console

**Note:**
- If new dependencies are included, we need to restart application


**Automatic Browser refresh Extension installation:**
- http://livereload.com/extensions/


![](assets/markdown-img-paste-20191108144918216.png)

![](assets/markdown-img-paste-20191108144928139.png)

# Ch 2: Developing web Applications

- In chapter one, we created an app that displays a static homepage webpage

**Goals for Ch 2:**
1. Need a page that displays ingredients for taco artists to choose from
2. Ingredient choice may change, so can not be hardcoded, it should be fetched from a DB and handed over to the page to be displayed


- Controller's job is to fetch and process data
- View's job is to render data into HTML

**We need:**
- A domain class that defines a Taco
- A controller that fetches ingredient info and passes it to the View
- a View template that integrates that will have data fetched and be displayed to the browser

Note: we are deferring DB functionality to focus on Spring MVC, we will refactor to add DB in chapter 3

#### 2.1.1 Establishing the domain
- an applications domain is the subject are it addressses--the ideas and concepts that influence the understanding of the app

For this app:
- Tacos
- Customer
- Ingredients
- Taco orders

Let's define Taco ingredients:
