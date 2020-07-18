---
layout: post
title: Azure Spring Cloud - Config Server Refresh
date: 2020-07-18 14:33 +0530
---

This blog is in succession to [Azure Spring Cloud - Simple Micro Service](https://gkgaurav31.github.io/azure-spring-cloud/azure-spring-cloud-simple-micro-service)

Now that we have an App deployed in Azure Spring Cloud which uses a Config Server configured with a git repository, we will try to solve the most common problem. Currently, if we make any change to our properties in the Config Server, it would not get reflected in our Apps.  

We will use [Refresh Scope](https://cloud.spring.io/spring-cloud-static/spring-cloud.html#_refresh_scope) in our Spring Cloud project to solve this problem.

### STEPS

Add the [Actuator](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html) dependency in your Spring Boot Project:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

If you are using [Spring Tool Suite (STS)](https://spring.io/tools), you can simply right click on your project in Project Explorer and go to __Spring -> Edit Starters__. Search for Actuator and select it to add it.

Next, add the following repository in pom.xml:

```xml
<repositories>
    <repository>
        <id>nexus-snapshots</id>
        <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
        <snapshots>
            <enabled>true</enabled>
        </snapshots>
    </repository>
</repositories>
```

Add the following dependency in pom.xml:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
    <version>2.1.0-SNAPSHOT</version>
</dependency>
```

Rename application.properties file under the project's __resources__ folder to application.yml and copy the following content:

```yml
spring:
  cloud:
    config:
      auto-refresh: true
      refresh-interval: 5
```

This will poll for changes every 5 seconds.

Finally, add the __@RefreshScope__ annotation in your REST controller (HelloController.java):

```java
package com.example.demo;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.cloud.context.config.annotation.RefreshScope;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RefreshScope
public class HelloController {
	
	@Value("${NAME}")
	String name;
	
	@GetMapping("/")
	public String test() {
		return "You are doing great, " + name;
	}
	
}
```

Now, we just need to re-package our JAR and deploy it on Azure Spring Cloud:

```cli
mvn clean package -DskipTests -Pcloud
az spring-cloud app deploy -n mymicroservice --jar-path target\demo-0.0.1-SNAPSHOT.jar
```

#### TEST

Test the application if it is working as expected by making changes to the properties file in your git repository. Then verify that the application loads the latest values without manually restarting the App.

That's it! :v:
