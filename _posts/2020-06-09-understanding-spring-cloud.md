---
layout: post
title: Understanding Spring Cloud
date: 2020-06-09 19:18 +0530
---

## LET ME TELL YOU A STORY

---

### [CHAPTER 1](#chapter1)

There was a guy called Alex. He owned a small shop called Minimart. After people got into online shopping, his business took a hit and he had hardly enough money left with him. He asked his son, Nick, to help him build an online website like Flipkart. Nick, who was still in college, found it to be an interesting idea, and was keep on helping his family.

He was an amateur developer, but he was able to create a small Web Application in Java. Since they did not have enough money to host their application, they bought a single mini server where they planned to host their Web Application. Nick deployed the application and soon they started recieving some orders. They started doing some advertisement with the help of their neighours and friends. After a few weeks, the Web Application suddenly went down. Upon investigation, Nick found that the application did not have a good way to handle the load. He did some research, and found that there was an amazing project called __Spring Boot__, which makes creating Enterprise Grade application really simple. Spring Boot (based on __Spring Framework__) uses the __best practices__ and takes an __opinionated__ way of developing Java Application. In a very short amount of time, Nick was able to create a good-enough application for his site. He packaged the application as a JAR file. Going with the best-practices, he also ensured that his application is modularized. So, he kept his business logic, separate from the view/interface of the application. The website ran well for quite some time, and the popularity of the application grew even more.  

Unfortunately, after a few months, the load was too high again, and the application crashed. The single VM was unable to handle such huge load. They had enough money to invest on more VMs. So, they bought 2 VMs as per their budget. Everytime, Nick had to make any changes to his application, he would need to deploy the JAR file on each of these machines. He got really frustrated when he missed any one of them. Nick decided to have a shared location for all his application files. He was able to achive this using a __Network File Share__. Now, things became better as he had to replace the JAR file only in the shared location and restart his servers.  

Not after very long time, they were able to purchase 10 more machines to host their web application. Nick, however, realize that there was still a huge problem. Most of his customers would use certain APIs like "Add to cart", "Get order status" and "View EMI details" more than any other API. The website was running on 10 machines, and it was mostly able to handle the load. However, learning from experience, Nick knew that if there are a spike in the request for these APIs, his app could again crash. He started to work on a plan.  

There was one clear problem. If Nick wanted to just scale-out a specific API, like "Get order status", with the current design, he could not do it. He would need to get another VM and deploy his entire application. This was a huge problem for him. As usual, Nick searched for answers, and found that there is a way of developing and designing applications which could solve his problem. He found out about __Micro Services__.  

---

### [CHAPTER 2](#chapter2)

Nick spent some time to understand how his app would work with this new __Architecture__. His application would need to be refractored a lot. He rolled up his sleeves and started working on his new project. He got to know that instead of packing his entire application as a single JAR file, he would need to break them into smaller versions. So, he would need a Java Application for "Add to Cart", another one for "Get Order Status" and like wise. Since his app was already modularized, Nick was able to achive this in a short period of time. So, for each module, he created a different application - a micro service. Soon enough, he realized there were some implicit problems which needed answers:

- How do these micro services talk to each other?
- There must be a single point (which his customers would access), and that should be able to find the location of other services. Let's call it "Entry point" for now.
- When we scale-out (increase the number of instances) any of these micro services, how would our Entry Point come to know about it?
- I can have separate configuration for each of these micro services. There are certain configurations which are shared. That means, if I wanted to change any configuration, I would need to do it for every micro service. That doesn't sound good.
- When the application was running on a certain number of VMs and if there was an issue, I could pretty much isolate the VM which was causing that. Now, I can have hundreds of micro service instances for, let's say, "Order Status". How do I track an issue now?

---

### [CHAPTER 3](#chapter3)

Nick realized that these were problems which already had a well established solution. There were certain open-source projects which addressed these exact problems. So, Nick just had to figure out how he can use these different solutions for his use-case. He read the official documentations about Spring Cloud Project to understand more. The Spring Cloud Project itself consisted of different sub-projects, and each of them served a specific purpose. Interestingly, Netflix, which is a well known media streaming service, had built a set of tools and projects for the same purpose. They have also been helpful enough to open-source these projects. Nick started searching the tools which would be useful for his project. To start with, he made the following draft with his initial understanding of the complete setup:

- For each API, we would ideally create a single Micro Service. If there was requirement to scale-out, we could simply run another instance of the same micro service
- These micro services would run on the same machine. They can have conflicting dependency requirements. For example, a micro-service A might need a dependency X of version 1.2.3 while some other micro-service B might need the version 4.5.6 for the same dependency. Since, it would be difficult to manage these dependencies, it would be sensible to go with Docker Containers since they provide an isolated environement for running the applications.
- If I needed to scale-out the micro-service, I could simply start another instance of the same application. Of course, each of them would be mapped to a different host port.
- Since we do not know the port or other configurations of these micro services before-hand, we would need a way to store them while the micro servies are coming up. This can be done using a tool/dependency called __Eureka__, which is a part of __Spring Cloud Netflix__. Basically, when the micro service instance is trying to come up, it would register itself on the Eureka Server as a client. We would obviously need to provide the "location" of this Eureka Server to all the micro services so that they can register themselves. Consequently, Eureaka server would know about each of these micro service endpoints. It would use the Spring Boot project name to distinguish between these micro-services.
- To make it easier, the creators of Eureka have created a Dashboad which shows all the running micro services (which would have registered to the Eureka Server, as an Eureka Client) One could just choose any one of them to check its details.
- Now we do have a place where we can get the details about each of these micro service. However, an external user would not really go through the burden of checking Eureka server's dashboard to get the endpoint. Essentially, we need a single "point of contact". The __Zuul API Gateway__, which is again provided by Netflix helped in accomplishing this. The logic is simple, the API Gateway would get the Eureka Server to find the location of the micro service being called and proceed. There were a lot of other benefits which this gateway provided, like load-balancing. It was capable of distributing the load evenly using round robin algorithm without any extra configuration. This was done by a component called Ribbon which the API Gateway was integrated with. This made life simpler.
- We would also need to centralize the configurations of these microservices, so that the changes do not have to be made manually for each of them. This was again made simple by a __Config Server__. The micro services would use this config server to get any configuration detail. It would have a single file called application.properties which would be shared by all the micro services. If a microservice needed a specific configuration, we could just create another file like: mymicroservice.properties. This can also be extended at the profile level. So, for the same microservice, we can have different profiles for different environments like: mymicroservice-dev.properties, mymicroservice-prod.properties. This was super useful. There was a small caveat to this - if there was a change in the configurations, although the config server would have the new details, the micro services would still require a restart. There must be done better way. Since this is not a huge hurdle, let me keep this aside for now.

Nick was very happy and pretty excited about his new journey. Within a couple of weeks, Nick was able to convert his mammoth project into micro services, which helped him greatly in using the resources efficiently.

---

### [CHAPTER 4](#chapter4)

Stay Tuned! :)
