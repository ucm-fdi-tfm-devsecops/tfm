# Dockerfile misconfigured by
# https://www.checkov.io/7.Scan%20Examples/Dockerfile.html

FROM node:alpine
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000 22
HEALTHCHECK CMD curl --fail http://localhost:3000 || exit 1
CMD ["node","app.js"]