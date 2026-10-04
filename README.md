# TicketPop

Development starter with React, Vite, TypeScript, Tailwind CSS, Go, Gin, and PostgreSQL. Booking, authentication, and payment features are not implemented yet.

## Structure

- `frontend/`: React application and Vite API proxy
- `backend/`: Gin API and Go tests
- `compose.yaml`: PostgreSQL, API, and frontend development services
- `.env.example`: optional local Compose settings

## Run the full stack

Install Docker Desktop and start its engine. Stop any locally running API or Vite server first to free ports 8080 and 5173.

```powershell
# Optional: copy and customize local settings
Copy-Item .env.example .env

docker compose up --build
```

Open http://localhost:5173 and click **Check connection**. It should report that the API and PostgreSQL are connected.

API endpoints:
- http://localhost:8080/health: API process health
- http://localhost:8080/ready: database readiness (503 when unavailable)

Stop with Ctrl+C, then `docker compose down`. The database volume is preserved. Changing PostgreSQL credentials in `.env` does not update an existing database volume.

## Develop locally

Start only PostgreSQL from the project root:

```powershell
docker compose up -d db
```

In a backend terminal:

```powershell
cd backend
$env:DATABASE_URL = 'postgres://ticketpop:ticketpop_local@localhost:5432/ticketpop?sslmode=disable'
go run .
```

Use your custom database credentials and port if you changed `.env`. The local Go server does not load `.env` automatically. Without DATABASE_URL, `/health` still works but `/ready` returns 503.

In a separate frontend terminal:

```powershell
cd frontend
npm ci
npm run dev
```

Vite forwards `/api/*` to http://localhost:8080. Set `API_PROXY_TARGET` before running Vite if the backend uses a different address. This proxy is for the development server; a production deployment requires separate static hosting and API routing.

## Verify

```powershell
# From backend/
go test ./...
go vet ./...
go build ./...

# From frontend/
npm run lint
npm run build

# From the project root
docker compose config --quiet
```

Compose uses local example credentials and Vite's development server. Prepare deployment configuration separately before publishing. Never commit `.env` or actual secrets.
