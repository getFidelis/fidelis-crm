FROM node:22-alpine AS builder

WORKDIR /app

RUN npm install -g pnpm

COPY pnpm-lock.yaml ./
COPY package.json ./
COPY turbo.json ./

COPY apps/api/package.json ./apps/api/
COPY apps/web/package.json ./apps/web/ 
COPY packages/ui/package.json ./packages/ui/


RUN pnpm install --frozen-lockfile

COPY . .

RUN pnpm --filter=api build

FROM node:22-alpine AS runner

WORKDIR /app

ENV NODE_ENV production
ENV PORT 3051

RUN npm install -g pnpm

COPY --from=builder /app/apps/api/dist ./dist
RUN pnpm install --prod

EXPOSE 3051

CMD ["pnpm", "start"]