# Context & Objective
You are an expert Staff Backend Engineer and Full-Stack Developer. Your goal is to build an open-source Identity Provider (IdP) and Single Sign-On (SSO) directory service from scratch. The platform will allow administrators to manage users, groups, and applications (portals), and will provide a central OIDC-compliant SSO login flow to govern access based on group permissions.

This project is intended to be open-sourced, meaning the code must be modular, highly readable, well-documented, and easy for other developers to self-host.

# Tech Stack Requirements
*   **Backend:** Golang (using Gin or Fiber framework) for high performance and concurrency.
*   **Frontend:** Next.js (React) with TypeScript and Tailwind CSS.
*   **Database (User Store):** PostgreSQL (using sqlc or GORM) for persistent storage of users, groups, apps, and relational mappings.
*   **Cache & Sessions:** Redis for short-lived access tokens, session states, and rate limiting.
*   **Containerization:** Docker & Docker Compose for an easy open-source self-hosting experience.

# Core Architecture & Features
1.  **Identity Directory (PostgreSQL)**
    *   Users: `id`, `email`, `password_hash`, `first_name`, `last_name`, `is_active`, `created_at`.
    *   Groups: `id`, `name`, `description`.
    *   User_Groups (Join Table): `user_id`, `group_id`.
2.  **App Registry**
    *   Applications: `id`, `name`, `client_id`, `client_secret_hash`, `redirect_uris` (array), `homepage_url`.
    *   App_Groups (Join Table): `app_id`, `group_id` (defines which groups have access to which apps).
3.  **Authentication & SSO Engine**
    *   Implementation of standard OAuth 2.0 / OpenID Connect (OIDC) endpoints:
        *   `/authorize`: Validates client, checks session, redirects to login if needed.
        *   `/login`: UI for user credentials.
        *   `/token`: Exchanges authorization code for an Access Token and ID Token (JWT).
        *   `/userinfo`: Returns claims about the authenticated user.
4.  **Admin Dashboard (Frontend)**
    *   CRUD interfaces for Users, Groups, and Applications.
    *   UI to assign users to groups, and groups to applications.

# Coding Standards & Open Source Guidelines
*   **Security First:** Passwords must be hashed using bcrypt/argon2. Never log sensitive tokens or secrets. Implement CSRF protection and secure, HttpOnly cookies for sessions.
*   **Project Structure:** Use standard Go project layout (e.g., `/cmd`, `/internal`, `/pkg`). Separate the Next.js frontend into a `/web` or `/ui` directory.
*   **Configuration:** All configurations (DB strings, Redis URLs, JWT secrets) must be injected via environment variables (`.env`). Provide an `.env.example` file.
*   **Error Handling:** Return consistent JSON error responses across all APIs.

# Execution Plan (Iterative Implementation)
Do not build everything at once. We will proceed in the following phases. Wait for my confirmation to move to the next phase.

*   **Phase 1: Infrastructure & DB Setup:** Scaffold the Go project, configure PostgreSQL/Redis connections, write the SQL schema migrations, and generate data access models.
*   **Phase 2: Core Directory CRUD API:** Build the REST API endpoints to manage Users, Groups, and Apps, including their relationships.
*   **Phase 3: The Authentication Engine:** Implement the OIDC flow (`/authorize`, `/login`, `/token`), JWT generation, and password hashing.
*   **Phase 4: Admin Dashboard (Next.js):** Scaffold the Next.js app, connect it to the backend APIs, and build the UI for managing the directory.
*   **Phase 5: Containerization & Docs:** Write the `Dockerfile`, `docker-compose.yml`, and the open-source `README.md`.

Let's begin with Phase 1. Scaffold the Go project structure, write the SQL schema for our PostgreSQL database, and set up the initial connection logic.