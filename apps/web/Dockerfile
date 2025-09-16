FROM node:22-alpine AS builder

WORKDIR /app

RUN npm install -g pnpm


COPY pnpm-lock.yaml ./
COPY package.json ./ 
COPY turbo.json ./

COPY apps/web/package.json ./apps/web/
COPY packages/ui/package.json ./packages/ui/

RUN pnpm install --frozen-lockfile

COPY . .

RUN pnpm --filter=web build

FROM node:22-alpine AS runner

WORKDIR /app

ENV NODE_ENV production
ENV PORT 3050

RUN npm install -g pnpm

COPY --from=builder /app/apps/web/.next/standalone ./
COPY --from=builder /app/apps/web/.next/static ./.next/static
COPY --from=builder /app/apps/web/public ./public
COPY --from=builder /app/apps/web/package.json ./package.json

EXPOSE 3050

CMD ["pnpm", "start"]