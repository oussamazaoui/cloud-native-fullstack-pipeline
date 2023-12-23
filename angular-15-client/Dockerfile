FROM node:latest as BUILD
WORKDIR /app
COPY package*.json ./
RUN npm install -g @angular/cli
RUN npm install
COPY . .
RUN ng build

FROM nginx:alpine

# Copy the built app from the builder stage to the nginx image
COPY --from=BUILD /app/dist/angular-15-crud/ /usr/share/nginx/html

# Expose port 80 to the outside world
EXPOSE 80

# CMD instruction to start Nginx when the container starts
CMD ["nginx", "-g", "daemon off;"]
