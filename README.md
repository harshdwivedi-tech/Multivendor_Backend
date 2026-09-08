# Scalable Multi-Vendor E-Commerce Backend

A modular **Spring Boot backend** for a multi-vendor e-commerce platform that provides REST APIs for managing customers, vendors, products, categories, shopping carts, orders, and related workflows.

The project is built with a layered architecture using **Spring Data JPA and Hibernate** for persistence, with a focus on clean entity relationships, transaction management, efficient database access, and maintainable backend design.

## Features

* Multi-vendor product management
* Category management
* Customer account and profile management
* Vendor management
* Product and category browsing
* Shopping cart operations
* Order and order-item management
* RESTful API development
* JPA and Hibernate entity relationships
* Transaction management using Spring
* Cascade operations and orphan removal
* Efficient database queries
* N+1 query optimization where applicable
* Hibernate batch processing where configured
* Unit testing with JUnit 5

## Tech Stack

* **Java 17**
* **Spring Boot**
* **Spring Data JPA**
* **Hibernate ORM**
* **MySQL**
* **HikariCP**
* **Maven**
* **JUnit 5**
* **React** — Frontend

## Architecture

```text
                    REST API
                       |
                       v
                 ┌───────────┐
                 │ Controller│
                 └─────┬─────┘
                       |
                       v
                 ┌───────────┐
                 │  Service  │
                 └─────┬─────┘
                       |
                       v
                 ┌───────────┐
                 │ Repository│
                 └─────┬─────┘
                       |
                       v
             Spring Data JPA
                       |
                       v
                   Hibernate
                       |
                       v
                     MySQL
```

The application follows a layered architecture that separates API handling, business logic, data-access operations, and database persistence.

## Entity Relationships

### User and CustomerProfile

```text
User
 |
 +-- @OneToOne
       |
       v
CustomerProfile
```

A user can have an associated customer profile.

### Category and Product

```text
Category
 |
 +-- @OneToMany
       |
       v
Product
```

A category can contain multiple products.

### Product and Cart

```text
Product
 |
 +-- @ManyToMany
       |
       v
Cart
```

Products and carts are associated through a join table.

### Order and OrderItem

```text
Order
 |
 +-- @OneToMany
       |
       +-- CascadeType.ALL
       +-- orphanRemoval
       |
       v
OrderItem
```

Order items are managed as child entities of an order.

## Hibernate & JPA Features

### Entity Relationship Mapping

The project uses JPA annotations to model relationships between different parts of the e-commerce domain, including:

* `@OneToOne`
* `@OneToMany`
* `@ManyToOne`
* `@ManyToMany`
* `mappedBy`
* `CascadeType.ALL`
* `orphanRemoval`

These mappings help maintain relationships between customers, vendors, products, carts, orders, and order items.

### Transaction Management

Spring's transaction management is used to maintain consistency across database operations, particularly for workflows involving multiple related entities.

### Hibernate Batch Processing

Hibernate JDBC batching can be configured to reduce the number of database round trips during bulk operations.

Example:

```properties
spring.jpa.properties.hibernate.jdbc.batch_size=50
```

Batch processing can be useful for operations involving multiple product or inventory records.

### Orphan Removal

Where configured, `orphanRemoval = true` allows child entities to be automatically removed when they are removed from their parent's collection.

## N+1 Query Optimization

When working with relationships between entities, careless fetching strategies can result in the **N+1 query problem**.

For example:

```text
1 query  → Fetch orders
N queries → Fetch associated order items
```

This can result in unnecessary database calls when retrieving related data.

Where required, JPQL `JOIN FETCH` can be used to retrieve associated entities efficiently.

Example:

```java
SELECT o
FROM Order o
JOIN FETCH o.orderItems oi
JOIN FETCH oi.product
WHERE o.customer.id = :customerId
```

This allows the required associations to be retrieved together instead of triggering additional queries for each individual record.

## Project Structure

```text
src/
├── main/
│   ├── java/
│   │   └── com.example.ecommerce/
│   │       ├── controller/
│   │       ├── service/
│   │       ├── repository/
│   │       ├── entity/
│   │       ├── dto/
│   │       └── EcommerceApplication.java
│   │
│   └── resources/
│       └── application.properties
│
└── test/
    └── java/
```

## Database Configuration

The application uses **MySQL** as its relational database.

Create the database:

```sql
CREATE DATABASE multivendor_db;
```

Then configure the database connection in `application.properties`:

```properties
spring.application.name=Ecommerce

server.port=8080

spring.datasource.url=jdbc:mysql://localhost:3306/multivendor_db
spring.datasource.username=root
spring.datasource.password=your_password

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

> Replace `your_password` with the password configured for your MySQL user.

## Running the Project

### Clone the Repository

```bash
git clone https://github.com/Priyank1922/MultiVendor_BackEnd.git
cd MultiVendor_BackEnd
```

### Configure MySQL

Make sure MySQL is installed and running.

Create the database:

```sql
CREATE DATABASE multivendor_db;
```

Update the username and password in:

```text
src/main/resources/application.properties
```

### Build the Project

```bash
mvn clean install
```

### Run the Application

```bash
mvn spring-boot:run
```

The backend will start on:

```text
http://localhost:8080
```

## Docker

The backend can also be packaged as a Docker image using a multi-stage Docker build.

Example Dockerfile:

```dockerfile
FROM maven:3.9-eclipse-temurin-17 AS build

WORKDIR /build

COPY pom.xml .
COPY src ./src

RUN mvn clean package -DskipTests

FROM eclipse-temurin:17-jre

WORKDIR /app

COPY --from=build /build/target/*.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
```

The application uses **Java 17** in both the build and runtime stages.

> When running the backend and MySQL in separate Docker containers, configure the datasource hostname to use the MySQL container/service name rather than `localhost`.

## Testing

The project uses **JUnit 5** for automated testing.

Run the test suite using:

```bash
mvn test
```

## Frontend

The frontend application is maintained in a separate repository.

**Multi-Vendor E-Commerce Frontend**

[Frontend Repository](https://github.com/harshdwivedi-tech/Multivendor_Frontend.git)

The React frontend communicates with this Spring Boot backend through REST APIs and provides the user interface for customers and vendors.

The frontend supports workflows such as:

* Product discovery
* Category browsing
* Shopping cart management
* Order management
* Authentication
* Vendor product/catalog management

## Key Learning Outcomes

This project demonstrates practical experience with:

* Spring Boot backend development
* REST API development
* Java 17
* Spring Data JPA
* Hibernate ORM
* MySQL database integration
* JPA entity relationship mapping
* Transaction management
* Cascade operations
* Orphan removal
* JPQL queries
* N+1 query optimization
* Hibernate batch processing
* Layered backend architecture
* Unit testing with JUnit 5
* Docker-based application packaging

## Project Highlights

**Project Type:** Multi-Vendor E-Commerce Platform

**Backend:** Spring Boot, Spring Data JPA, Hibernate

**Language:** Java 17

**Database:** MySQL

**Connection Pool:** HikariCP

**Build Tool:** Maven

**Testing:** JUnit 5

**Frontend:** React

**Architecture:** Layered REST API architecture

**Optimization:** Efficient JPA queries, N+1 query optimization, and Hibernate batching where configured

**Deployment:** Docker-ready backend
