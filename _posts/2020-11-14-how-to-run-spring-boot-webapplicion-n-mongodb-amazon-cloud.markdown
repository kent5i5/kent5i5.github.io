---
layout: post
title:  "How to run and develop spring boot web wpplication MongoDb on Amazcon Cloud(Linux 2)"
date:   2020-11-14 13:56:00 -0800
author: yinkin
categories: spring-boot JAVA Restful-API
---
# Install Mongodb and Tomcat on Amazon Linux 2 (from tgz source package)
#### Prepare the computing machine
* Create an EC2 Instance (Amazon linux2) on amazon cloud 
![Choose the AmazonLinux 2](/assets/img/choose-instance.png)

* Hit review and launch to create the instance with default settings.<br>

* A panel pops up where create a key and store in it local file. <br>
![create key](/assets/img/create-key.png)

* Connect to the instance: hit the connect panel and copy the link
![connect](/assets/img/connect.png)



* Open command-line and navigate to the file where your mongo.pem located. <br>
``ssh -i "mongo.pem" ec2-user@{instance-ip}.us-west-2.compute.amazonaws.com``

* Install Java 8 on the cloud machine
``sudo yum install java-1.8.0``

* Open security group of the instance, open port 8080 and choose your IP only (dev purpose)
![security group](/assets/img/security-group.png/)
 

### Set up the environement on the cloud machine
* Download Mongodb using curl command 
choose amazon2 package from MongoDB Download Center
https://www.mongodb.com/try/download/community?tck=docs_server
copy the link and run 
``curl -LO https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-amazon2-4.4.1.tgz``

* Download Tomcat tar file
``curl -LO https://mirrors.koehn.com/apache/tomcat/tomcat-9/v9.0.39/bin/apache-tomcat-9.0.39.tar.gz``

* Unzip the files
``tar -zxvf mongodb-linux-x86_64-amazon2-4.4.1.tgz``
``tar -zxvf apache-tomcat-9.0.39.tar.gz``

* Change the names
``mv apache-tomcat-9.0.39.tar.gz tomcat9``
``mv mongodb-linux-x86_64-amazon2-4.4.1.tgz mongodb``



* optional: add mongodb and tomcat bin to the enviroment path by adding the absolute path of mongodb/bin and tomcat/bin to the end of /.bashrc file 
`` sudo vi ~/.bashrc``

### Run tomcat and mongo services

* Open tomcat9/bin folder, run
``./start.sh``

* Give mongo files permission
``sudo chown `whoami` /var/lib/mongo``
``sudo chown `whoami` /var/log/mongodb``

* Open mongo/bin folder, run
``./mongod --dbpath /var/lib/mongo --logpath /var/log/mongodb/mongod.log --fork``
![mongod](/assets/img/startedmongod.png/)

* Test the mongodb
``./mongo``
![mongo](/assets/img/mongoworking.png/)

* Now, we can open the tomcat configuration page with the instance public url. exampel:

<ec2-user@{instance-ip}.us-east-1.compute.amazonaws.com::8080>

 >  p.s Use port 8080 and Do not add https:// (the dev server doesn't have it yet)

### Start a spring-boot web application

* Open https://start.spring.io/ , choose maven, java8 and war( make sure to choose war for tomcat) and dependencies: Spring web. Define your name for the package
![springbootstart](/assets/img/springbootinit.png/)

* Create a simple hello world http request handler on main
{% highlight shell-script %}
@SpringBootApplication
@RestController
public class DemoApplication {

public static void main(String[] args) {
SpringApplication.run(DemoApplication.class, args);
}

@GetMapping("/hello")
public String hello(@RequestParam(value = "name", defaultValue = "World") String name) {
return String.format("Hello %s!", name);
}
}
{% endhighlight %}
* Run the applicatoin
``./mvnw spring-boot:run``

* Create war package. (Assume  maven is installed) p.s IDK have maven plugin that will have option to package the application.
``mvn clean package``

* On local machine, open the folder with server key.pem. Upload the folder with ssh
``scap ./key.pem ABSOLUTEPATH/ applicaiton.war ec2-user@ec2-instance_ip.us-east-1.compute.amazonaws.com:/home/ec2-user/

* Back in the cloud server:
* Optionaal Change your application name to shorter version since it will be in the url 
mv application.war app.war

* Move the war file into tomcat webapps file
``cp app.war tomcat/webapps

* Finally, open our application in:

<ec2.{instance ip}.us-west-2.compute.amazonaws.com:8080/app> <br> <br>

#### Add style and layout to the website  
![app working](/assets/img/appworking.png)

Cheer!

## references:
[Mongo installtion tutorial](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-amazon-tarball/)

[Tomcat website](https://tomcat.apache.org/)

[Spring boot offcial website](https://spring.io/)

[Maven offical site](https://maven.apache.org/)

