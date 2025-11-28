# Tailor

## The Challenge

The task is to write a restaurants application in TypeScript.
Attached below are a series of steps you can take for guidance progressively.

## Mandatory deliverables

1. **Postman (or Insomnia) collection** covering every required endpoint, with ready-to-use variables/environments targeting the local backend. Ship the collection inside the repo (e.g. `docs/tailor.postman_collection.json`) so we can import it directly.
2. **Hexagonal architecture + DDD with CQS**: keep a clear split between domain/application/infrastructure layers and model commands/queries explicitly. Frameworks such as **NestJS** are recommended because they ease these patterns (modules, providers, CQRS package), but you may use another stack as long as the architecture is evident and documented.
3. **Documentation**: provide clear, reproducible guidance to run, test and understand your solution (README updates, Swagger, Postman envs, diagrams, etc.). We should be able to follow your instructions end-to-end without guessing.
4. **Postgres database**: the API must run against Postgres populated with the canonical seed provided here. Use the included `docker-compose.yml` and `postgres/init.sql` snapshot to stay aligned.

## Backend

We have a simple database with some tables.
Create a an API with the following endpoints:
At Tailor, we use TypeScript, Express and NestJS for our projects, but feel free to use any other framework you are comfortable with (as long as the architecture described above is respected).

### Public endpoints:
	1.	GET /restaurants:
	•	Pagination: page, limit.
	•	Filters: cuisine, rating, neighborhood.
	•	Sorting: sort (ascendente/descendente por campo).
	2.	GET /restaurants/:id
	3.	GET /restaurants/:id/reviews
	4.	POST /auth/login
	5.	POST /auth/register

### Authenticated endpoints (roles: USER or ADMIN):
	6.	GET /me: Information about the authenticated user.
	7.	GET /me/reviews: Reviews created by the authenticated user.
	8.	POST /restaurants/:id/reviews: Create a review.
	9.	PUT /me/reviews/:id: Edit a review.
	10.	DELETE /me/reviews/:id: Delete a review.
	11.	POST /me/favorites/:restaurantId: Add a restaurant to favorites.
	12.	DELETE /me/favorites/:restaurantId: Delete a restaurant from favorites.
	13.	GET /me/favorites: See the list of favorites.

### Admin endpoints:
	14.	POST /restaurants: Create a restaurant.
	15.	PUT /restaurants/:id: Edit a restaurant.
	16.	DELETE /restaurants/:id: Delete a restaurant.
	17.	GET /admin/stats: See statistics of: number of users, reviews, restaurants.

### Middlewares    

- Middleware for authentication
- Middleware for roles

### Scalability

Think about this as your roadmap to production. You do not need a fully running setup, but we expect to see how you would approach these topics in code and documentation:

- **Rate limiting:** implement (even if simplified) how you would enforce different limits per role (e.g. anonymous 10 req/min, authenticated 60 req/min, admin 100 req/min) and sketch in code or docs how the counters stay consistent across multiple pods/instances.
- **Caching:** implement or mock the cache layer in your read path (for example a repository adapter), clarify which data you would cache, the key structure/TTLs, and explain how the mock would be swapped for Redis/Memcached or any similar service in production. Describe the invalidation policy you would use when the source data changes.
- **Docker:** include the containers you actually need (API + Postgres). If you plan to add cache or rate-limiter services later, document them or leave commented services in `docker-compose.yml` so we understand your intent.

### Architecture diagram

Please use [excalidraw](https://excalidraw.com), [Mermaid](https://mermaid.js.org) or any similar schema tools to create a diagram of the overall architecture of your application.

- Diagram 1: expose how you designed your current application.

Imagine that after some time our application has *100.000 users per week* and some peak timings during the day with a lot of requests & traffic. 

- Diagram 2: describe how you would scale your application with these new conditions, detailing every component (API, DB, cache, rate limiter, queues, observability, etc.) and how they interact to handle the extra load and improve performance.

## Bonus points

1. Deploy the app.
2. Write realistic unit & end-to-end tests.

## Postgres database

We migrated the original `restaurants.db` (SQLite) into a Postgres snapshot so everyone works with the same dataset.

### How to run it

```bash
cd senior-backend-challenge
docker compose up -d postgres
```

Default credentials:

- Host: `localhost`
- Port: `5432`
- Database: `restaurants_challenge`
- User: `tailor`
- Password: `tailor`

The container executes `postgres/init.sql` automatically the first time the `postgres_data` volume is created. To reset the data:

```bash
docker compose down -v
docker compose up -d postgres
```

To seed a different Postgres instance manually:

```bash
psql -U tailor -d restaurants_challenge -f postgres/init.sql
```

The `postgres/init.sql` file keeps the exact schema and data from the original SQLite file, ensuring every challenger starts from the same baseline.
