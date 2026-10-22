FROM oven/bun:1.4.4@sha256:1d22548f177f4ff38e0c15baa3c2c46c6c2060ba2d1bc573bb4720b407548f1d
WORKDIR /app
COPY package.json bun.lock ./
RUN bun install --production --frozen-lockfile
COPY server.ts entrypoint.sh ./
COPY views/ views/
RUN chmod +x entrypoint.sh
EXPOSE 8080 8443
CMD ["/app/entrypoint.sh"]
