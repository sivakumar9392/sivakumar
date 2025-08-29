<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>bfh-java-task</artifactId>
    <version>1.0.0</version>
    <packaging>jar</packaging>

    <name>Bajaj Health Java Task</name>
    <description>Spring Boot solution for BFH Qualifier 1</description>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.2.2</version>
    </parent>

    <dependencies>
        <!-- Web Client -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- JSON Parsing -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
        </dependency>

        <!-- Lombok (optional, for boilerplate reduction) -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
package com.example.bfhjavaboot;
import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.http.*;
import org.springframework.web.client.RestTemplate;
import java.util.HashMap;
import java.util.Map;
@SpringBootApplication
public class BfhJavaApplication implements CommandLineRunner {
    private final RestTemplate restTemplate = new RestTemplate();
    private final ObjectMapper mapper = new ObjectMapper();
    public static void main(String[] args) {
        SpringApplication.run(BfhJavaApplication.class, args);
    }
    @Override
    public void run(String... args) throws Exception {
        String generateUrl = "https://bfhldevapigw.healthrx.co.in/hiring/generateWebhook/JAVA";
        Map<String, Object> requestBody = new HashMap<>();
        requestBody.put("name", "John Doe");
        requestBody.put("regNo", "REG12347");
        requestBody.put("email", "john@example.com"); 
        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(MediaType.APPLICATION_JSON);
        HttpEntity<Map<String, Object>> entity = new HttpEntity<>(requestBody, headers);
        ResponseEntity<String> response = restTemplate.postForEntity(generateUrl, entity, String.class);
        System.out.println("Webhook Response: " + response.getBody());
        JsonNode root = mapper.readTree(response.getBody());
        String webhookUrl = root.get("webhook").asText();
        String accessToken = root.get("accessToken").asText();
        String finalQuery = "SELECT department, COUNT(*) FROM employees GROUP BY department;";
        headers = new HttpHeaders();
        headers.setContentType(MediaType.APPLICATION_JSON);
        headers.setBearerAuth(accessToken);
        Map<String, String> finalBody = new HashMap<>();
        finalBody.put("finalQuery", finalQuery);
        HttpEntity<Map<String, String>> finalEntity = new HttpEntity<>(finalBody, headers);
        ResponseEntity<String> finalResponse = restTemplate.postForEntity(webhookUrl, finalEntity, String.class);
        System.out.println("Final Submission Response: " + finalResponse.getBody());
    }
}
