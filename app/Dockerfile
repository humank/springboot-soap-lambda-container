FROM public.ecr.aws/sam/build-java17:latest as build-image
WORKDIR "/task"
COPY src src/
COPY pom.xml ./
RUN mvn -q clean package

FROM public.ecr.aws/docker/library/amazoncorretto:17-al2023
COPY --from=public.ecr.aws/awsguru/aws-lambda-adapter:0.8.1 /lambda-adapter /opt/extensions/lambda-adapter
EXPOSE 8080
WORKDIR /opt
COPY --from=build-image /task/target/producing-web-service-initial-0.0.1-SNAPSHOT.jar /opt
CMD ["java", "-jar", "producing-web-service-initial-0.0.1-SNAPSHOT.jar.jar"]