FROM node:latest as build-stage
WORKDIR /app
COPY package*.json ./
RUN npm install -g @angular/cli
RUN npm install
COPY . .
RUN ng build
FROM nginx:alpine
COPY --from=build-stage /app/dist/angular-15-crud/ /usr/share/nginx/html
COPY ngnx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80


