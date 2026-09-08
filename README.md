# Scalable Multi-Vendor E-Commerce Data Access Layer

A modular **Spring Boot backend** for a multi-vendor e-commerce platform designed to manage vendors, products, categories, shopping carts, orders, and customer accounts.

The project focuses on building a scalable persistence layer using **Spring Data JPA and Hibernate**, with optimized entity relationships, transaction management, soft deletes, batch processing, and efficient database queries.

The backend uses **PostgreSQL** as its database and is configured for cloud deployment using **Neon PostgreSQL, Docker, and Render**.

## Features

* Multi-vendor product and category management
* Customer account and profile management
* Shopping cart operations
* Order and order-item management
* Vendor management
* Complex entity relationships using JPA and Hibernate
* Soft deletion of products
* Hibernate batch processing for bulk operations
* Cascade operations and orphan removal
* N+1 query optimization using JPQL `JOIN FETCH`
* Transaction management with Spring
* PostgreSQL database integration
* Neon PostgreSQL cloud database support
* Dockerized backend
* Render deployment support
* Unit testing with JUnit 5

## Tech Stack

* Java 21
* Spring Boot
* Spring Data JPA
* Hibernate ORM
* PostgreSQL
* Neon PostgreSQL
* HikariCP
* Maven
* Docker
* Render
* JUnit 5

## Architecture

```text
Controller
    |
    v
Service
    |
    v
Repository
    |
    v
Spring Data JPA / Hibernate
    |
    v
PostgreSQL
    |
    v
Neon PostgreSQL
```

The application follows a layered architecture that separates API handling, business logic, data access, and persistence responsibilities.

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

Products and carts are connected using a join table.

### Order and OrderItem

```text
Order
 |
 +-- @OneToMany
     CascadeType.ALL
     orphanRemoval = true
       |
       v
OrderItem
```

Order items are managed as child entities of an order.

## Hibernate Features

### Bidirectional Entity Mapping

The project demonstrates bidirectional relationships, particularly between `Order` and `OrderItem`, using:

* `@OneToMany`
* `@ManyToOne`
* `mappedBy`
* `CascadeType.ALL`

### Soft Delete

Products are logically deleted instead of being physically removed from the database.

Example:

```java
@SQLDelete(sql = "UPDATE products SET active = false WHERE id = ?")
@Where(clause = "active = true")
```

### Batch Processing

Hibernate JDBC batching is configured to improve performance during bulk operations.

```properties
hibernate.jdbc.batch_size=50
```

### Orphan Removal

`orphanRemoval = true` automatically removes an `OrderItem` from the database when it is removed from its parent `Order` collection.

## N+1 Query Problem and Optimization

### Problem

While retrieving customer orders, accessing associated order items and products individually can generate multiple SQL queries.

```text
1 query  -> Fetch orders
N queries -> Fetch associated data
```

This creates the N+1 query problem and can affect application performance as the number of orders increases.

### Solution

The project uses JPQL `JOIN FETCH` to retrieve the required associations efficiently.

```java
SELECT o
FROM Order o
JOIN FETCH o.orderItems oi
JOIN FETCH oi.product
WHERE o.customer.id = :customerId
```

This reduces unnecessary database calls by retrieving the required order, order-item, and product data through an optimized query.

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

Database credentials are provided through environment variables.

```properties
spring.datasource.url=${DB_URL}
spring.datasource.username=${DB_USERNAME}
spring.datasource.password=${DB_PASSWORD}

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.jdbc.batch_size=50
```

### Local PostgreSQL

```text
DB_URL=jdbc:postgresql://localhost:5432/ecommerce
DB_USERNAME=postgres
DB_PASSWORD=your_password
```

### Neon PostgreSQL

For the deployed application, PostgreSQL is hosted on Neon.

```text
DB_URL=your_neon_database_url
DB_USERNAME=your_neon_username
DB_PASSWORD=your_neon_password
```

Database credentials should not be committed to GitHub.

## Running the Project

### Clone the Repository

```bash
git clone https://github.com/Priyank1922/MultiVendor_BackEnd.git
cd MultiVendor_BackEnd
```

### Build the Project

```bash
mvn clean install
```

### Run the Application

```bash
mvn spring-boot:run
```

The backend runs on port `8080` by default.

## Docker

Build the Docker image:

```bash
docker build -t multivendor-backend .
```

Run the Docker container:

```bash
docker run -p 8080:8080 \
-e DB_URL="your_database_url" \
-e DB_USERNAME="your_database_username" \
-e DB_PASSWORD="your_database_password" \
multivendor-backend
```

## Deployment

The backend is designed for deployment using Docker on Render with Neon PostgreSQL as the cloud database.

```text
GitHub
   |
   v
Render
   |
   v
Docker
   |
   v
Spring Boot
   |
   v
Neon PostgreSQL
```

## Testing

Run the test suite using:

```bash
mvn test
```

## Frontend Repository

The frontend application is maintained separately.

[Multi-Vendor E-Commerce Frontend](https://github.com/harshdwivedi-tech/Multivendor_Frontend.git)

The frontend communicates with the Spring Boot backend through REST APIs.

## Key Learning Outcomes

* Spring Boot backend development
* Spring Data JPA
* Hibernate ORM
* Complex entity relationship mapping
* PostgreSQL database integration
* Neon PostgreSQL
* Transaction management
* Soft-delete implementation
* Hibernate batch processing
* Cascade operations
* Orphan removal
* JPQL queries
* N+1 query optimization
* Layered backend architecture
* REST API development
* Docker containerization
* Cloud deployment
* Environment-based configuration
* Unit testing with JUnit 5

## Project Highlights

**Project Type:** Multi-Vendor E-Commerce Backend

**Primary Focus:** Scalable persistence and data-access architecture

**Backend:** Spring Boot, Spring Data JPA, Hibernate

**Database:** PostgreSQL, Neon PostgreSQL

**Optimization:** JDBC batching and N+1 query resolution

**Containerization:** Docker

**Deployment:** Render

**Frontend:** React, maintained in a separate repository
