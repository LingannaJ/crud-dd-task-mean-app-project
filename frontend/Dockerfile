# Stage 1: Build with Node 18 (debian/bullseye)
FROM node:18-bullseye AS build
WORKDIR /app
ENV NODE_OPTIONS="--max_old_space_size=4096"

# copy package files first (cache)
COPY package*.json ./

# try reproducible install; if it fails fallback to npm install with legacy-peer-deps
RUN set -ex \
 && npm ci || npm install --legacy-peer-deps

# copy source and build
COPY . .

# build production
RUN npm run build -- --output-path=dist/angular-15-crud --configuration=production

# Stage 2: nginx to serve
FROM nginx:stable-bullseye
RUN rm -rf /usr/share/nginx/html/*
COPY --from=build /app/dist/angular-15-crud/ /usr/share/nginx/html/
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
