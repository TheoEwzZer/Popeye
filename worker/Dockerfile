FROM maven:3.8.4-jdk-11-slim AS builder
WORKDIR /worker
COPY . /worker
RUN mvn dependency:resolve
RUN mvn package

FROM openjdk:11-jre-slim
WORKDIR /worker
COPY --from=builder /worker/target/worker-jar-with-dependencies.jar .
CMD ["java", "-jar", "worker-jar-with-dependencies.jar"]