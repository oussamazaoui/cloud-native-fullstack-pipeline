# Build stage
FROM maven:3.8.2-openjdk-11 AS build
WORKDIR /app
COPY . .
RUN mvn clean install -DskipTests

# Runtime stage
FROM openjdk:11-jre-slim
WORKDIR /app
COPY --from=build /app/target/spring-boot-data-jpa-0.0.1-SNAPSHOT.jar .
EXPOSE 8080
CMD ["java", "-jar", "spring-boot-data-jpa-0.0.1-SNAPSHOT.jar"]

