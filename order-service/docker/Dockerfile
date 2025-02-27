#######################################################################################################
# multistage builds
#######################################################################################################
FROM adoptopenjdk:11-jdk-hotspot as build
WORKDIR /workspace/app
COPY .mvn .mvn
COPY mvnw .
COPY pom.xml .

# trigger download of maven binaries - after that it will be cached and never downloaded again
RUN ./mvnw dependency:go-offline

COPY src src
RUN ./mvnw install -DskipTests

#######################################################################################################
# Consider best practices: https://docs.docker.com/develop/develop-images/dockerfile_best-practices/
#######################################################################################################
FROM adoptopenjdk:11-jre-hotspot
RUN mkdir /opt/app
RUN groupadd -r spring && useradd --no-log-init -r -g spring spring
USER spring:spring
COPY --from=build /workspace/app/target/*.jar /opt/app/app.jar
ENTRYPOINT ["java", "-Dkeycloak.auth-server-url=${KEYCLOAK_SERVER_AUTH_URL}", "-jar", "/opt/app/app.jar"]