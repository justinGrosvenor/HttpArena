# https://elysiajs.com/patterns/deploy.html#deploy-to-production
FROM oven/bun:1.4.4@sha256:1d22548f177f4ff38e0c15baa3c2c46c6c2060ba2d1bc573bb4720b407548f1d AS build

WORKDIR /app

# Cache packages installation
COPY package.json package.json
COPY bun.lock bun.lock

RUN bun install --production

COPY server.ts .
# The fortunes template is imported as text and embedded into the binary, so it
# has to be present when bun build runs.
COPY views ./views

ENV NODE_ENV=production

RUN bun build \
	--compile \
	--minify \
	--target bun \
	--outfile server \
	server.ts

FROM gcr.io/distroless/base

WORKDIR /app

COPY --from=build /app/server server

ENV NODE_ENV=production

CMD ["./server"]

EXPOSE 3000
