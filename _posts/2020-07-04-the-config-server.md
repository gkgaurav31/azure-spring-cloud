---
layout: post
title: Azure Spring Cloud - Config Server
date: 2020-07-04 00:59 +0530
---

> Spring Cloud Config provides server-side and client-side support for externalized configuration in a distributed system. With the Config Server, you have a central place to manage external properties for applications across all environments.

In a distributed environment, one of the key aspects is to find a way to ___share___ or ___centralize___ information which is used by multiple resources. Of course, we can have that information available to reach of these resources separately. However, that would make updating them and managing them when they change, quite complex. The Config Server helps in addressing this problem.

On Azure Spring Cloud, it's as simple as just mentioning the endpoint for your configurations, which can be a Git URL. There is also an option to update a YAML file.

The following example uses a Private Git Repository, and uses Authentication Type as __HTTP Basic__ (Username/Password)

![asc-config-server]({{ site.baseurl }}/assets/asc-config-server.png)

:notebook: You can check the health of the Config Server from __Diagnose & Solve Problems__ blade of the App Service.
