# ---------- builder ----------
FROM node:16-bullseye-slim AS builder
WORKDIR /app

# Install build tools (some node modules may need them)
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential python3 ca-certificates && \
    rm -rf /var/lib/apt/lists/*

# Copy package files and install
COPY package*.json ./
RUN npm ci

# Copy source
COPY . .

# Give Node more heap for large builds
ENV NODE_OPTIONS=--max_old_space_size=4096

# Disable angular progress spinner which can sometimes hang in non-TTY docker builds
# and enable verbose to make failures visible.
RUN npm run build -- --configuration=production --verbose --progress=false

# ---------- runtime ----------
FROM nginx:alpine AS runtime

# Remove default nginx index if exists
RUN rm -rf /usr/share/nginx/html/*

# Replace with your actual dist folder name if different
# Example below assumes dist/<your-project-folder>/ (Angular default)
COPY --from=builder /app/dist/ /usr/share/nginx/html/

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
