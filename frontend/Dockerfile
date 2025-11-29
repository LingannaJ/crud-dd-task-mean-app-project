# ---------- Stage 1: build with Node 18 ----------
FROM node:18-bullseye AS build

WORKDIR /app

# Avoid interactive prompts and set Node memory for large builds
ENV NODE_OPTIONS="--max_old_space_size=4096"

# Copy package files first for docker layer caching
COPY package*.json ./
# Use npm ci to install devDependencies required for build
RUN npm ci

# Copy full source
COPY . .

# Build: ensure output path matches angular.json
# using --configuration=production for optimized build
RUN npm run build -- --output-path=dist/angular-15-crud --configuration=production

# ---------- Stage 2: serve with nginx ----------
FROM nginx:stable-bullseye

# Remove default nginx content
RUN rm -rf /usr/share/nginx/html/*

# Copy built Angular files from the build stage
COPY --from=build /app/dist/angular-15-crud/ /usr/share/nginx/html/

# Optional: copy a custom nginx config if you want rewrite fallback for SPA
# COPY nginx/default.conf /etc/nginx/conf.d/default.conf

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
