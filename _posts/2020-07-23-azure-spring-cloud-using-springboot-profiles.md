---
layout: post
title: Azure Spring Cloud - Using SpringBoot Profiles
date: 2020-07-23 09:48 +0530
---

Often we would use the same application in different Environements (Like Dev, QA, Prod) with different configurations. Profiles in Spring Boot makes it easy to achive this. We basically create multiple properties file (following certain naming convention) and configure our application to use a certain configuration. Here is an example of how you could do it on Azure Spring Cloud:  

##### Create a new Spring Boot project with the following dependencies

- Spring Web
- Eureka Client
- Config Client

##### Add the Maven profile in pom.xml:

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

__NOTE:__ This is different than our Spring Boot profile that we will be creating. This cloud profile is required for Azure Spring Cloud for injecting certain configuration to our project.

##### Create a new REST Controller with the following code

```java
package com.example.demo;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.core.env.Environment;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

	@Autowired
	private Environment environment;
	
	@Value("${application.message:default}")
	private String message;
	
	@GetMapping("/profile")
	public String[] getProfile() {
		return environment.getActiveProfiles();
	}
	
	@GetMapping("/test")
	public String getMessage() {
		return message;
	}
	
}
```

##### Configure our Config Server in Azure Spring Cloud

To this simple, we will be using a public Config Server. Add this using Azure CLI command:

```cli
az spring-cloud config-server git set -n gaukspringcloud --uri https://github.com/gkgaurav31/config
```

In your git repository, create the following files:

__application.properties:__

```txt
application.message=Hello, from default!
```

__mymicroservice.properties:__

```txt
application.message=Hello, from mymicroservice!
```

__mymicroservice-dev.properties:__

```txt
application.message=Hello, from mymicroservice-dev!
```

##### Let's build and deploy this to Azure Spring Cloud

```cli
mvn clean package -DskipTests -Pcloud
az spring-cloud app deploy -n mymicroservice --jar-path <location of the JAR file>
```

__IMPORTANT:__ The name of the microservice will be the name of the App in Azure Spring Cloud. (spring.application.name === App name in Azure Spring Cloud)
The profile will be created based on this App name.

#### Test

It's important to know the priority and how the configuration is picked up. It follows this logic:

- If we have set a profile (say dev), check for the value of application.message in mymicroservice-dev.properties
- If it's not there, check application.message in mymicroservice.properties
- If it's not there as well, check in application.properties
- Finally, set it to the defalut value (if provided) if it is not found anywhere

With this logic, since we have not set any specific profile, it will start looking from mymicroservice.properties. If we had set the profile as dev, it would start looking from mymicroservice-dev.properties.

If we try to access the /test endpoint now, we should get ___Hello, from mymicroservice!___ which is present in mymicroservice.properties as expected. If you remove this property (or the file itself, it will pick the value from application.properties.)

#### Change the profile to dev

There are different ways to do this. We could have created a __bootstrap.properties__ file in our Spring Boot project and set the profile using spring.profiles.active=dev.

There is another convenient way to do it using JVM argument.

Set the following JVM argument for our App in Azure Spring Cloud:

```cli
az spring-cloud app update -n mymicroservice --jvm-options='-Dspring.profiles.active=dev'
```

REFER: [ADDING JVM ARGUMENTS IN AZURE SPRING CLOUD](https://gkgaurav31.github.io/azure-spring-cloud/adding-jvm-arguments-azure-spring-cloud)

If you access the /test endpoint now, you should see ___Hello, from mymicroservice-dev!___

__TIP:__ In order to verify what configurations are being used by the Config Server, you could simply creata a new Spring Boot Project with Config Server dependency and point it to use the git repository.

```cli
spring.cloud.config.server.git.uri=<YOUR_GIT_REPO_URL>
```

That's it! :v:
