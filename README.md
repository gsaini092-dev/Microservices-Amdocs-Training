
### URLs
1. Department Service      : http://localhost:9002/departments/
2. User Service            : http://localhost:9001/users/1
3. API Gateway             : http://localhost:9191
4. Eureka Service Registery: http://localhost:8761
5. Hystrix Dashboard       : http://localhost:9295/hystrix
6. Histrix Sreams          : http://localhost:9191/actuator/hystrix.stream
7. ConfigServer            : http://localhost:9296                     
8. ConfigServer GIT        : https://github.com/gsaini4/config-server
9. Zipkin Server           : http://127.0.0.1:9411/zipkin/

1------ Department-Service -------- H2 Database, Lombok, Spring Data JPA, Eureka Discovery Client
```yaml
server:
  port: 9001
spring:
  application:
    name: DEPARTMENT-SERVICE
  zipkin:
    base-url: http://127.0.0.1:9411/
```
2------ User Service ------ H2 Database, Lombok, Spring Data JPA, Eureka Discovery Client
```yaml
server:
  port: 9002
spring:
  application:
    name: USER-SERVICE 
  zipkin:
    base-url: http://127.0.0.1:9411/
eureka:
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      defaultZone: http://localhost:8761/eureka/
  instance:
    hostname: localhost
```

3----- Service Registery ---- Eureka Server
```yaml
server:
  port: 8761
eureka:
  client:
    register-with-eureka: false
    fetch-registry: false
```
4------ Cloud API Gateway ---- Eureka Discovery Client, 
```yaml
server:
  port: 9191
spring:
  application:
    name: API-GATEWAY
  cloud:
    gateway:
      routes:
        - id: USER-SERVICE
          uri: lb://USER-SERVICE
          predicates:
            - Path=/users/**
          filters:
            - name: CircuitBreaker
              args:
                name: USER-SERVICE
                fallbackuri: forward:/userServiceFallBack
        - id: DEPARTMENT-SERVICE
          uri: lb://DEPARTMENT-SERVICE
          predicates:
            - Path=/departments/**
          filters:
            - name: CircuitBreaker
              args:
                name: DEPARTMENT-SERVICE
                fallbackuri: forward:/departmentServiceFallBack
hystrix:
  command:
    fallbackcmd:
      execution:
        isolation:
          thread:
            timeoutInMilliseconds: 4000
management:
  endpoints:
    web:
      exposure:
        include: hystrix.stream
```
5------ HYSTRIX ---------
```yaml
server:
  port: 9295
spring:
  application:
    name: HYSTRIX-DASHBOARD
hystrix:
  dashboard:
    proxy-stream-allow-list: "*"
```
6---- Config Server ----
```yaml
server:
  port: 9296
spring:
  application:
    name: CONFIG-SERVER
  cloud:
    config:
      server:
        git:
          uri: https://github.com/gsaini4/config-server
          clone-on-start: true
```
7---- Zipkin Server ----

### Dependencies
```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```
