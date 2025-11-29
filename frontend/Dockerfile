# Use the Debian-based slim image
FROM node:18-bullseye-slim AS builder
WORKDIR /app

# Install any build tools if needed (optional, but can help)
RUN apt-get update && apt-get install -y --no-install-recommends build-essential python3 ca-certificates && rm -rf /var/lib/apt/lists/*

# Copy package files and install dependencies
COPY package*.json ./
RUN npm ci

# Copy the rest of the application code
COPY . .

# Set Node memory options if needed for large builds
ENV NODE_OPTIONS=--max_old_space_size=4096

# Build the Angular app (or whatever frontend framework you're using)
RUN npm run build -- --configuration=production

# Use NGINX to serve the built app
FROM nginx:alpine

# Copy the built files from the builder stage
COPY --from=builder /app/dist/angular-15-crud/ /usr/share/nginx/html/

# Expose port 80 and start NGINX
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
