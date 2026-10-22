FROM node:22.23.4-slim@sha256:de170b39439eb0e5a3444ad7d60b015614a381e10fecae2e30ec9e735122f38d
WORKDIR /app
COPY package.json .
RUN npm install --omit=dev
COPY server.js .
ENV NODE_ENV=production
EXPOSE 8080
CMD ["node", "server.js"]
