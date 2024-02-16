

## Architecture Overview

To be updated.
By the way, it should be constructed by API gateway which enabled proxy integration to Lambda Function.
The Lambda function will be running as a service which embedded the Java Spring-boot-based SOAP Web Service. 
In terms of the lambda function traffic handling, it leverages the AWS Lambda Web Adapter to accept the incoming requests which proxied by API Gateway, then invoke the lambda function.

Package the whole architecture and deploy via AWS SAM CLI to onboard the service in production environment.

## Run application
Run the application locally.
```shell
mvn spring-boot:run
```

## Test

While the SOAP Web Services is running, you may raise a client request to test the API locally.

```shell
cd src/test/resources
curl -fsSL --header "content-type: text/xml" -d @request.xml http://localhost:8080/ws | xmllint --format - > output.xml; cat output.xml
```
## Containerize

It is convenient to containerize the application through docker & dockerfile.
In this sample, we use the multiple-stage build process to package the docker image.

```dockerfile
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
CMD ["java", "-jar", "producing-web-service-initial-0.0.1-SNAPSHOT.jar"]

```
The aws-lambda-adapter is the proxy layer of the container
which is responsible to handle the request/response to the soap application container runtime.
For more introduction, you may refer to (https://github.com/awslabs/aws-lambda-web-adapter)

Build the docker image
```shell

docker build -t springboot-soap:latest .
```
Make sure that you can find out the built image via 
```shell
docker images
```

Run the docker image locally

```shell
docker run -d -p 8080:8080 --name soap-demo springboot-soap

```
Stop the docker image
```shell
docker stop soap-demo
```
Then, test the docker runtime locally.

```shell
cd src/test/resources
curl -fsSL --header "content-type: text/xml" -d @request.xml http://localhost:8080/ws | xmllint --format - > output.xml; cat output.xml
```
## Build & Deploy via AWS SAM CLI

The AWS Serverless Application Model (AWS SAM) is a toolkit that improves the developer experience of building and running serverless applications on AWS. AWS SAM consists of two primary parts:

AWS SAM template specification – An open-source framework that you can use to define your serverless application infrastructure on AWS.

AWS SAM command line interface (AWS SAM CLI) – A command line tool that you can use with AWS SAM templates and supported third-party integrations to build and run your serverless applications.

Install the SAM CLI before proceed further instruction. Switch to the root folder of the sample.

```shell
# The path should looks like ~/springboot-soap-lambda-container
sam build

```

Deploy to AWS 

```shell
sam deploy --guided
```
If there is no preference on deployment configuration, leave it all with default would be good to go.

## Reference

[1] appreciate learning from Spring.io which offered the classic Spring boot based SOAP Web service example.
https://spring.io/guides/gs/producing-web-service

[2] AWS Lambda Web Adapter - Springboot example
https://github.com/awslabs/aws-lambda-web-adapter/tree/main/examples/springboot

