---
layout: post
title: Client Side Decryption - Azure Spring Cloud
date: 2021-07-01 00:00 +0530
---

To understand how encryption/decryption of properties work with Spring Cloud, I would suggest reading my previous blog post
[HERE](https://gkgaurav31.github.io/azure-spring-cloud/encryption-decryption-of-config-server-properties).

You can use client side decryption of properties on Azure Spring Cloud.

### STEPS

- Add the encrypted property in your __.properties__ file in your git repository.  
It would look like:

```text
message={cipher}f43e3df3862ab196a4b367624a7d9b581e1c543610da353fbdd2477d60fb282f
```

- Update the Config Server on Azure Spring Cloud to use the git repository which has our encrypted propery. You could do it directly from Azure Portal or use [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/spring-cloud/config-server/git?view=azure-cli-latest#az_spring_cloud_config_server_git_set):

```cli
az spring-cloud config-server git set -n myspringcloud --uri <git_repo_url>
```

- In your Spring Boot application, add the decryption key in the __bootstrap.yml__ file. (You would need to create it if it does not exist)

```yml
encrypt:
  key: somerandomkey
```

[This is the key you would have used in order to encrypt your property earlier]

- Now, add some code to get the value of the encrypted property.

```java
package com.example.demo;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class TestController {

    @Value("${message:default}") 
    String message;

    @GetMapping("/")
    public String hello() {
        return message;
    }

}
```

- Create an App, or deploy to an existing one.

```cli
az spring-cloud app create -n <appName>
```

- Deploy your app.

```cli
az spring-cloud app deploy -n appName --jar-path <location_of_jar>
```

- Assign a public endpoint (or use the Test Endpoints)

```cli
az spring-cloud app update -n appName --assign-endpoint true
```

- Access your application to view the value.

```cURL
https://<myspringcloud>-<appName>.azuremicroservices.io/
```

- (Optional) You can check the encrypted values on the Config Server by following the steps mentioned in this documentation:
[https://docs.microsoft.com/en-us/azure/spring-cloud/how-to-access-data-plane-azure-ad-rbac](https://docs.microsoft.com/en-us/azure/spring-cloud/how-to-access-data-plane-azure-ad-rbac)

That's it! :v:
