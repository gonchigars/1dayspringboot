# Step-by-Step Spring Boot Tutorial: Building a Movie API Application

In this tutorial, we will build a Spring Boot application from scratch using Spring Initializr. The application will expose three RESTful APIs for fetching popular, comedy, and action movies. We'll use an H2 in-memory database to store our movie data and provide scripts to create and update the database tables.

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Setting Up the Project](#setting-up-the-project)
3. [Project Structure](#project-structure)
4. [Configuring the H2 Database](#configuring-the-h2-database)
5. [Creating the Movie Entity](#creating-the-movie-entity)
6. [Creating the Repository](#creating-the-repository)
7. [Creating the Service Layer](#creating-the-service-layer)
8. [Creating the Controllers](#creating-the-controllers)
9. [Defining the APIs](#defining-the-apis)
10. [Initializing the Database](#initializing-the-database)
11. [Testing the APIs](#testing-the-apis)
12. [Database Scripts](#database-scripts)
13. [Conclusion](#conclusion)

---

## Prerequisites

- Java Development Kit (JDK) 8 or higher
- Maven or Gradle build tool
- An IDE like IntelliJ IDEA, Eclipse, or Spring Tool Suite
- Basic knowledge of Java and Spring Boot

---

## Setting Up the Project

### 1. Using Spring Initializr

- Navigate to [Spring Initializr](https://start.spring.io/).
- **Project:** Maven Project
- **Language:** Java
- **Spring Boot:** Latest stable version (e.g., 2.7.x)
- **Project Metadata:**
  - **Group:** `com.example`
  - **Artifact:** `movieapi`
  - **Name:** `movieapi`
  - **Package Name:** `com.example.movieapi`
  - **Packaging:** Jar
  - **Java Version:** 11 (or your installed version)
- **Dependencies:**
  - Spring Web
  - Spring Data JPA
  - H2 Database

Click on **Generate** to download the project. Extract the zip file to your desired location.

### 2. Importing the Project

- Open your IDE.
- Import the project as a Maven project.
- Wait for the dependencies to download.

---

## Project Structure

Your project structure should look like this:

```
movieapi
├── src
│   ├── main
│   │   ├── java
│   │   │   └── com.example.movieapi
│   │   │       ├── MovieApiApplication.java
│   │   │       ├── controller
│   │   │       ├── entity
│   │   │       ├── repository
│   │   │       └── service
│   │   └── resources
│   │       ├── application.properties
│   │       └── data.sql
│   └── test
└── pom.xml
```

---

## Configuring the H2 Database

Open the `application.properties` file and configure the H2 database:

```properties
# H2 Database Configuration
spring.datasource.url=jdbc:h2:mem:movie_db;DB_CLOSE_DELAY=-1
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

# JPA Configuration
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.jpa.hibernate.ddl-auto=update

# H2 Console Configuration
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console
```

---

## Creating the Movie Entity

Create a new Java class `Movie` in the `entity` package:

```java
package com.example.movieapi.entity;

import javax.persistence.*;

@Entity
@Table(name = "movies")
public class Movie {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;
    private String genre;
    private boolean isPopular;

    // Constructors
    public Movie() {
    }

    public Movie(String title, String genre, boolean isPopular) {
        this.title = title;
        this.genre = genre;
        this.isPopular = isPopular;
    }

    // Getters and Setters
    // (Generate getters and setters for all fields)
}
```

---

## Creating the Repository

Create a new interface `MovieRepository` in the `repository` package:

```java
package com.example.movieapi.repository;

import com.example.movieapi.entity.Movie;
import org.springframework.data.jpa.repository.JpaRepository;
import java.util.List;

public interface MovieRepository extends JpaRepository<Movie, Long> {
    List<Movie> findByGenre(String genre);
    List<Movie> findByIsPopularTrue();
}
```

---

## Creating the Service Layer

Create a new class `MovieService` in the `service` package:

```java
package com.example.movieapi.service;

import com.example.movieapi.entity.Movie;
import com.example.movieapi.repository.MovieRepository;
import org.springframework.stereotype.Service;
import java.util.List;

@Service
public class MovieService {

    private final MovieRepository movieRepository;

    public MovieService(MovieRepository movieRepository) {
        this.movieRepository = movieRepository;
    }

    public List<Movie> getPopularMovies() {
        return movieRepository.findByIsPopularTrue();
    }

    public List<Movie> getMoviesByGenre(String genre) {
        return movieRepository.findByGenre(genre);
    }
}
```

---

## Creating the Controllers

Create a new class `MovieController` in the `controller` package:

```java
package com.example.movieapi.controller;

import com.example.movieapi.entity.Movie;
import com.example.movieapi.service.MovieService;
import org.springframework.web.bind.annotation.*;
import java.util.List;

@RestController
@RequestMapping("/api/movies")
public class MovieController {

    private final MovieService movieService;

    public MovieController(MovieService movieService) {
        this.movieService = movieService;
    }

    @GetMapping("/popular")
    public List<Movie> getPopularMovies() {
        return movieService.getPopularMovies();
    }

    @GetMapping("/genre/{genre}")
    public List<Movie> getMoviesByGenre(@PathVariable String genre) {
        return movieService.getMoviesByGenre(genre);
    }
}
```

---

## Defining the APIs

Our application exposes the following APIs:

1. **Get Popular Movies**

   - **URL:** `/api/movies/popular`
   - **Method:** `GET`
   - **Description:** Retrieves a list of popular movies.

2. **Get Movies by Genre**

   - **URL:** `/api/movies/genre/{genre}`
   - **Method:** `GET`
   - **Description:** Retrieves a list of movies by genre (e.g., comedy, action).

---

## Initializing the Database

Create a `data.sql` file in the `resources` directory to initialize the database with sample data:

```sql
INSERT INTO movies (title, genre, is_popular) VALUES ('The Shawshank Redemption', 'Drama', TRUE);
INSERT INTO movies (title, genre, is_popular) VALUES ('The Godfather', 'Crime', TRUE);
INSERT INTO movies (title, genre, is_popular) VALUES ('The Dark Knight', 'Action', TRUE);
INSERT INTO movies (title, genre, is_popular) VALUES ('Pulp Fiction', 'Crime', TRUE);
INSERT INTO movies (title, genre, is_popular) VALUES ('The Hangover', 'Comedy', FALSE);
INSERT INTO movies (title, genre, is_popular) VALUES ('Superbad', 'Comedy', FALSE);
INSERT INTO movies (title, genre, is_popular) VALUES ('Mad Max: Fury Road', 'Action', FALSE);
INSERT INTO movies (title, genre, is_popular) VALUES ('Die Hard', 'Action', FALSE);
```

---

## Testing the APIs

### 1. Run the Application

Run the `MovieApiApplication` main class to start the application.

### 2. Access the H2 Console (Optional)

- Open a browser and navigate to `http://localhost:8080/h2-console`.
- Use the JDBC URL `jdbc:h2:mem:movie_db` and click **Connect** to view the database.

### 3. Test the APIs Using Postman or Browser

- **Get Popular Movies**

  ```
  GET http://localhost:8080/api/movies/popular
  ```

  **Response:**

  ```json
  [
    {
      "id": 1,
      "title": "The Shawshank Redemption",
      "genre": "Drama",
      "isPopular": true
    },
    {
      "id": 2,
      "title": "The Godfather",
      "genre": "Crime",
      "isPopular": true
    },
    {
      "id": 3,
      "title": "The Dark Knight",
      "genre": "Action",
      "isPopular": true
    },
    {
      "id": 4,
      "title": "Pulp Fiction",
      "genre": "Crime",
      "isPopular": true
    }
  ]
  ```

- **Get Comedy Movies**

  ```
  GET http://localhost:8080/api/movies/genre/Comedy
  ```

  **Response:**

  ```json
  [
    {
      "id": 5,
      "title": "The Hangover",
      "genre": "Comedy",
      "isPopular": false
    },
    {
      "id": 6,
      "title": "Superbad",
      "genre": "Comedy",
      "isPopular": false
    }
  ]
  ```

- **Get Action Movies**

  ```
  GET http://localhost:8080/api/movies/genre/Action
  ```

  **Response:**

  ```json
  [
    {
      "id": 3,
      "title": "The Dark Knight",
      "genre": "Action",
      "isPopular": true
    },
    {
      "id": 7,
      "title": "Mad Max: Fury Road",
      "genre": "Action",
      "isPopular": false
    },
    {
      "id": 8,
      "title": "Die Hard",
      "genre": "Action",
      "isPopular": false
    }
  ]
  ```

---

## Database Scripts

### 1. Table Creation Script

If you need to create the table manually, use the following SQL script:

```sql
CREATE TABLE movies (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    genre VARCHAR(50) NOT NULL,
    is_popular BOOLEAN NOT NULL
);
```

### 2. Data Insertion Script

Insert data into the `movies` table:

```sql
INSERT INTO movies (title, genre, is_popular) VALUES
('The Shawshank Redemption', 'Drama', TRUE),
('The Godfather', 'Crime', TRUE),
('The Dark Knight', 'Action', TRUE),
('Pulp Fiction', 'Crime', TRUE),
('The Hangover', 'Comedy', FALSE),
('Superbad', 'Comedy', FALSE),
('Mad Max: Fury Road', 'Action', FALSE),
('Die Hard', 'Action', FALSE);
```

### 3. Update Script

To update existing records, for example, mark "Die Hard" as popular:

```sql
UPDATE movies SET is_popular = TRUE WHERE title = 'Die Hard';
```

---

## Conclusion

