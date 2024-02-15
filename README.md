
## Run application

```bash
mvn spring-boot:run
```

## Test
```bash
cd src/test/resources
curl -fsSL --header "content-type: text/xml" -d @request.xml http://localhost:8080/ws | xmllint --format - > output.xml; cat output.xml
```
