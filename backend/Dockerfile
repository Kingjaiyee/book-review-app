FROM node:22-alpine
ENV NODE_ENV=production
WORKDIR /app

# Dependencies first so this layer is cached between code changes
COPY package*.json ./
RUN npm ci --omit=dev --no-audit --no-fund

# Only the application source goes into the image
COPY src ./src

USER node
EXPOSE 3001
CMD ["node", "src/server.js"]
