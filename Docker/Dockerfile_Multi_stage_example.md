**Node.js** — dev, test, lint, prod all in one Dockerfile

```dockerfile
# Base: shared setup
FROM node:20-alpine AS base
WORKDIR /app
COPY package*.json ./

# Dev dependencies stage
FROM base AS deps
RUN npm ci

# Dev stage — hot reload, all devDependencies, source mounted via compose
FROM deps AS dev
COPY . .
CMD ["npm", "run", "dev"]

# Test stage — run in CI, never shipped
FROM deps AS test
COPY . .
RUN npm run lint
RUN npm run test -- --coverage
CMD ["npm", "test"]

# Build stage — compile for production
FROM deps AS builder
COPY . .
RUN npm run build

# Prod deps only (no devDependencies)
FROM base AS prod-deps
RUN npm ci --omit=dev

# Final production image
FROM node:20-alpine AS prod
WORKDIR /app
COPY --from=prod-deps /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist
USER node
CMD ["node", "dist/server.js"]
```

Usage:

```bash
docker build --target=test -t myapp:test .
docker build --target=prod -t myapp:prod .     # default with `docker build .` (last stage)
```

**Python** — dev vs prod with test stage

```dockerfile
FROM python:3.12-slim AS base
WORKDIR /app
COPY requirements.txt .

FROM base AS deps
RUN pip install --no-cache-dir -r requirements.txt

FROM deps AS test
RUN pip install --no-cache-dir pytest ruff
COPY . .
RUN ruff check .
RUN pytest

FROM deps AS prod
COPY . .
USER nobody
CMD ["gunicorn", "app:app", "--bind", "0.0.0.0:8000"]
```

**Go** — separate test stage (common in CI)

```dockerfile
FROM golang:1.23 AS builder
WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY . .

FROM builder AS test
RUN go vet ./...
RUN go test -race -cover ./...

FROM builder AS build
RUN CGO_ENABLED=0 go build -o /bin/app .

FROM gcr.io/distroless/static AS prod
COPY --from=build /bin/app /app
ENTRYPOINT ["/app"]
```

CI pipeline just runs:

```bash
docker build --target=test .   # fails the build if tests fail
docker build --target=prod -t myapp .
```