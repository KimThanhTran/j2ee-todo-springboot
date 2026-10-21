# J2EE Spring Boot Coursework (COS141)

![Java](https://img.shields.io/badge/Java-17%20%7C%2021%20%7C%2025-007396?logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.x%20%7C%204.x-6DB33F?logo=springboot&logoColor=white)
![Thymeleaf](https://img.shields.io/badge/Thymeleaf-005F0F?logo=thymeleaf&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-4479A1?logo=mysql&logoColor=white)
![Maven](https://img.shields.io/badge/Maven-C71A36?logo=apachemaven&logoColor=white)

Coursework repository for **COS141** (J2EE application development) at HUTECH. It collects six progressive lab exercises, a midterm project, and a final project: **Jolliebee**, a lightweight Jira-style task and issue tracker built with Spring Boot, Thymeleaf, and MySQL.

> The final project lives in [`DoAnMonHoc/`](DoAnMonHoc/) and is documented in [its own section below](#final-project-jolliebee-doanmonhoc).

## Repository Layout

| Folder | Type | What it covers |
| --- | --- | --- |
| [`Lab01 - Console`](Lab01%20-%20Console/) | Lab 1 | Plain Java console app for managing a book list (add, delete, edit, list, search by title, filter by price, search by author) using collections and streams. |
| [`Lab02`](Lab02/) | Lab 2 | Spring Boot project setup (Spring Web MVC, Boot 4.0.2, Java 25). Bootstrap application only, no controllers yet. |
| [`Lab03`](Lab03/) | Lab 3 | Book CRUD with Spring MVC and Thymeleaf (`/books`), backed by an in-memory list. |
| [`Lab04`](Lab04/) | Lab 4 | Product and category management with Spring MVC, Thymeleaf, and validation (`/products`), backed by an in-memory list. |
| [`Lab05`](Lab05/) | Lab 5 | Product and category CRUD persisted with Spring Data JPA and MySQL, using a shared Thymeleaf layout. |
| [`Lab06`](Lab06/) | Lab 6 | Extends Lab 5 with Spring Security (form login, `USER` and `ADMIN` roles), a shopping cart, checkout, and an order list. |
| [`kiemtragiuaky`](kiemtragiuaky/) | Midterm | Course registration app: admins manage courses, students enroll and view their courses. Spring Security with form login and Google OAuth2, MySQL. |
| [`DoAnMonHoc`](DoAnMonHoc/) | Final project | Jolliebee issue tracker (see below). |

Each Spring Boot folder is an independent Maven project. Versions differ per lab because each was created at a different point in the course:

| Project | Spring Boot | Java |
| --- | --- | --- |
| Lab02 | 4.0.2 | 25 |
| Lab03 | 4.0.2 | 21 |
| Lab04 | 3.2.2 | 21 |
| Lab05, Lab06 | 3.2.5 | 21 |
| kiemtragiuaky | 3.3.5 | 21 |
| DoAnMonHoc | 4.0.4-SNAPSHOT | 17 |

## Final Project: Jolliebee (DoAnMonHoc)

A small project-management tool in the style of Jira: users sign up, create projects, track issues on a board, assign work, and discuss in comments. The project is server-rendered with Thymeleaf and exposes a REST API under `/api`.

### Features

- Registration and login with a form, plus Google OAuth2 sign-in
- Project management, including project members and per-project roles
- Create, edit, delete, and assign issues
- Issue board with `TODO`, `IN_PROGRESS`, `REVIEW`, and `DONE` columns
- Comments on issues
- Real-time notifications when an issue is assigned (Server-Sent Events)
- Dashboard, issue list, profile, and reports pages
- Role model: global `SUPER_ADMIN`, plus project roles `PROJECT_ADMIN`, `PM`, `MEMBER`, `VIEWER`, and `LEAD`

### Tech Stack

| Layer | Technology |
| --- | --- |
| Language / build | Java 17, Maven (Maven Wrapper) |
| Framework | Spring Boot 4.0.4-SNAPSHOT: Spring MVC, Spring Data JPA, Bean Validation |
| Security | Spring Security (form login, HTTP Basic, method security), OAuth2 client (Google), BCrypt password hashing |
| View | Thymeleaf, HTML, CSS, JavaScript |
| Database | MySQL (`mysql-connector-j`) |
| Tooling | Lombok, Spring Boot DevTools |

### REST Endpoints

All `/api/**` routes require authentication, except `POST /api/users` and `POST /api/login`. CSRF protection is disabled in the security configuration. Some routes carry additional method-level checks (super admin, current user, or project permissions).

**Auth and users**

| Method | Path | Notes |
| --- | --- | --- |
| `POST` | `/api/login` | Validates username and password, returns user info |
| `GET` | `/api/users` | Super admin only |
| `GET` | `/api/users/me` | Current user |
| `GET` | `/api/users/{id}` | Super admin or the user themself |
| `POST` | `/api/users` | Create user (public) |
| `PUT` | `/api/users/{id}` | Super admin or the user themself |
| `DELETE` | `/api/users/{id}` | Super admin only |

**Projects**

| Method | Path |
| --- | --- |
| `GET` | `/api/projects` |
| `GET` | `/api/projects/{id}` |
| `POST` | `/api/projects` |
| `PUT` | `/api/projects/{id}` |
| `DELETE` | `/api/projects/{id}` |
| `GET` | `/api/projects/{id}/members` |
| `POST` | `/api/projects/{projectId}/members` |
| `DELETE` | `/api/projects/{projectId}/members/{userId}` |
| `DELETE` | `/api/projects/{projectId}/members/me` |

**Issues and comments**

| Method | Path | Notes |
| --- | --- | --- |
| `GET` | `/api/issues` | All issues |
| `GET` | `/api/issues/{id}` | |
| `GET` | `/api/issues/status/{statusId}` | Filter by status |
| `GET` | `/api/issues/project/{projectId}` | Filter by project |
| `POST` | `/api/issues` | |
| `PUT` | `/api/issues/{id}` | |
| `PUT` | `/api/issues/{id}/assign/{userId}` | Requires permission to edit issues in the project |
| `DELETE` | `/api/issues/{id}` | |
| `GET` | `/api/issues/{issueId}/comments` | |
| `POST` | `/api/issues/{issueId}/comments` | |

**Notifications**

| Method | Path | Notes |
| --- | --- | --- |
| `GET` | `/api/notifications/stream` | Server-Sent Events stream |

**Web pages** (Thymeleaf): `/login`, `/register`, `/dashboard`, `/projects`, `/project/{id}`, `/project/{id}/edit`, `/issues`, `/issue/{id}`, `/profile`, `/reports`.

### Database Setup

The project uses a MySQL database named `todo_db` with `utf8mb4`.

1. Install Java 17 and MySQL (the guide targets MySQL 5.7 or later).
2. Import the schema and sample data: `mysql -u root -p < DoAnMonHoc/todo_db.sql`
3. Alternatively, create an empty `todo_db` database and let Hibernate create the tables on first run (`spring.jpa.hibernate.ddl-auto=update`). Lookup data (statuses, priorities, issue types) and sample users are seeded on startup when missing.

Full instructions, including MySQL Workbench and phpMyAdmin options, the sample data included, and the table list, are in [`DoAnMonHoc/DATABASE_SETUP.md`](DoAnMonHoc/DATABASE_SETUP.md).

### Configuration

`application.properties` is not committed to this repository (it is excluded by `.gitignore`). Create `DoAnMonHoc/src/main/resources/application.properties` before running. The project README lists the keys it expects: the `spring.datasource.*` connection settings, `server.port`, and `spring.jpa.hibernate.ddl-auto`. For Google sign-in, add your own OAuth2 client ID and secret under `spring.security.oauth2.client.registration.google.*`. Never commit real credentials.

### How to Run

```bash
cd DoAnMonHoc

# macOS / Linux
./mvnw spring-boot:run

# Windows
.\mvnw.cmd spring-boot:run
```

Then open `http://localhost:8080`.

If the Maven Wrapper does not work on your machine, install Maven and run `mvn spring-boot:run` instead.

To build a jar: `./mvnw clean package`.

### Testing

- **Automated:** the only test is a Spring Boot `contextLoads` smoke test (`ApplicationTests`). Run it with `./mvnw test`. It starts the full context, so MySQL must be reachable with the configuration above.
- **API (manual):** [`DoAnMonHoc/API_TEST_GUIDE.md`](DoAnMonHoc/API_TEST_GUIDE.md) walks through testing the REST API with Postman or Thunder Client (create user, project, issue, comment) and lists common errors. The routes above are secured, so authenticate first (session after form login, or HTTP Basic).
- **Sample accounts:** seeded users are listed in `DATABASE_SETUP.md` and are intended for local development only.

For a Vietnamese-language overview of the same project, see [`DoAnMonHoc/README.md`](DoAnMonHoc/README.md).

## Running the Labs and Midterm

Every Spring Boot folder is a standalone Maven project:

```bash
cd Lab03
./mvnw spring-boot:run    # Windows: .\mvnw.cmd spring-boot:run
```

- `Lab05`, `Lab06`, and `kiemtragiuaky` need a running MySQL server. Check the database URL, username, and password in each project's `src/main/resources/application.properties` against your local setup first.
- `Lab01 - Console` has no build file. Open it as an IntelliJ IDEA module or compile the files in `src/` with `javac`.

## Author

Tran Thi Kim Thanh - [@KimThanhTran](https://github.com/KimThanhTran)
