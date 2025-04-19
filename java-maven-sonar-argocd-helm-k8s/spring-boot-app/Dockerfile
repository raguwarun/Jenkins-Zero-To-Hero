# Use a specific OpenJDK 17 base image based on Alpine Linux
FROM alpine:3.19

# Install only the JRE for OpenJDK 17
RUN apk update && apk add --no-cache openjdk17-jre

# Simply the artifact path
ARG artifact=target/spring-boot-web.jar

WORKDIR /opt/app

COPY ${artifact} app.jar

# This should not be changed
ENTRYPOINT ["java","-jar","app.jar"]