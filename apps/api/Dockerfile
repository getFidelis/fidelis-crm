# apps/api/Dockerfile

# Stage 1: Build the Hono application
FROM node:20-alpine AS builder

WORKDIR /app

# Copy pnpm-lock.yaml and package.json files for dependency caching
COPY pnpm-lock.yaml ./
COPY package.json ./
COPY turbo.json ./

COPY apps/api/package.json ./apps/api/

RUN npm install -g pnpm
RUN pnpm install --frozen-lockfile

COPY . .

RUN pnpm --filter=api build

FROM node:22-alpine AS runner

WORKDIR /app
# This needs careful selection. For Hono, it's typically the dist folder and prisma client.
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/apps/api/dist ./apps/api/dist
COPY --from=builder /app/apps/api/package.json ./apps/api/


# Set environment variables
ENV NODE_ENV production
ENV PORT 3051

# Expose the port
EXPOSE 3051

# Start the application
CMD ["pnpm", "--filter", "api", "start"]