---
layout: post
title: Adding JVM arguments - Azure Spring Cloud
date: 2020-06-24 01:59 +0530
---

There are a couple of ways to set the JVM arguments to Apps running on Azure Spring Cloud.

### Using CLI

You can use the following command to find the different configurations which can be updated for the Apps:

```cli
az spring-cloud app update --help
```

As of now, below are the arguments we can use:

```list
Arguments
    --name -n        [Required] : Name of app.
    --deployment -d             : Name of an existing deployment of the app. Default to the
                                  production deployment if not specified.
    --enable-persistent-storage : If true, mount a 50G disk with default path.  Allowed values:
                                  false, true.
    --env                       : Space-separated environment variables in 'key[=value]' format.
    --is-public                 : If true, assign public domain.  Allowed values: false, true.
    --jvm-options               : A string containing jvm options, use '=' instead of ' ' for this
                                  argument to avoid bash parse error, eg: --jvm-options='-Xms1024m
                                  -Xmx2048m'.
    --resource-group -g         : Name of resource group. You can configure the default group using
                                  `az configure --defaults group=<name>`.  Config: SpringCloud.
    --runtime-version           : Runtime version of used language.  Allowed values: Java_11,
                                  Java_8.
    --service -s                : Name of Azure Spring Cloud, you can configure the default service
                                  using az configure --defaults spring-cloud=<name>.  Config:
                                  gaukspringcloud.
```

### Via Azure Portal

Just go to the App in Azure Spring Cloud, and configure JVM arguments via Configurations blade -> General Settings:  

![asc-jvm-args]({{ site.baseurl }}/assets/asc-jvm-args.png)

### Via Command Line

```bash
az spring-cloud app update -n coupon-service --jvm-options='-Xms256m -Xmx512m'
```

:exclamation: This assumes that you have already set the default __Resouce Group__ and __Spring Cloud__ resource, which can be done using:

```cli
az configure --defaults group=$AZ_RESOURCE_GROUP
az configure --defaults spring-cloud=$AZ_SPRING_CLOUD_NAME
```

:exclamation: Use double-quotes when using command-line on Windows.