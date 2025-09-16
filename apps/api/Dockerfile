# apps/api/Dockerfile (für Produktion/Deployment)

# Stage 1: Build the Hono application using the full monorepo context and Turborepo
FROM node:20-alpine AS builder

WORKDIR /app

# Install pnpm globally first
RUN npm install -g pnpm

# --- KORREKTUREN FÜR DEN BUILDER STAGE ---
# Kopiere ALLE pnpm-relevanten Dateien aus dem Root und den apps/packages.
# Dies ist entscheidend, damit pnpm und turbo die Monorepo-Struktur erkennen.
COPY pnpm-lock.yaml ./
COPY package.json ./
COPY turbo.json ./
# Kopiere ALLE package.json-Dateien aus allen apps/ und packages/
COPY apps/api/package.json ./apps/api/
COPY apps/web/package.json ./apps/web/ 
COPY packages/ui/package.json ./packages/ui/

# --- ENDE KORREKTUREN FÜR DEN BUILDER STAGE ---

# Install all monorepo dependencies
RUN pnpm install --frozen-lockfile

# Copy the rest of the entire monorepo source code
# Dies sollte NACH 'pnpm install' erfolgen, um Layer-Caching für Dependencies zu optimieren.
COPY . .

RUN pnpm --filter=api build

# --- Stage 2: Run the Hono application in a lean environment ---
FROM node:22-alpine AS runner

WORKDIR /app

# Set environment variables for the runner
ENV NODE_ENV production
ENV PORT 3051 # Angepasster Port

# Re-install pnpm for the runner stage
RUN npm install -g pnpm

# Copy only the necessary runtime files from the builder stage for a Hono/Prisma app
# 1. The built API application (dist folder)
COPY --from=builder /app/apps/api/dist ./dist
# 2. The prisma schema and migrations (needed for `prisma:migrate:deploy` and runtime client)
#    Stelle sicher, dass der 'prisma'-Ordner in apps/api liegt.
COPY --from=builder /app/apps/api/prisma ./prisma
# 3. The package.json of the API (needed for `pnpm start` and `prisma:migrate:deploy`)
COPY --from=builder /app/apps/api/package.json ./package.json

# Re-install *only production* dependencies in the runner stage based on the copied package.json.
# This ensures a minimal node_modules for the runner.
RUN pnpm install --prod

# Expose the port
EXPOSE 3051

# Start the application using its defined script from the copied package.json.
# Da die package.json der API-App ins Root des Runner-Containers kopiert wurde,
# ist ein einfacher `pnpm start` ausreichend und filtert nicht mehr das gesamte Monorepo.
# Der CMD-Befehl für das `entrypoint` in `docker-compose.yml` wird dies überschreiben.
CMD ["pnpm", "start"]