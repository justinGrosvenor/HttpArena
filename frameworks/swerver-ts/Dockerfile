FROM oven/bun:1.4.4
WORKDIR /app
# swerver-ts pulls its engine (binary + libswerver) as an optionalDependency
# prebuilt matching the container's platform (esbuild-style) — no build step.
COPY package.json ./
RUN bun install
COPY app.ts ./
ENV DATASET_PATH=/data/dataset.json STATIC_DIR=/data/static
EXPOSE 8080 8081
CMD ["bun", "/app/app.ts"]
