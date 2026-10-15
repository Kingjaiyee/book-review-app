# Stage 1: build the production Next.js app
FROM node:22-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci --no-audit --no-fund
COPY . .

# NEXT_PUBLIC_* values are baked into the build, so fail early if missing
ARG NEXT_PUBLIC_API_URL
ENV NEXT_PUBLIC_API_URL=$NEXT_PUBLIC_API_URL
RUN test -n "$NEXT_PUBLIC_API_URL" || (echo "NEXT_PUBLIC_API_URL is required" && exit 1)
RUN npm run build

# Stage 2: runtime with production dependencies only
FROM node:22-alpine
ENV NODE_ENV=production
WORKDIR /app
COPY package*.json ./
RUN npm ci --omit=dev --no-audit --no-fund
COPY --from=build /app/public ./public
COPY --from=build /app/next.config.mjs ./
COPY --from=build --chown=node:node /app/.next ./.next

USER node
EXPOSE 3000
CMD ["node", "node_modules/next/dist/bin/next", "start", "-p", "3000"]
