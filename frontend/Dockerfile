# ---------- Stage 1: Builder ----------
FROM node:20-slim AS builder
WORKDIR /app

# copy package files first (cache layer for deps)
COPY package.json package-lock.json* ./

# Use npm ci if lockfile exists else npm install
RUN if [ -f package-lock.json ]; then \
      npm ci --silent --no-audit --no-fund; \
    else \
      npm install --silent --no-audit --no-fund; \
    fi

# copy source and build
COPY . .

# Build angular app (production build)
# Angular CLI by default writes to dist/<project-name>.
RUN npm run build -- --configuration production

# ---------- Stage 2: Runtime (nginx) ----------
FROM nginx:alpine AS runtime
# remove default nginx content
RUN rm -rf /usr/share/nginx/html/*

# copy built files from builder
# The Angular build output usually ends up at dist/<project-name>.
# Copy everything under dist into nginx html folder.
COPY --from=builder /app/dist/* /usr/share/nginx/html/

# optional: copy a custom nginx conf (uncomment if you have one)
# COPY nginx/default.conf /etc/nginx/conf.d/default.conf

# run as non-root is not needed for nginx:alpine default, but keep ports explicit
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
