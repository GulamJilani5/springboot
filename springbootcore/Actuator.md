# Spring Boot Actuator
- Production-ready features to monitor and manage your application.
- Check the application’s health, view metrics, inspect beans, or even shut down the application programmatically.

### Features
##### 1. Monitoring Application Health: 
- `/actuator/health`
- Check if application is running and its dependencies (e.g., database, message broker) are available. 
- This is useful for load balancers or orchestration tools like Kubernetes to determine if a service is healthy.
`/actuator/health`
##### 2. Performance Monitoring:
- `/actuator/metrics` 
- Track metrics like **JVM memory usage**, **CPU usage**, or the number of **HTTP requests** processed.
- `jvm.memory.used`
##### 3. Dynamic Configuration:
- `/actuator/env` - Inspect environment
- `/actuator/loggers` - change logging levels without restarting the application.

##### 4. Auditing and Compliance:
- `/actuator/auditevents` track user actions (e.g., **login attempts**) for security auditing.

### pom.xml
`<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>`

### application.properties
` 
##### Enable specific Actuator endpoints
management.endpoints.web.exposure.include=health,metrics,info,env
##### Optionally secure endpoints
management.endpoint.health.show-details=always`