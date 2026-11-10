# Sri Kanth — DevOps Portfolio

A responsive five-page React portfolio and Express contact API. Unknown contact details and unprovided project repositories are marked as placeholders; no work history or credentials are invented.

## Requirements

- Node.js 22+ and npm
- Docker Desktop with Compose (optional)

## Run locally

In one terminal:

```powershell
cd backend
npm install
Copy-Item .env.example .env
npm run dev
```

The API runs at http://localhost:4000. Its health endpoint is http://localhost:4000/api/health. Contact form submissions are appended to `backend/data/messages.json`; no email is sent.

In a second terminal:

```powershell
cd frontend
npm install
npm run dev
```

Open http://localhost:5173. Vite proxies `/api` requests to the backend on port 4000. `VITE_API_URL` is only needed if you intentionally host the API elsewhere; Vite frontend configuration is public and must not contain secrets.

## Docker Compose

From the repository root:

```powershell
docker compose up --build
```

Frontend: http://localhost:8080. API health: http://localhost:4000/api/health. The Nginx frontend proxies `/api` requests to the backend service. Compose persists submissions in the `contact-data` volume. Stop with `docker compose down`; remove stored submissions too with `docker compose down -v`.

Build images separately:

```powershell
docker build -t srikanth-portfolio-frontend:latest ./frontend
docker build -t srikanth-portfolio-backend:latest ./backend
```

## Docker Hub

Create repositories in Docker Hub, then authenticate interactively (do not put a token in source):

```powershell
docker login
docker build -t YOUR_DOCKERHUB_USERNAME/srikanth-portfolio-frontend:latest ./frontend
docker build -t YOUR_DOCKERHUB_USERNAME/srikanth-portfolio-backend:latest ./backend
docker push YOUR_DOCKERHUB_USERNAME/srikanth-portfolio-frontend:latest
docker push YOUR_DOCKERHUB_USERNAME/srikanth-portfolio-backend:latest
```

## Jenkins pipeline

Create a Pipeline job configured to use this repository's `Jenkinsfile` (or choose Pipeline from SCM and provide the repository URL and branch). A reachable Jenkins agent needs Node.js/npm, Docker Engine, Docker Compose, and curl, and permission to run Docker. The pipeline checks out source, installs dependencies, builds the frontend, validates backend syntax, builds both images, starts the local Compose deployment, and checks frontend/API health. It targets the agent's local Docker host; it does not assume a production server or credentials. Docker Hub publishing can be added with Jenkins credentials and a reviewed login/push stage.

## Configuration and data

Copy `backend/.env.example` to `backend/.env` to configure API port, allowed frontend origin, and data path. For a split-port local setup, set `VITE_API_URL` as described above. Messages are persisted in a local JSON file in development and a named volume in Compose. Restrict access and retention appropriately before exposing this demo publicly. To add email later, configure a mail provider in the backend with server-side environment variables; never expose its credentials in Vite variables.

## Personal details to add

- Actual email address and LinkedIn profile, if available
- Actual resume file (replace the downloadable placeholder under `frontend/public`)
- Repository links and verified details for the Dockerized Web Application and Docker Compose Application projects
- A deployment host/domain only if you later choose to publish the site
