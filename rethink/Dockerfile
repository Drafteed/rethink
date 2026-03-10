# Build stage
FROM node:20-alpine AS builder
WORKDIR /app

COPY package.json package-lock.json ./
RUN npm ci

COPY . .
RUN npm run build && npm prune --omit=dev

# Production stage
FROM node:20-alpine AS runtime
WORKDIR /app

RUN apk add --no-cache openssl

COPY --from=builder /app/package.json /app/package-lock.json ./
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist
COPY config.json /app/data/config.json

RUN chown -R node:node /app
USER node

# NOTE: the port numbers may be modified in config.json !
EXPOSE 4433 8884 1884 46030 47878 44401
CMD ["node", "dist/rethink-cloud.js", "/app/data/config.json"]
