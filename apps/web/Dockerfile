# apps/web/Dockerfile (Fidelis Frontend / Web)

# Stage 1: Install base dependencies (root node_modules)
FROM node:20-alpine AS dependencies

WORKDIR /app

# Install pnpm globally
RUN npm install -g pnpm

# Copy root lockfile and package.json
COPY pnpm-lock.yaml ./
COPY package.json ./ 
COPY turbo.json ./

# Copy all workspace package.json files
COPY apps/api/package.json ./apps/api/
COPY apps/web/package.json ./apps/web/
COPY packages/ui/package.json ./packages/ui/

# Install all dependencies (development & production) for the entire monorepo
RUN pnpm install --frozen-lockfile --ignore-scripts


# Stage 2: Build the Web application with Turborepo prune
FROM node:20-alpine AS builder

WORKDIR /app

# Install pnpm globally
RUN npm install -g pnpm

# Copy only the necessary dependencies and lockfile from the previous stage
COPY --from=dependencies /app/node_modules ./node_modules
COPY --from=dependencies /app/pnpm-lock.yaml ./pnpm-lock.yaml
COPY --from=dependencies /app/package.json ./package.json
COPY --from=dependencies /app/turbo.json ./turbo.json

# Copy all workspace package.json files for turbo prune context
COPY apps/api/package.json ./apps/api/
COPY apps/web/package.json ./apps/web/
COPY packages/ui/package.json ./packages/ui/

# Copy the entire source code (now with dependencies in node_modules)
COPY . .

# Generate a pruned subset of the monorepo specifically for the 'web' app
RUN turbo prune --scope=web --docker

# Move into the pruned workspace and build the web app
WORKDIR /app/out/full
# No need for pnpm --filter here, as 'out/full' is already a minimal workspace for 'web'
# Assumes 'web' package.json has a 'build' script
RUN pnpm build


# Stage 3: Run the Next.js application in a lean production environment
FROM node:22-alpine AS runner

WORKDIR /app

# Install pnpm globally in the runner for `pnpm start`
RUN npm install -g pnpm

# Set environment variables for the runner
ENV NODE_ENV production
ENV PORT 3050

# Copy the generated pruned production node_modules and output from the builder stage
COPY --from=builder /app/out/full/package.json ./package.json
COPY --from=builder /app/out/full/pnpm-lock.yaml ./pnpm-lock.yaml
COPY --from=builder /app/out/full/node_modules ./node_modules
# For Next.js, copy the standalone output. Ensure `output: 'standalone'` in next.config.js
COPY --from=builder /app/out/full/apps/web/.next/standalone ./
COPY --from=builder /app/out/full/apps/web/.next/static ./.next/static
COPY --from=builder /app/out/full/apps/web/public ./public

# Expose the port
EXPOSE ${PORT}

# Start the application
CMD ["pnpm", "start"]