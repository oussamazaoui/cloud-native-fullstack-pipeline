FROM node:18 as build-stage
WORKDIR /app
COPY package*.json ./
RUN npm install
RUN npm run build
FROM nginx:latest
COPY --from=build-stage /app/dist/front/ /usr/share/nginx/html
COPY ngnx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD nginx -g "daemon off;"



