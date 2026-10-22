FROM node:26.12.1-trixie-slim@sha256:36793bcbb160a6a16cede1e62b93f84e26b78a972a8a4c0b879c1c12f22761ec AS build
WORKDIR /app
COPY package.json .
RUN npm install --omit=dev

FROM node:26.12.1-trixie-slim@sha256:36793bcbb160a6a16cede1e62b93f84e26b78a972a8a4c0b879c1c12f22761ec
RUN apt-get update && \
    apt-get install -y --no-install-recommends ca-certificates curl && \
    rm -rf /var/lib/apt/lists/*
WORKDIR /app
COPY --from=build /app/node_modules ./node_modules
# package.json rides along for "type": "module"; node 26 strips the types off
# server.ts on load, so there is no build step
COPY package.json server.ts ./
ENV NODE_ENV=production
EXPOSE 8080
CMD ["node", "server.ts"]
