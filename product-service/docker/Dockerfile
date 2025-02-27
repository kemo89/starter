#######################################################################################################
# multistage builds
#######################################################################################################
FROM adoptopenjdk:11-jdk-hotspot as build
WORKDIR /workspace/app
COPY gradle gradle
COPY gradlew .
COPY build.gradle .
COPY settings.gradle .

# trigger download of gradle binaries - after that it will be cached and never downloaded again
RUN ./gradlew --version

COPY src src
RUN ./gradlew assemble --no-daemon --stacktrace

#######################################################################################################
# Consider best practices: https://docs.docker.com/develop/develop-images/dockerfile_best-practices/
#######################################################################################################
FROM adoptopenjdk:11-jre-hotspot
RUN mkdir /opt/app
RUN groupadd -r spring && useradd --no-log-init -r -g spring spring
USER spring:spring
COPY --from=build /workspace/app/build/libs/*.jar /opt/app/app.jar
ENTRYPOINT ["java", "-Dkeycloak.auth-server-url=${KEYCLOAK_SERVER_AUTH_URL}", "-jar", "/opt/app/app.jar"]