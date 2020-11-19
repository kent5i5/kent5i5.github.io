---
layout: post
title:  "Spring-aws-message-service-example"
date:   2020-11-19 14:05:24 -0800
author: yinkin
categories: spring-boot JAVA Restful-API
---

* Create IAM user in Amazon IAM console. 

* In SNS panel, we create SNS subscription for user to subscribe to.

* Create a amazonkey.properties file of the spring boot app, add the 
> If you have cloudformation script, you can turn cloud.aws.stack.auto to true to create the stack of resources you want.
{% highlight shell-script %}
cloud.aws.credentials.accessKey=Access_key_of_the_IAM_user
cloud.aws.credentials.secretKey=Secret_key_of_IAM_user
cloud.aws.region.static=us-west-2
cloud.aws.region.auto=true
cloud.aws.stack.auto=false
spring.autoconfigure.exclude=org.springframework.cloud.aws.autoconfigure.metrics.CloudWatchExportAutoConfiguration
{% endhighlight %}

* Create a Controller to handle the http request.
{% highlight java %}

public class SnsEndpointController {

    
    @Value( "${cloud.aws.credentials.accessKey}" )
    private String access_key_id;
    @Value( "${cloud.aws.credentials.secretKey}" )
    private String secret_key_id;

    @GetMapping("/subscribe/{email}")
    public String subscribtoSNS(@PathVariable("email") String email){

        BasicAWSCredentials awsCreds = new BasicAWSCredentials(access_key_id, secret_key_id);
        AmazonSNSClient amazonSNSClient = new AmazonSNSClient(awsCreds).withRegion(Regions.US_WEST_2);
        SubscribeRequest subscribeRequest = new SubscribeRequest(topicarn, "email", email);
        amazonSNSClient.subscribe(subscribeRequest);
        return "Check your email box";
    }
{% endhighlight %}


* For the aws services to work , we need the following Dependencies. In pom.xml file ( maven project)
{% highlight java %}
    <dependencyManagement>
		<dependencies>
		  <dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-dependencies</artifactId>
			<version>Brixton.RELEASE</version>
			<type>pom</type>
			<scope>import</scope>
		  </dependency>
		</dependencies>
	</dependencyManagement>

    <dependency>
		<groupId>org.springframework.cloud</groupId>
		<artifactId>spring-cloud-starter-aws-messaging</artifactId>
	</dependency>
	<dependency>
		<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-aws</artifactId>
	</dependency>
	<dependency>
		<groupId>org.apache.httpcomponents</groupId>
		   <artifactId>httpclient</artifactId>
			<version>4.5</version>
	</dependency>
    {% endhighlight %}

* open the broswer andRun the app. Enter localhost:8080/subscribe/youremail

* Check the email and accept the confirm the subscription.