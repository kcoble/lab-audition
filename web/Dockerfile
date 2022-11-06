FROM openjdk:8-jdk-alpine
RUN apk update && apk add --no-cache bash
COPY target/*.jar web-server.jar
ENTRYPOINT ["java","-jar","/web-server.jar"]
