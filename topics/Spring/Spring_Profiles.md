#Spring Boot Profiles

####Profiles
- A set of properties or even classes that should be enabled in some specific environment
- Profiles are a way to define an environment



####The Need for Profiles
- Profiles are needed in order to define different properties and implementations
 that will vary depending on whether our application is run in prod, dev, or test.
- We could make all changes manually, but it would be a lot easier to specify a set of properties and behaviors for dev, prod, and testing.

**Examples of different properties and behaviors based on environment:**
- Maybe we want a mock implementation of an expensive external API call that returns a fixed value in development and Testing and an implementation with the actual call in production **(different beans are injected based on environment that is active)**
- Maybe we want the app to run in a different portions
- We would probably want different database connections based on environment

####Defining profiles in application.properties or application.yml files
- These Profiles that represent an environment can be defined in either applciation.properties or application.yml files

######Steps to create various profiles using application.yml files
1. **Create and add properties to a application-dev.yml and application-prod.yml files**

`application-dev.yml`
```YML
server:
  port: 8080

message: Development mode is on

mode: dev
```

`application-prod.yml`
```YML
server:
  port: 9090

message: Production mode is on

mode: prod
```


2. **Activating one of the profiles**
- activate a profile in `application.yml` file which is considered by spring as a default property file

`application.yml`
```YML
spring:
  profiles:
    active: dev
```

- If no profile is set to active, then the "default" profile will be activated


####How to run different code depending on active profile

- one way to do this is to add the class level annotation `@Profile("profileName")` to the production, dev, or test class implementation.
- This annotation can be added when we register POJOs as beans by using annotation based bean configuration and component scanning, like `@Component`, `@Repository`, `@Service` as shown below
- `@Profile` can also be used to annotate methods annotated with `@Bean` (Java based configuration) instead of annotation based. We will see this later on.


ex:

`MovieRatingLiveimpl.java`
```java
package com.openclassrooms.watchlist.service;

import com.fasterxml.jackson.databind.node.ObjectNode;
import org.springframework.http.ResponseEntity;
import org.springframework.web.client.RestTemplate;

//* service acts as a client for the OMDB API

@Service
@Profile("prod")
public class MovieRatingServiceLiveImpl implements MovieRatingService {

    String apiUrl = "http://www.omdbapi.com/?apikey=ac58dccb&t=";

    @Override
    public String getMovieRating(String title) {

        try {
            RestTemplate template = new RestTemplate();

            //** getForEntity used to make a GET request
            ResponseEntity<ObjectNode> response =
                    template.getForEntity(apiUrl + title, ObjectNode.class);

            ObjectNode jsonObject = response.getBody();


            return jsonObject.path("imdbRating").asText();
        } catch(NullPointerException e){
            System.out.println("No rating available for this movie");
            return null;
        }
        catch (Exception e) {
            System.out.println("Something went wrong while calling OMDb API" + e.getMessage());
            return null;
        }
    }

}

```


`MovieRatingDummyImpl.java`
```java
package com.openclassrooms.watchlist.service;

@Profile("dev")
@Service
public class MovieRatingDummyImpl implements MovieRatingService {

    @Override
    public String getMovieRating(String title) {
        return "9";
    }
}

```



`application.yml`


```yml
spring:
  profiles:
    active: prod
```

`WatchlistService.java`

```java
package com.openclassrooms.watchlist.service;

import com.openclassrooms.watchlist.domain.WatchlistItem;
import com.openclassrooms.watchlist.exception.DuplicateTitleException;
import com.openclassrooms.watchlist.repository.WatchlistitemRepository;

import java.util.List;

@Service
public class WatchlistService {

    private WatchlistitemRepository watchlistitemRepository;
    //THE PRODUCTION BEAN WILL BE THE ONE INJECTED BY THE CONTAINER VIA CONSTRUCTOR DI WHEN THE ACTIVE PROFILE IS PROD!!!!
    private MovieRatingService movieRatingServiceImpl;

   @Autowired
    public WatchlistService(WatchlistitemRepository watchlistitemRepository, MovieRatingService movieRatingService){
        this.watchlistitemRepository = watchlistitemRepository;
        this.movieRatingServiceImpl = movieRatingService;
    }


    public List<WatchlistItem> getWatchlist (){
        return watchlistitemRepository.getList();
    }

    public int getWatchlistSize(){
        return watchlistitemRepository.getList().size();
    }

    public void addOrUpdateWatchlistItems(WatchlistItem watchlistItem) throws DuplicateTitleException{
        if(watchlistItem.getId()==null){
            if (watchlistitemRepository.findByTitle(watchlistItem.getTitle())!=null){
                throw new DuplicateTitleException();
            }

            String rating = movieRatingServiceImpl.getMovieRating(watchlistItem.getTitle());

            //not sure why rating is empty when API returns movie not found message
            if (rating!= null &&  !rating.equals("")){
                watchlistItem.setRating(rating);
            }

            watchlistitemRepository.addItem(watchlistItem);
        }
        else{
            WatchlistItem existingWatchlistItem = watchlistitemRepository.findWatchlistItemById(watchlistItem.getId());
            watchlistitemRepository.updateItem(watchlistItem, existingWatchlistItem);
        }
    }

    public WatchlistItem findWatchlistItembyId(Integer id){
        return watchlistitemRepository.findWatchlistItemById(id);
    }


}

```

- When the Active profile is set to prod, like it was shown above in `application.yml`, the production implementation of MovieRatingService will be the bean that will be injected into `WatchlistService.java` by the Spring container via constructor Dependency Injection




**Bean Configuration and DI Using Java**
- we an also use java to configure and to perform DI
- we can annotate the method annotated with @Bean, which will return beans, with the following annotation `@ConditionalOnProperty(name = "mode", havingValue = "name of profile")`
- We can also use `@Profile`
- In this case since we both configure the beans and perform DI in the @Configuration file, we do not need the @Service and @Autowire Tags
- `@ConditionalOnProperty` allows us to load beans conditionally depending on certain environment properties


```java
@Configuration
public class AppConfig {

    @Bean
    @ConditionalOnProperty(name = "mode", havingValue = "dev")
    //@Profile("dev") can also be used
    public WatchlistService watchListServiceDev(){
        return new WatchlistService(watchlistitemRepository(), MovieRatingServiceDummy());
    }

    @Bean
    @ConditionalOnProperty(name = "mode", havingValue = "prod")
    //@Profile("prod") can also be used
    public WatchlistService watchListServiceProd(){
        return new WatchlistService(watchlistitemRepository(), MovieRatingServiceLive());
    }

    @Bean
    public WatchlistitemRepository watchlistitemRepository(){
        return new WatchlistitemRepository();
    }

    @Bean
    public MovieRatingService MovieRatingServiceLive(){
        return new MovieRatingServiceLiveImpl();
    }

    @Bean
    public MovieRatingService MovieRatingServiceDummy(){
        return new MovieRatingDummyImpl();
    }


}
```
