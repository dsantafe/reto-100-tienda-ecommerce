FROM node:20.9.0-alpine3.18 AS build
WORKDIR /app
COPY . /app
RUN npm ci 
RUN npm run build

FROM node:20.9.0-alpine3.18
WORKDIR /app
COPY --from=build /app/dist ./dist
COPY --from=build /app/node_modules ./node_modules
EXPOSE 3001
CMD ["node", "dist/server.js"]
