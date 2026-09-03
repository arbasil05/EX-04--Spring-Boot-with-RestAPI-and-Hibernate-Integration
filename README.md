# Exp-04 - Spring Boot with REST API and Hibernate Integration

## AIM

To develop a Spring Boot application to store and retrieve data from a Movies database using Object Relational Mapping (ORM) with Hibernate and expose it via REST APIs.

## ALGORITHM

### 1. Create Spring Boot Project with Dependencies

Add the following dependencies:

* Spring Web
* Spring Data JPA
* H2 or MySQL Database

### 2. Configure `application.properties`

Configure the database connection and JPA settings.

### 3. Create Movie Entity

Create a `Movie` entity with fields such as:

* `id`
* `title`
* `genre`
* `rating`
* `year`

### 4. Create MovieRepository

Create a `MovieRepository` interface extending `JpaRepository`.

### 5. Create MovieController

Create REST endpoints for CRUD operations:

| HTTP Method | Endpoint       | Operation       |
| ----------- | -------------- | --------------- |
| GET         | `/movies`      | Get all movies  |
| GET         | `/movies/{id}` | Get movie by ID |
| POST        | `/movies`      | Add movie       |
| PUT         | `/movies/{id}` | Update movie    |
| DELETE      | `/movies/{id}` | Delete movie    |

## PROGRAM CODE

### application.properties

```properties
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

### Movie.java

```java
@Entity
public class Movie {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;
    private String genre;
    private int year;
    private double rating;

    // Getters and Setters
}
```

### MovieRepository.java

```java
public interface MovieRepository extends JpaRepository<Movie, Long> {
}
```

### MovieController.java

```java
@RestController
@RequestMapping("/movies")
public class MovieController {

    @Autowired
    private MovieRepository repo;

    @GetMapping
    public List<Movie> getAllMovies() {
        return repo.findAll();
    }

    @GetMapping("/{id}")
    public ResponseEntity<Movie> getMovieById(@PathVariable Long id) {
        return repo.findById(id)
                .map(ResponseEntity::ok)
                .orElse(ResponseEntity.notFound().build());
    }

    @PostMapping
    public Movie addMovie(@RequestBody Movie movie) {
        return repo.save(movie);
    }

    @PutMapping("/{id}")
    public ResponseEntity<Movie> updateMovie(
            @PathVariable Long id,
            @RequestBody Movie movieDetails) {

        return repo.findById(id).map(movie -> {
            movie.setTitle(movieDetails.getTitle());
            movie.setGenre(movieDetails.getGenre());
            movie.setYear(movieDetails.getYear());
            movie.setRating(movieDetails.getRating());

            return ResponseEntity.ok(repo.save(movie));
        }).orElse(ResponseEntity.notFound().build());
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteMovie(@PathVariable Long id) {
        return repo.findById(id).map(movie -> {
            repo.delete(movie);
            return ResponseEntity.ok().build();
        }).orElse(ResponseEntity.notFound().build());
    }
}
```
