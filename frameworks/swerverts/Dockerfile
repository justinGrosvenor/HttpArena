FROM oven/bun:1.4.4@sha256:1d22548f177f4ff38e0c15baa3c2c46c6c2060ba2d1bc573bb4720b407548f1d
WORKDIR /app
# swerverts pulls its engine (binary + libswerver) as an optionalDependency
# prebuilt matching the container's platform (esbuild-style) — no build step.
COPY package.json ./
RUN bun install
COPY app.ts ./
ENV DATASET_PATH=/data/dataset.json STATIC_DIR=/data/static
EXPOSE 8080 8081
CMD ["bun", "/app/app.ts"]
