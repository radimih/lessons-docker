# ====================================================================
FROM alpine/git as clone
WORKDIR /app
RUN git clone https://github.com/radimih/java-hello-world.git .

# ====================================================================
FROM maven:3-jdk-8-alpine as build

COPY --from=clone /app/pom.xml /app/
COPY --from=clone /app/src /app/src/
WORKDIR /app
RUN mvn -s /usr/share/maven/ref/settings-docker.xml package

# ====================================================================
FROM openjdk:8-jre-alpine

EXPOSE 8080

COPY --from=build /app/target/hello-world.jar /app/app.jar

ENTRYPOINT ["java","-jar","/app/app.jar"]
