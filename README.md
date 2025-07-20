# 🏪 Store-X: Asset Management System API

![Go](https://img.shields.io/badge/Go-1.18+-00ADD8?style=for-the-badge&logo=go)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker)
![Gorilla Mux](https://img.shields.io/badge/Gorilla_Mux-000000?style=for-the-badge)

**Store-X** is a robust backend API for a comprehensive asset management system. It provides a full suite of endpoints for managing employees, company assets, and their entire lifecycle—from assignment and service to retrieval and auditing.

The system is built with a powerful Go backend, uses PostgreSQL for reliable data persistence, and leverages Docker for a seamless and consistent development environment. 🚀

## ✨ Features

-   🔐 **JWT Authentication:** Secure endpoints using Access & Refresh JSON Web Tokens.
-   🛡️ **Role-Based Access Control (RBAC):** Middleware to restrict endpoints to specific user roles (e.g., `admin`, `asset_manager`).
-   💻 **Complete Asset Lifecycle Management:**
    -   Create, view, and soft-delete assets.
    -   Assign assets to employees and retrieve them.
    -   Track asset service and repair history.
-   👥 **Employee Management:**
    -   Create, view, and soft-delete employees.
-   📜 **Detailed Timelines:** Get a full chronological history for any asset or employee.
-   🔍 **Dynamic Filtering & Pagination:** Powerful list APIs with universal search and easy pagination.
-   🔗 **Transactional Integrity:** Guarantees data consistency for all critical operations using database transactions.
-   🗃️ **Database Migrations:** Schema changes are managed and version-controlled with `golang-migrate`.
-   🐳 **Dockerized Environment:** Set up the database and application effortlessly with Docker Compose.

## 🏛️ Project Architecture

The project follows a clean, layered architecture to ensure separation of concerns and long-term maintainability.

-   **`handlers`**: 📡 Contains the HTTP handlers that parse requests, call business logic, and write responses.
-   **`database/dbHelper`**: 🗄️ The data access layer (Repository Pattern) responsible for all SQL queries and database interactions.
-   **`models`**: 📝 Defines the Go structs for API requests/responses and database models.
-   **`middlewares`**: 🚦 Contains middleware for authentication, authorization (RBAC), and other cross-cutting concerns.
-   **`utils`**: 🛠️ Holds helper functions for tasks like JWT generation, password hashing, and response encoding.
-   **`server`**: 🌐 Responsible for setting up the router (`gorilla/mux`) and starting the HTTP server.
-   **`database/migrations`**: 📊 Stores all SQL migration files for versioning the database schema.
-   **`seeder`**: 🌱 Contains logic to seed the database with realistic sample data for development and testing.

## 🚀 Getting Started

### Prerequisites

-   [Go](https://go.dev/doc/install) (version 1.18 or higher)
-   [Docker](https://docs.docker.com/get-docker/) & [Docker Compose](https://docs.docker.com/compose/install/)
-   [migrate](https://github.com/golang-migrate/migrate/tree/master/cmd/migrate) CLI (optional, for manual migrations)

### ⚙️ Installation & Setup

1.  **Clone the Repository:**
    ```bash
    git clone https://github.com/hemant-giri2004/Store-X.git
    cd Store-X
    ```

2.  **Configure Environment Variables:**
    Create a `.env` file by copying the example. This is where you'll put your secrets!
    ```bash
    cp .env.example .env
    ```
    Open `.env` and fill in the required values, especially `ACCESS_SECRET` and `REFRESH_SECRET`.

3.  **Start the Database with Docker:** 🐳
    This command will spin up a PostgreSQL container, ready for action.
    ```bash
    docker-compose up -d db
    ```

4.  **Install Go Dependencies:**
    ```bash
    go mod tidy
    ```

5.  **Run the Application!** 🎉
    The app will automatically connect to the database and run any pending migrations on startup.
    ```bash
    go run ./cmd
    ```
    Your server will be live at `http://localhost:8082` (or your configured port).

## 🗺️ API Endpoints

All protected endpoints require an `Authorization: Bearer <token>` header.

### 🔑 Authentication

| Method | Endpoint        | Description                               |
| :----- | :-------------- | :---------------------------------------- |
| `POST` | `/store-x/sign-in`  | Sign in with an email. Creates the user on first login. |
| `POST` | `/store-x/refresh`  | Get a new access token using a refresh token. |

### 👥 Employee Management

*Base Path: `/private/employees` (Requires `admin` or `employee_manager` role)*

| Method   | Endpoint                | Description                                         |
| :------- | :---------------------- | :-------------------------------------------------- |
| `GET`    | `/`                     | Get a paginated list of all employees with filters. |
| `DELETE` | `/{employee_id}`        | 🚮 Soft-delete (archive) an employee.                  |
| `GET`    | `/{employee_id}/timeline` | 📜 Get the asset assignment history for an employee.   |

### 💻 Asset Management

*Base Path: `/private/assets` (Requires `admin` or `asset_manager` role)*

| Method   | Endpoint                  | Description                                            |
| :------- | :------------------------ | :----------------------------------------------------- |
| `POST`   | `/`                       | ➕ Create a new asset with its specifications.            |
| `GET`    | `/`                       | 🔍 Get a paginated list of all assets with filters.       |
| `DELETE` | `/{asset_id}`             | 🚮 Soft-delete (archive) an asset.                        |
| `GET`    | `/{asset_id}/timeline`    | 📜 Get the full event history for an asset.               |
| `POST`   | `/{asset_id}/assign`      | ➡️ Assign an asset to an employee.                        |
| `POST`   | `/{asset_id}/unassign`    | ⬅️ Retrieve (un-assign) an asset from an employee.        |
| `POST`   | `/{asset_id}/service`     | 🛠️ Send an asset for service/repair.                      |
| `POST`   | `/{asset_id}/receive`     | ✅ Receive an asset back from service.                    |

### ⚙️ System (Admin Only)

*Base Path: `/private/admin` (Requires `admin` role)*

| Method | Endpoint | Description                                    |
| :----- | :------- | :--------------------------------------------- |
| `POST` | `/seed`  | 🌱 Seed the database with sample data.            |

## 🌱 Seeding the Database

To populate the database with a realistic set of sample data for easy testing, first authenticate as an `admin`, then make a request to the seed endpoint:

```bash
POST /private/admin/seed
