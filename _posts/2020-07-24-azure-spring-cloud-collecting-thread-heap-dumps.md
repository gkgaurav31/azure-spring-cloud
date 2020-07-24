---
layout: post
title: Azure Spring Cloud - Collecting Thread & Heap Dumps
date: 2020-07-24 22:52 +0530
---

As of now, we do not have a way to SSH into the container to take the Thread or Heap dumps manually. However, we do have a way to collect the dumps of our Java Microservices running on Azure Spring Cloud using [Actuators](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html).

The way you would do it quite simple:

- Create a Spring Boot Project with the following dependencies:

  - Spring Web
  - Config Client (If required)
  - Eureka Client
  - Actuator  

- Add the following configuration to application.properties:

```txt
management.endpoints.web.exposure.include=*
```

This will expose all the endpoints of actuators that ___MAY contain sensitive information___. So, it is recommended to keep these endpoints secured. You could also expose only the endpoints you require and which do not contain any sensitive information: [DOCUMENTATION](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html#production-ready-enabling)

- Add the ___cloud___ profile, which is required for Azure Spring Cloud:

```xml
<profiles>
<profile>
    <id>cloud</id>
    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
            <version>2.2.0</version>
        </dependency>
    </dependencies>
</profile>
</profiles>
```

- Build and deploy your application:

```cli
mvn clean package -DskipTests -Pcloud
az spring-cloud app deploy -n mymicroservice --jar-path <JAR_FILE>
```

Once the application is deployed, you can access the Actuators endpoints __/actuator/threaddump__ and __/actuator/headpdump__ to collect the Thread Dump and Heap Dump respectively.

There are other really helpful endpoints available which you can explore by going to __/actuators__ endpoint.

Now, you would also want to secure these endpoints and this can be done using __Spring Security__ dependency:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

Configure the username, password and role via application.properties:

```properties
management.endpoints.web.exposure.include=*
spring.security.user.name=admin
spring.security.user.password=HelloWorld123
spring.security.user.roles=ENDPOINT_ADMIN
```

Create a new Java Class called __ActuatorSecurity__:

```java
package com.example.demo;

import org.springframework.boot.actuate.autoconfigure.security.servlet.EndpointRequest;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@Configuration(proxyBeanMethods = false)
public class ActuatorSecurity extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.requestMatcher(EndpointRequest.toAnyEndpoint()).authorizeRequests((requests) ->
                requests.anyRequest().hasRole("ENDPOINT_ADMIN"));
        http.httpBasic();
    }

}
```

To use __Basic Authentication__, we would need to also convert our username and password to __base64__ encoding.

Go to: [https://www.base64encode.org/](https://www.base64encode.org/)

In the text box enter:

```text
admin:HelloWorld
```

Copy the encoded output and use that in the curl command below for Basic Authentication:

```bash
curl -X GET http://localhost:8080/actuator/health -H 'Authorization: Basic YWRtaW46SGVsbG9Xb3JsZDEyMw=='

{"status":"UP"}
```

__NOTE:__ If you are using Windows Terminal try using double quotes.

[SAMPLE APPLICATION ON GITHUB](https://github.com/gkgaurav31/actuator-with-spring-security)

That's it! :v:
