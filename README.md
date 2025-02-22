# Comprehensive Guide to Building Microservices with Spring Boot and Express.js  

This guide provides a detailed walkthrough for building microservices using **Spring Boot** and **Express.js**. It covers key concepts such as entity creation, repository management, REST controllers, service discovery with **Eureka**, API Gateway integration, and fault tolerance using **Circuit Breakers**.  

## Table of Contents  
- [Installation](#installation)  
- [Creating Entities](#creating-entities)  
- [Creating Repositories](#creating-repositories)  

---

# Installation  

## Creating Microservices with Spring Boot & Installing Dependencies  

### Microservices:  
- **ms-users**  
- **ms-books**  

### Required Dependencies:  
- **Lombok**  
- **Spring Web**  
- **Spring Data JPA**  
- **Spring H2 Database**  
- **Spring Boot Rest Repositories**  
- **OpenFeign**  

---

# Creating Entities  

- Within the same package as the **main class** in each microservice, create a new package named **"Entities"**. This package will contain all entity classes.  

- Define your JPA entities using annotations like `@Entity`, `@Data`, `@NoArgsConstructor`, and `@AllArgsConstructor`.  

## `ms-books` - Creating `Book.java`  

Ensure all required annotations are imported when using them.
```java
@Entity  
@Data  
@NoArgsConstructor  
@AllArgsConstructor  
public class Book {  
    @Id  
    @GeneratedValue(strategy = GenerationType.IDENTITY)  
    private Long bookId;  

    @Column(nullable = false)  
    private String title;  

    private Long authorId;  
}
```

## `ms-users` - Creating `Author.java`
```java
package com.example.msbooks.repositories;

@Entity
@Data @NoArgsConstructor
@AllArgsConstructor
public class Author {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long authorId;

    private String name;

    @Embedded
    private Address address;

    private List<String> bookId;
}
```

# Creating Repositories (Interfaces)  

- Within the same package as the **main class** in each microservice, create a new package named **"Repositories"**.  
- This package will contain all repository interfaces.  
- Each interface will extend `JpaRepository<EntityName, TypeOfIdInEntity>`.

### Notes:  
- **Import Required Dependencies**: Ensure `JpaRepository` and the entity class are correctly imported.  
- **Naming Convention**: The repository interface should follow the naming convention `EntityNameRepository` (e.g., `BookRepository`).  

## `ms-books` - Creating `BookRepository.java`  

```java
// Ensure all required annotations are imported before using them.
package com.example.msbooks.repositories;  // keep your package path

import com.uni.msbooks.Entities.Book;
import org.springframework.data.jpa.repository.JpaRepository;

public interface BookRepo extends JpaRepository<Book, Long> {

}

```

## `ms-users` - Creating `AuthorRepository.java` 
```java
// Ensure all required annotations are imported before using them.
package com.example.msusers.repositories;  // keep your package path

import com.uni.msuserss.Entities.Author;
import org.springframework.data.jpa.repository.JpaRepository;

public interface AuthorRepo extends JpaRepository<Author, Long> {

}
```

# Editing the Main Class  

To initialize data on application startup, modify the main class by implementing `CommandLineRunner` and injecting repositories.  

## Steps:  
1. **Implement `CommandLineRunner`** in the main class.  
2. **Inject repositories** using `private final` and annotate the class with `@RequiredArgsConstructor` (from Lombok).  
3. **Override the `run` method** to add initialization logic.  

## `ms-books` - Editing `MsBooksApplication.java`  

```java
package com.example.msbooks;
// Ensure all required annotations are imported before using them.
@SpringBootApplication
@RequiredArgsConstructor
public class MsBooksApplication implements CommandLineRunner {

    private final BookRepository bookRepository;

    public static void main(String[] args) {
        SpringApplication.run(MsBooksApplication.class, args);
    }

    @Override
    public void run(String... args) throws Exception {
        bookRepository.save(new Book(null, "Spring Microservices", 1L));
        bookRepository.save(new Book(null, "Java", 2L));

        System.out.println("Sample books added to the database.");
    }
}
```
## `ms-users` - Editing `MsUsersApplication.java` 
```java

@SpringBootApplication @RequiredArgsConstructor
@EnableFeignClients
@EnableCaching
public class MsUsersApplication implements CommandLineRunner {

    private final AuthorRepository userRepository;

    public static void main(String[] args) {
        SpringApplication.run(MsUsersApplication.class, args);
    }

    @Override
    public void run(String... args) throws Exception {
        userRepository.save(new Author(null, "ishak", new Address("sba street", "sba") ,null));
        userRepository.save(new Author(null, "Abdellah", new Address("sba street", "sba"), null));
    }
}
```

# Edit application.properties in Each Microservice

For each microservice, update the `application.properties` file with the following configuration:

```java
server.port=8081

spring.datasource.url=jdbc:h2:mem:testdb

spring.h2.console.enabled=true

spring.jpa.show-sql=true
```
**Note**: In the ms-users microservice, change the port number to 8082

# Add some Functions in AuthorRepo :
In the `AuthorRepo` interface, add the following functions to support custom queries and updates:
```java
List<Author> findAuthorByName(String name);
@Query("SELECT e FROM Author e where e.name like %:keyword%")
List<Author> findAuthorByKeywordInName(String keyword);
//(@Param("keyword") String keyword);

@Modifying
@Transactional
@Query(value = "update Author a  set a.name=:newName where upper(a.name) =upper(:name)")
int updateAuthorName(@Param("name") String name, @Param("newName") String newName);
// return 1 if success else 0
```

now let's test our work in browser , try :
```
http://localhost:8082/authors
```
```
http://localhost:8082/authors/1
```
```
http://localhost:8082/authors/search/findAuthorByName?name=ishak
```
```
http://localhost:8082/authors/search/findAuthorByKeywordInName?name=ishak
``` 
