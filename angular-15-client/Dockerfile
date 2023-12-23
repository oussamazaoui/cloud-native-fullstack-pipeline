FROM node:latest
WORKDIR /app
COPY package*.json ./
RUN npm install -g @angular/cli
RUN npm install
CMD ["ng", "serve"]

# Stage 2: Expose the port used by the Angular app
EXPOSE 4200

