# ====================================================================
FROM alpine/git as clone

ARG GIT_URL
ARG GIT_TAG=master

WORKDIR /app
RUN git clone $GIT_URL --branch $GIT_TAG .

# ====================================================================
FROM maven:3-jdk-8-alpine as build

COPY --from=clone /app/pom.xml /app/
COPY --from=clone /app/src /app/src/
WORKDIR /app
RUN mvn -s /usr/share/maven/ref/settings-docker.xml package

# ====================================================================
FROM openjdk:8-jre-alpine

EXPOSE 8080

ARG ARTIFACT

COPY --from=build /app/target/${ARTIFACT}.jar /app/app.jar

ENTRYPOINT ["java","-jar","/app/app.jar"]
