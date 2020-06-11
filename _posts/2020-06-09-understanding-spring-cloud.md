---
layout: post
title: Understanding Spring Cloud
date: 2020-06-09 19:18 +0530
---

## LET ME TELL YOU A STORY

### CHAPTER 1

There was a guy called Alex. He owned a small shop called Minimart. After people got into online shopping, his business took a hit and he had hardly enough money with him. He asked his son, Nick, to help him build an online website like Flipkart. Nick, who was still in college, found it to be an interesting idea, and was keep on helping his family.

He was an amateur developer, but he was able to creat a small Web Application in Java. Since they did not have enough money to host their application, they bought a single server where they planned to host their Web Application. Nick deployed the application and soon they started recieving some orders. They started doing some advertisement with the help of their neighours and friends. After a few weeks, the Web Application suddenly went down. Upon investigation, Nick found that the application did not have a good way to handle the load. He did some research, and found that there is an amazing project called "Spring Boot", which makes creating Enterprise Grade application really simple. Spring Boot (based on Spring Framework) uses the "best practices" and takes an "opinionated" way of developing Java Application. In a very short amount of time, Nick was able to create a good-enough application for his site. He packaged the application as a JAR file. Going with the best-practices, he also ensured that his application is modularized. So, he kept his business logic, separate from the view/interface of the application. The website ran well for quite some time, and the popularity of the application grew even more.  

Unfortunately, after a few months, the load was too high again, and the application crashed. The single VM was unable to handle such huge load. They had enough money to invest on more VMs. So, they bought 2 VMs as per their budget. Everytime, Nick had to make any changes to his application, he would need to deploy the JAR file on each of these machines. He got really frustrated when he missed any one of them. Nick decided to have a shared location for all his application files. He was able to achive this using Network File Share. Now, things became better as he had to replace the JAR file only in the shared location.  

Not after very long time, they were able to purchase 10 machines to host their web application. Nick, however, realize that there was still a huge problem. Most of his customers would use certain APIs like "Add to cart", "Get order status" and "View EMI details" more than any other API. The website was running on 10 machines, and it was mostly able to handle the load. However, learning from experience, Nick knew that if there are a spike in these APIs, his app could again crash. He started to work on a plan.  

There was one clear problem. If Nick wanted to just scale-out a specific API, like "Get order status", with the current design, he could not do it. He would need to get another VM and deploy his entire application. This was a huge problem for him. As usual, Nick searched for answers, and found that there is a way to creating applications which could solve his problem. He found out about Micro Servies.  

### Chapter 2

Nick spent some time to understand how his app would work with this new "Architecture". His application would need to be refractored a lot. He rolled up his sleeves and started working on his new project. He got to know that instead of packing his entire application as a single JAR file, he would need to break them into smaller versions. So, he would need a Java Application for "Add to Cart", another one for "Get Order Status" and like wise. Since his app was already modularized, Nick was able to achive this in a short period of time. So, for each module, he created a different application - a micro service. Soon enough, he realized there were some implict problems which needed answers:

- How do these micro services talk to each other?
- There must be a single point (which his customers would access), and that should be able to find the location of other services. Let's call it "Entry point" for now.
- When we scale-out (increase the number of instances) any of these micro services, how would our Entry Point come to know about it?
- I can have separate configuration for each of these micro services. There are certain configurations which are shared. That means, if I wanted to change any configuration, I would need to do it for every micro service
- When the application was running on certain number of VMs and there was an issue, I could pretty much isolate the VM which was causing that. Now, I can have 100s of micro service instance for, let's say, "Order Status". How do I track an issue now?

### CHAPTER 3

Coming soon. :)
