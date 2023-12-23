# Use a base image with Maven to build the Spring Boot application
FROM openjdk:11-jre-slim
WORKDIR /app
COPY target/spring-boot-data-jpa-0.0.1-SNAPSHOT.jar /app
RUN mvn clean install -DskipTests
EXPOSE 8080
CMD ["java", "-jar", "spring-boot-data-jpa-0.0.1-SNAPSHOT.jar"]

