FROM node:26.12.1-trixie-slim@sha256:36793bcbb160a6a16cede1e62b93f84e26b78a972a8a4c0b879c1c12f22761ec

WORKDIR /app

# The only dependencies are the two drivers the database profiles cannot run
# without: node ships neither a Postgres nor a Redis client. Copied before the
# server so a source edit does not re-run the install.
COPY package.json .
RUN npm install --omit=dev --no-audit --no-fund

COPY server.js .

ENV NODE_ENV=production
EXPOSE 8080
CMD ["node", "server.js"]
