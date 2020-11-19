---
layout: post
title:  "Deploy Spring-boot web applicaion through Amazon cloud Elastic Beanstalk"
date:   2020-11-17 22:18:00 -0800
author: yinkin
categories: spring-boot JAVA Restful-API
---

### Simplify way of deployging spring boot applicaiton to Amazon Cloud

* Elastic Beanstalk automatically generate resources including 1 load balancer, 1 security group , public subnets, private subnets, Nat Gateway and EC2 instance for us.

* It is also set to autoscaling. For example, we currently have 1. If we stop this instance, it will regenerate another one to replace it. If we have two Ec2 instance, it will ensure a highly available website.

![Steop1](/assets/img/elasticbeanstalk/create_env1.png/)

![Steop2](/assets/img/elasticbeanstalk/create_env2.png/)

![Steop3](/assets/img/elasticbeanstalk/create_env3.png/)
![Steop3](/assets/img/elasticbeanstalk/create_env4.png/)

### Other options: 
* if we have a dockerfile, the docker managed platform will create docker image and deploy the application with it. 

* Java Server managed platform will take jar files generated from our application and run it using Corretto 11 with Tomcat 8.5. 