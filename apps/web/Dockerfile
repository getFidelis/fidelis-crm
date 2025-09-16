# apps/web/Dockerfile

# Stage 1: Build the Next.js application
FROM node:20-alpine AS builder

WORKDIR /app

# Copy pnpm-lock.yaml and package.json files for dependency caching
COPY pnpm-lock.yaml ./
COPY package.json ./
COPY turbo.json ./

# Add more package.json files from packages as needed by the web
COPY packages/ui/package.json ./packages/ui/
COPY apps/web/package.json ./apps/web/

# Install pnpm and then install dependencies across the monorepo
RUN npm install -g pnpm
RUN pnpm install --frozen-lockfile

# Copy the entire monorepo source code
COPY . .

# Build the specific web application using Turborepo filter
RUN pnpm --filter=web build

# Stage 2: Run the Next.js application
FROM node:20-alpine AS runner

WORKDIR /app

# Copy necessary files from the build stage for Next.js standalone output
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/apps/web/.next/standalone ./
COPY --from=builder /app/apps/web/.next/static ./.next/static
COPY --from=builder /app/apps/web/public ./public
COPY --from=builder /app/apps/web/package.json ./apps/web/package.json
# Copy only the project's package.json
# Next.js standalone builds should encapsulate most dependencies

# Set environment variables
ENV NODE_ENV production
ENV PORT 3050

# Expose the port
EXPOSE 3050

# Start the application
CMD ["pnpm", "--filter", "web", "start"]