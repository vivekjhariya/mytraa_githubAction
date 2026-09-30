# Myntraa

A polished, Myntra-inspired fashion storefront built with React, Vite and Express. It includes a responsive catalog, search and filtering, product details, wishlist, cart drawer, coupon validation, authentication, theme toggle and a seeded product collection.

## Run locally

```bash
npm install
cp .env.example .env   # on Windows: copy .env.example .env
npm run dev

Open http://localhost:5173.
The Vite dev server does not proxy API calls by default. The frontend uses /api, so either:

run the API on port 3001, or
set VITE_API_URL=http://localhost:3001/api in .env

Production-style single server ------------------------------------------------------------------

npm run build
NODE_ENV=production npm start

The production server serves dist and the API on port 3001.
Docker Compose (app + MySQL)

cp .env.example .env   # set JWT_SECRET, MYSQL_* values
docker compose up --build

Project structure  -----------------------------------------------------------------------------

src/app             router and application providers
src/components      layout and reusable UI
src/features        auth, cart, catalog, and wishlist boundaries
src/pages           route-level screens
src/services        API clients
src/hooks           shared state hooks
src/lib             formatting and utilities
server              config, routes, controllers, services, middleware, data
.github/workflows   GitHub Actions CI/CD pipelines

Persistence and API ---------------------------------------------------------------------------------

Without database variables, signup/login and cart data use an in-memory demo store. When MySQL variables are available, users are stored in the users table; the app still falls back safely if MySQL cannot be reached.
Products are seeded in server.js and mirrored in server/data/products.js.
On startup, the API creates required tables: users, cart_items, orders, and order_items (see server/data/schema.sql). Checkout writes an order and line items to MySQL and clears the user's cart after a successful transaction.

CI/CD with GitHub Actions ------------------------------------------------------------------------------
Pipeline lives in:
text.github/workflows/ci-cd.yml

What the workflow does
On push / pull request to main:

Checkout code
Install dependencies
Security scans (e.g. Trivy)
Build Docker image
Scan image
Push image to Docker Hub (vjstylose/myntraa)
(Optional) Deploy with Docker Compose on a self-hosted runner / server

Required GitHub Secrets
Repo → Settings → Secrets and variables → Actions

DOCKERHUB_USERNAMEDocker   -> Hub username
DOCKERHUB_TOKENDocker      ->  Hub access token
JWT_SECRETApp              -> JWT secret (deploy)
MYSQL_ROOT_PASSWORDMySQL    -> root password (deploy)
MYSQL_PASSWORDMySQL        ->  app user password (deploy)
SONAR_TOKENSonarQube        ->  analysis
SONAR_HOST_URLSonarQube    ->  server URL
NVD_API_KEYOWASP         -> Dependency-Check

vjstylose/myntraa:latestLatest  -> successful main build
vjstylose/myntraa:build-<run_number>Specific  -> workflow run

Pull latest image from Docker Hub----------------------------------------------------------------------------
Bashdocker pull vjstylose/myntraa:latest
Deploy from published image------------------------------------------------------------------------
# ensure .env has JWT_SECRET, MYSQL_ROOT_PASSWORD, MYSQL_DATABASE, MYSQL_USER, MYSQL_PASSWORD
docker compose pull
docker compose up -d

Notes
This is a self-contained demo storefront. Before a public launch:

replace JWT_SECRET
use HTTPS
add a real payment provider
add production-grade validation and rate limiting
do not commit real .env files (keep secrets in GitHub Actions secrets / server env)



