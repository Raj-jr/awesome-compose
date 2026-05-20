# Standarised docker file 

# Following is a multi-stage build

#1. Base build across all builds
# Instead of doing --> stable alpine will break as not in sync with other builds 
# Use proper version.
FROM --platform=$BUILDPLATFORM node:20-alpine AS base

WORKDIR /app

COPY package*.json .
RUN npm ci

COPY . .

#2. Devlopment build
FROM base AS developement

ENV CI=true
ENV PORT=3000

CMD ["npm", "start"]

#3. Building app
FROM base AS builder

RUN npm run build

#4. Sanity testing previous build
FROM node:20-alpine AS sanity-check

WORKDIR /app

RUN npm install -g serve
COPY --from=builder /app/build ./build

EXPOSE 3000
CMD ["serve", "-s", "build", "-l", "3000"]

FROM nginx:1.31-alpine AS deployer

RUN rm -rf /usr/share/nginx/html/*

COPY --from=builder /app/build /usr/share/nginx/html
EXPOSE 80

# ONLY REQUIRED WHILE DOING MULTI STAGE BUILD.
CMD ["nginx", "-g", "daemon off"]

