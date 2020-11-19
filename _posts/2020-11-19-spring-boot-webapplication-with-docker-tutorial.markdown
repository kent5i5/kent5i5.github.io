---
layout: post
title:  "Build a simple web applicaiton with spring boot with docker"
date:   2020-11-19 10:05:24 -0800
author: yinkin
categories: spring-boot JAVA Restful-API
---
* Create a spring boot webapp from https://start.spring.io/ . spring boot allows user to create spring framework web applicaion quickly.

* On application.properties file (applicaion.yaml for gradle project) add the follow.
``spring.data.mongodb.host=mongo``

* Make a war Package 
``mvn clean package``

* Create a dockerfile
{% highlight shell-script %}
FROM openjdk:8-jdk-alpine
VOLUME /tmp
ARG WAR_FILE=target/*.war
COPY ${WAR_FILE} app.war
ENTRYPOINT ["java", "-Dspring.data.mongodb.uri=mongodb://mongo:27017/mongo","-jar","/app.war"]
{% endhighlight %}
``docker built -t imagename .``

* Check the image 
``docker images``

## docker-compose up
{% highlight shell-script %}
version: "3.1"
services:
  mongo:
    image: mongo:4.4.1
    container_name: "mongo"
    #hostname: mongo
    ports:
    - 27017:27017
    restart: always
  app:
    #build: .
    image: api
    restart: always
    hostname: localhost
    ports:
    - 8080:8080
    depends_on:
    - mongo
{% endhighlight %}

* the docker-compose script will download mongodb image from dockerhub and generate a mongodb image for us. It will also run the database image first before our app. Our app will connect to mongodb with mongo:27017. Once finish, open localhost:8080 to check out the web application. 

references:
[spring-boot-docs]

[spring-boot-docs]:https://www.tutorialspoint.com/spring_boot/spring_boot_introduction.htm