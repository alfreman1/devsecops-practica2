FROM node:20-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --omit=dev
RUN npm prune --omit=dev && rm -f package-lock.json

COPY . .

RUN chown -R node:node /app
USER node

EXPOSE 3000
CMD ["node", "src/server.js"]
