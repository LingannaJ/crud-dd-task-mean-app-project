# Use Node 16 (Angular 15 compatible)
FROM node:16-bullseye-slim AS builder
WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential python3 ca-certificates && \
    rm -rf /var/lib/apt/lists/*

COPY package*.json ./
RUN npm ci

COPY . .

ENV NODE_OPTIONS=--max_old_space_size=4096

RUN npm run build -- --configuration=production

# Serve using NGINX
FROM nginx:alpine

COPY --from=builder /app/dist/angular-15-crud/ /usr/share/nginx/html/

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
