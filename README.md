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

Within the same package as the **main class** in each microservice, create a new package named **"Entities"**. This package will contain all entity classes.  

Define your JPA entities using annotations like `@Entity`, `@Data`, `@NoArgsConstructor`, and `@AllArgsConstructor`.  

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

    @Transient  
    private AuthorDTO author;  
}
```

## `ms-users` - Creating `Author.java`
```java
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
