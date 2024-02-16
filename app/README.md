


## Run application

```bash
mvn spring-boot:run
```

## Test
```bash
cd src/test/resources
curl -fsSL --header "content-type: text/xml" -d @request.xml http://localhost:8080/ws | xmllint --format - > output.xml; cat output.xml
```
## Containerize

## Embedded as Serverless Container using AWS Lambda Web Adapter

## Build IaC via AWS SAM CLI

## Reference

Appreciate to learn from Spring.io which offered the classic Spring boot based SOAP Web service example.
https://spring.io/guides/gs/producing-web-service#initial
