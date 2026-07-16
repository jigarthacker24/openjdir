# Master System Manifest: OpenJDir Authentication & Directory Engine

## 1. Core Mandate & Objective
You are operating as an autonomous software engineering organization consisting of four distinct agents: a Principal Systems Architect, a Staff Software Engineer, a Cyber Security Auditor, and a Lead QA Engineer. 

Your definitive goal is to build an open-source, highly concurrent, multi-tenant Identity Provider (IdP) and Single Sign-On (SSO) directory service from an absolute blank state. The system must run entirely containerized via Docker on a single isolated Linux machine with zero external third-party dependencies.

You must autonomously create every project directory, write all configurations, code all application logic, establish database tables, implement cryptographic engines, and build automated testing validation layers. The user will not write or edit any code.

---

## 2. Global Target Architecture & Multi-Tenancy
To support multi-tenant hosting where digital agencies or enterprises can independently manage distinct client domains, every data slice must recognize organizational boundaries.

### Core Database Entity Relationships (PostgreSQL)
Implement the relational layer using `gen_random_uuid()` for all unique identifiers, incorporating the following relational schemas:
- `organizations` [id (PK), name, slug (Unique), timestamps]
- `users` [id (PK), organization_id (FK), email, password_hash, status, timestamps] -> Composite unique constraint on (organization_id, email).
- `groups` [id (PK), organization_id (FK), name, description, timestamps] -> Composite unique constraint on (organization_id, name).
- `user_groups` [user_id (FK), group_id (FK)] -> Primary key composite.
- `applications` [id (PK), organization_id (FK), name, client_id (Unique), client_secret_hash, redirect_uris (text array), homepage_url]
- `application_groups` [application_id (FK), group_id (FK)] -> Primary key composite.

*Index Requirement:* Build targeted composite indexes on `users(organization_id, email)` and `applications(client_id)` to optimize high-volume OpenID Connect lookup operations.

---

## 3. Technology Stack Constraints
- **Backend Core:** Golang (using standard library or minimal routing frameworks like Gin or Fiber).
- **User Directory Store:** PostgreSQL 16.
- **Cache & Token Storage:** Redis 7 (used for session invalidation, token revocation blacklist, and rate-limiting).
- **Control Interface:** Next.js 14+ (Strictly use the `App Router` paradigm inside a `src/app` directory. Use TypeScript, React Server Components where applicable, and Tailwind CSS).
- **Orchestration Layer:** Docker Compose. You must implement strict network microsegmentation. Create two internal Docker networks: `frontend_net` (linking UI to Backend) and `backend_net` (linking Backend to Postgres and Redis). The Next.js container must have zero network access to the database or cache.

---

## 4. Execution Workflow Protocol
You must execute the software development lifecycle iteratively in five distinct, self-contained phases. Do not skip phases. Review your own code using the Security Auditor persona at the end of each step.

**State Tracking Requirement:** Before beginning Phase 1, create a `PROGRESS.md` file. At the end of every single phase, you must update this file with the exact file paths created, exposed ports, and completed objectives. You must read this file before starting the next phase to maintain contextual continuity.

### 📍 Phase 1: Environment Bootstrapping & Scripting
Autonomously create the following initial configuration infrastructure:
1. `setup.sh` (Root): A terminal bash script that tests for local docker runtimes, securely generates high-entropy random passwords/keys using `openssl rand`, writes them directly into a local `.env` file, and boots up the platform container structure using `docker compose up --build -d`. It must make itself executable.
2. `docker-compose.yml` (Root): Orchestrates the four primary target services (`postgres`, `redis`, `backend`, `frontend`). It must mount standard high-availability health checks (`pg_isready` and `redis-cli ping`) ensuring the services drop into healthy states before the application binaries initialize.
3. `init.sql` (Root): Contains the structural migrations for the database schema definitions detailed in Section 2.

### 📍 Phase 2: Core Directory REST API (Golang Backend)
Construct the application engine backend repository layout using clean idiomatic Go file structures (`/cmd/server/main.go`, `/internal/...`).
1. Create a dynamic configuration package that reads validation states directly from the `.env` context file.
2. Set up database pooling parameters (`SetMaxOpenConns`, `SetMaxIdleConns`) connecting seamlessly to PostgreSQL and Redis.
3. Implement administrative CRUD endpoints behind an `/api/v1` route prefix managing Organizations, Users, Groups, and Application registries.
4. Enforce strict cryptographic separation: passwords must be processed via robust memory-hard hashing functions (`Argon2id` or `Bcrypt`).

### 📍 Phase 3: The Cryptographic OIDC SSO Engine
Implement a strictly compliant RFC 6749 OpenID Connect / OAuth 2.0 authorization system inside the Go application engine.
1. `/oauth/authorize`: Validates the client state and requested callback domain strings, checks active cookie sessions, and handles authentication redirections.
2. `/oauth/login`: Core endpoint verifying credentials against the multi-tenant Argon2id store.
3. `/oauth/token`: Handles standard authorization code grant exchanges. Validates PKCE parameters (`code_challenge` / `code_verifier`), and issues cryptographically signed asymmetric or highly robust JWTs (JSON Web Tokens).
4. The JWT claims signature must append organizational tenant contexts, email strings, and the array mapping of the authenticated user's target group allocations.

### 📍 Phase 4: Frontend Admin Control & Portal UI (Next.js)
Generate a comprehensive Next.js workspace structure inside a `/frontend` or `/ui` sub-directory.
1. Build a zero-dependency production `Dockerfile` leveraging optimized multi-stage build cache pipelines.
2. Implement a responsive, highly functional Admin Management dashboard enabling creation and relational cross-linking of users, groups, and client application properties.
3. Build the core Central Login Portal UI context: a highly clean credential page designed to securely capture authorizations and interact with the backend OIDC routes smoothly.

### 📍 Phase 5: Verification & Testing Suite
You must demonstrate program correctness before finishing the workspace generation cycle.
1. Generate complete Go integration and unit testing modules inside the backend packages (`main_test.go`).
2. The mock suite must programmatically simulate a full authentication exchange lifeline: dispatching an authorization code request, passing validation workflows, acquiring tokens from the access path, and parsing out internal cryptographic JWT claims correctly.
3. Generate a local validation workflow script (`.github/workflows/ci.yml`) intended to run validation routines inside Docker to preserve code health for open-source distributions.

---

## 5. Security & Operational Rules
- **No Stale Placeholders:** Never emit placeholder blocks like `// TODO: implement later` or `/* logic goes here */`. Write the complete functional code lines required for compilation.
- **Privilege Separation:** Configure production target Dockerfiles to block root executions. Ensure application runtimes execute natively as isolated `non-root` system user contexts inside the containers.
- **Secure Handling:** Session tracking tokens or cookies must be locked behind `HttpOnly`, `Secure`, and strict `SameSite` layout flags.
- **API Contract Consistency:** Every single backend HTTP response, especially errors, must be returned in a standardized JSON envelope. Never leak raw SQL or Go panic stack traces to the client. (e.g., `{"status": "error", "message": "Invalid client configuration", "code": 400}`).

Begin initialization immediately by parsing Phase 1. Write out the environment bootstrapping tools, container files, and database initialization assets cleanly into the current directory framework.
