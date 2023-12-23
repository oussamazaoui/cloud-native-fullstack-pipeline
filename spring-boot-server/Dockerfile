# Use a base image with Maven to build the Spring Boot application
FROM maven:3.6.3-jdk-11 AS BUILD_ARTIFACT
WORKDIR /app
COPY . .
RUN mvn clean install -DskipTests
# Use a Tomcat image as the runtime image
FROM tomcat:9-jdk11
RUN rm -rf /usr/local/tomcat/webapps/*
COPY --from=BUILD_ARTIFACT /app/target/spring-boot-data-jpa-0.0.1-SNAPSHOT.war /usr/local/tomcat/webapps/ROOT.war
EXPOSE 8080
CMD ["catalina.sh", "run"]

