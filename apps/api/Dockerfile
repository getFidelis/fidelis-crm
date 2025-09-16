# apps/api/Dockerfile (Fidelis Backend / API)

# Stage 1: Install base dependencies (root node_modules)
FROM node:20-alpine AS dependencies

WORKDIR /app

# Install pnpm globally
RUN npm install -g pnpm

# Copy root lockfile and package.json
COPY pnpm-lock.yaml ./
COPY package.json ./ 
COPY turbo.json ./

# Copy all workspace package.json files so pnpm can resolve everything
# (This can be more dynamic if you have many, but explicit is clear)
COPY apps/api/package.json ./apps/api/
COPY apps/web/package.json ./apps/web/
COPY packages/ui/package.json ./packages/ui/
# Install all dependencies (development & production) for the entire monorepo
RUN pnpm install --frozen-lockfile --ignore-scripts


# Stage 2: Build the API application with Turborepo prune
FROM node:20-alpine AS builder

WORKDIR /app

# Install pnpm globally (needed for turbo commands, or ensure PATH is set)
RUN npm install -g pnpm

# Copy only the necessary dependencies and lockfile from the previous stage
COPY --from=dependencies /app/node_modules ./node_modules
COPY --from=dependencies /app/pnpm-lock.yaml ./pnpm-lock.yaml
COPY --from=dependencies /app/package.json ./package.json
COPY --from=dependencies /app/turbo.json ./turbo.json

# Copy all workspace package.json files for turbo prune context
# (It's safe to copy more than strictly necessary for prune)
COPY apps/api/package.json ./apps/api/
COPY apps/web/package.json ./apps/web/
COPY packages/ui/package.json ./packages/ui/

# Copy the entire source code (now with dependencies in node_modules)
COPY . .

# Generate a pruned subset of the monorepo specifically for the 'api' app
# This creates a 'out/json' (metadata) and 'out/full' (pruned workspace)
RUN turbo prune --scope=api --docker

# Move into the pruned workspace and build the api app
WORKDIR /app/out/full
# No need for pnpm --filter here, as 'out/full' is already a minimal workspace for 'api'
# Assumes 'api' package.json has a 'build' script
RUN pnpm build

# Stage 3: Run the Hono application in a lean production environment
FROM node:22-alpine AS runner

WORKDIR /app

# Install pnpm globally in the runner for `pnpm start` and `prisma:migrate:deploy`
RUN npm install -g pnpm

# Set environment variables for the runner
ENV NODE_ENV production
ENV PORT 3051

# Copy the generated pruned production node_modules and output from the builder stage
# /app/out/json/pnpm-lock.yaml contains only production dependencies for the pruned graph
COPY --from=builder /app/out/full/package.json ./package.json
COPY --from=builder /app/out/full/pnpm-lock.yaml ./pnpm-lock.yaml
COPY --from=builder /app/out/full/node_modules ./node_modules
COPY --from=builder /app/out/full/apps/api/dist ./apps/api/dist

# Ensure prisma client is present and dependencies are correct if `pnpm install --prod` wasn't run
# Since out/full's pnpm-lock.yaml and node_modules are for the pruned graph, this should be sufficient.
# If issues, consider `RUN pnpm install --prod` here.

# Expose the port
EXPOSE ${PORT}

# The actual start command (Prisma migrate before starting Hono)
# The docker-compose entrypoint will override this, but good to have a default
CMD ["sh", "-c", "/usr/local/share/pnpm/pnpm prisma:migrate:deploy && /usr/local/share/pnpm/pnpm start"]