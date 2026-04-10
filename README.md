# UBJH Server

Backend API for the University of Benin Journal of Humanities (UBJH). This service handles manuscript submission, review workflows, article publication, indexing metadata, DOI registration, and public article access.

## Overview

The project is a Node.js + Express + TypeScript server backed by MongoDB.

It supports two publication paths:

1. Review-driven publication
- A manuscript is submitted and reviewed.
- Admin approves it.
- An `Article` record is created in a pending state (`isPublished: false`).
- Admin publishes it into a specific volume/issue.

2. Manual publication (bypass)
- Admin uploads and publishes directly (useful for archives/migrations/special publications).

When an article is published, background jobs can run via Agenda for DOI registration, indexing metadata, preservation upload, and subscriber notifications.

## Core Features

- Auth for admin, author, and reviewer roles.
- Manuscript submission and review pipeline.
- Publication management:
- Volume and issue CRUD.
- Publish approved articles.
- Manual article upload and publishing.
- Public journal endpoints:
- Archives, current issue, article details, search.
- Citation formats and metadata endpoints.
- Analytics tracking (views/downloads/popular articles).
- Email subscription and unsubscribe flow.
- Failed background job tracking and retry.
- Dynamic admin email campaign endpoints.

## Tech Stack

- Runtime: Node.js
- Framework: Express 5
- Language: TypeScript
- Database: MongoDB + Mongoose
- Job queue: Agenda
- Auth: JWT
- Validation: Joi, Zod, Envalid
- Docs: Swagger UI (`swagger.yaml`)
- Logging: Winston + Morgan
- File upload: Multer

## Project Structure

Primary folders in [src/](src/):

- [Articles/](src/Articles/): Article models/controllers/routes and analytics.
- [Manuscript_Submission/](src/Manuscript_Submission/): Submission workflow.
- [Review_System/](src/Review_System/): Review and decision workflow.
- [Publication/](src/Publication/): Volumes, issues, publication, citations, subscriptions, failed jobs.
- [authors/](src/authors/): Author dashboard/manuscript/co-author flows.
- [routes/](src/routes/): Main route composition and auth/admin routes.
- [config/agenda.ts](src/config/agenda.ts): Background job definitions.
- [worker.ts](src/worker.ts): Agenda worker entrypoint.

## Getting Started

### 1. Install dependencies

```bash
npm install
```

### 2. Configure environment

Create a `.env` file at the repository root.

Required variables validated at startup:

- `NODE_ENV` (`development` | `test` | `production`)
- `PORT`
- `MONGODB_URI`
- `FRONTEND_URL`
- `API_URL`
- `LOG_LEVEL`
- `JWT_ACCESS_SECRET`
- `JWT_REFRESH_SECRET`
- `SMTP_HOST`
- `SMTP_PORT`
- `SMTP_USER`
- `SMTP_PASS`
- `EMAIL_FROM`
- `REVIEWER_EMAILS` (comma-separated)
- `ADMIN_NAME`
- `ADMIN_EMAIL`
- `ADMIN_PASSWORD`
- `SUPPORT_EMAIL`

Optional integration variables used by publication services:

- `CROSSREF_USERNAME`
- `CROSSREF_PASSWORD`
- `CROSSREF_DOI_PREFIX`
- `CROSSREF_DEPOSITOR_NAME`
- `CROSSREF_DEPOSITOR_EMAIL`
- `JOURNAL_ISSN`
- `INTERNET_ARCHIVE_ACCESS_KEY`
- `INTERNET_ARCHIVE_SECRET_KEY`

### 3. Run in development

```bash
npm run dev
```

### 4. Run worker (recommended for publication jobs)

```bash
npm run worker
```

### 5. Build and run production bundle

```bash
npm run build
npm start
```

## NPM Scripts

- `npm run dev`: Start API in dev mode using `nodemon` + `ts-node`.
- `npm run worker`: Start Agenda worker.
- `npm run build`: Compile TypeScript to `dist/`.
- `npm start`: Run compiled server from `dist/index.js`.
- `npm run lint`: Run ESLint.
- `npm run lint:fix`: Run ESLint with auto-fixes.
- `npm run clean`: Remove `dist/`.
- `npm run seed:admin`: Seed/create admin user.

## API Base and Docs

- Base API prefix: `/api/v1`
- Swagger UI: `/api-docs`

## Key Endpoint Groups

Mounted through [src/routes/index.ts](src/routes/index.ts):

- `/api/v1/auth/*` authentication routes.
- `/api/v1/admin/*` admin manuscript/review/management routes.
- `/api/v1/submit/*` manuscript submission routes.
- `/api/v1/author/*`, `/api/v1/revise-manuscript/*`, `/api/v1/co-author/*` author routes.
- `/api/v1/reviewer/*` reviewer routes.
- `/api/v1/reviewsys/*` review system routes.
- `/api/v1/publication/*` publication, archives, citation, analytics, and subscription routes.

Examples in publication module:

- Admin:
- `POST /api/v1/publication/volumes`
- `POST /api/v1/publication/issues`
- `GET /api/v1/publication/publications/pending`
- `POST /api/v1/publication/publications/:articleId/publish`
- `POST /api/v1/publication/publications/manual`
- `GET /api/v1/publication/failed-jobs`
- `POST /api/v1/publication/failed-jobs/:id/retry`

- Public:
- `GET /api/v1/publication/articles`
- `GET /api/v1/publication/articles/:id`
- `GET /api/v1/publication/articles/search?query=...`
- `GET /api/v1/publication/current-issue`
- `GET /api/v1/publication/archives`
- `POST /api/v1/publication/subscribe`
- `GET /api/v1/publication/articles/:id/citation`
- `GET /api/v1/publication/articles/:id/metadata`
- `POST /api/v1/publication/article-analytics/:id/view`
- `POST /api/v1/publication/article-analytics/:id/download`

## Publication Background Jobs

Configured in [src/config/agenda.ts](src/config/agenda.ts):

- `register-doi`
- `generate-indexing-metadata`
- `upload-to-archive`
- `send-publication-notification`
- `publish-article` (coordinator)

Failed jobs are stored and can be retried through the failed jobs controller endpoints.

## Upload Directories

The server ensures upload folders exist at startup. Main folders under [src/uploads/](src/uploads/):

- `documents/`
- `manual_articles/`
- `volume_covers/`
- `email-attachments/`

## Notes

- Automated tests are not currently configured in `package.json`.
- Keep the API and worker running together for complete publication workflows.
- For DOI and preservation features, ensure external service credentials are present in `.env`.
