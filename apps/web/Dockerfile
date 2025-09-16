# apps/web/Dockerfile (für Produktion/Deployment)

# Stage 1: Build the Next.js application using the full monorepo context and Turborepo
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

COPY apps/web/package.json ./apps/web/
COPY packages/ui/package.json ./packages/ui/

# --- ENDE KORREKTUREN FÜR DEN BUILDER STAGE ---

# Install all monorepo dependencies
RUN pnpm install --frozen-lockfile

# Copy the entire monorepo source code
# Dies sollte NACH 'pnpm install' erfolgen, um Layer-Caching für Dependencies zu optimieren.
COPY . .

# Build the specific web application using Turborepo filter
RUN pnpm --filter=web build

# --- Stage 2: Run the Next.js application in a lean environment ---
FROM node:22-alpine AS runner

WORKDIR /app

# Set environment variables for the runner
ENV NODE_ENV production
ENV PORT 3050

# Re-install pnpm (or ensure it's in PATH if `node:22-alpine` has it)
# Since `node:22-alpine` is used, it's safer to install pnpm again for standalone execution.
RUN npm install -g pnpm

# Copy necessary files from the build stage for Next.js standalone output
# Die Pfade müssen jetzt "apps/web" berücksichtigen, da dies der Ordner ist.
COPY --from=builder /app/apps/web/.next/standalone ./
COPY --from=builder /app/apps/web/.next/static ./.next/static
COPY --from=builder /app/apps/web/public ./public
# Copy the specific app's package.json to enable `pnpm start` in the runner stage
COPY --from=builder /app/apps/web/package.json ./package.json

# If `pnpm start` from the copied package.json relies on `node_modules` in its direct context,
# you might need to run `pnpm install --prod` here.
# However, Next.js `output: 'standalone'` usually bundles almost everything.
# If you encounter "module not found" errors, uncomment and run:
# RUN pnpm install --prod

# Expose the port
EXPOSE 3050

# Start the application using its defined script from the copied package.json.
# Da die package.json der Web-App ins Root des Runner-Containers kopiert wurde,
# ist ein einfacher `pnpm start` ausreichend und filtert nicht mehr das gesamte Monorepo.
CMD ["pnpm", "start"]