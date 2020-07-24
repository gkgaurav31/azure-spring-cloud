---
layout: post
title: Azure Spring Cloud - Collecting Thread & Heap Dumps
date: 2020-07-24 22:52 +0530
---

As of now, we do not have a way to SSH into the container to take the Thread or Heap dumps manually. However, we do have a way to collect Thread & Heap Dumps of our Java Microservices running on Azure Spring Cloud using Actuators.

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

This will expose all the endpoints of actuators that MAY contain sensitive information. So, it is recommended to keep this endpoint secured. You could also expose only the endpoint you required and which does not contain any sensitive information: [DOCUMENTATION](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html#production-ready-enabling)

- Add the cloud profile, which is required for Azure Spring Cloud:

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

That's it! :v:
