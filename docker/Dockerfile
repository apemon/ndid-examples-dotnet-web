FROM node:8-alpine as build
WORKDIR /app/tmp
COPY . .
RUN yarn install
RUN yarn run build
FROM node:8-alpine

RUN yarn add global serve
WORKDIR /app
COPY --from=build /app/tmp/dist /app
EXPOSE 5000
CMD ["yarn", "run", "serve"]