---
layout: post
title:  "Create Jenkins pipeline : automated build and testing"
date:   2020-11-09 12:05:24 -0800
author: yinkin
categories: spring-boot JAVA Restful-API
---

* install jenkin [with docker](https://www.jenkins.io/doc/book/installing/docker/#setup-wizard)

* After installation,open configuration system. Check environement box and add both JAVA and ANDROID path to 

![Jenkin java and android path](/assets/img/jenkin_config.png)

* Click open Blueocean

* In Blueocean, we can create new pipeline by connecting github to it. 

    - The token is generated in github-setting-develop setting-Personal access token 
    Follow the links, it will select the necessary permissions: 
    repo:status, public_repo, admin:repo_hook, read:user, user:email

    >> select repo for full access for private repositories

    - Choose a repo and create a basic pipeline, the pipeline is not working since a jenkinsfile
    is required for pipeline to define stages and run.

* List of stages, jenkins pipeline
    - link codes to check syntax
    - build the app
    - testing
    - deploy: blue/green, production, etc

# Use jenkinsfile to define complete set of build, test , deploy steps.
        pipeline {
            agent any 
            stages {
                stage('Lint') {
                    steps {
                        // Check for syntax Errors 
                       
                    }   
                }
                stage('build') {
                    steps {
                        // Run whatever commands that is necessary to build the application  and build the applicatoin
                        sh 'npm install' 
                        
                    }   
                }

                stage('Push image') {
                    steps {
                        //Upload the change to repository such as docker hub
                    }
                }


                stage('set current kubectl context') {
                    steps{
                       // set up kubect resources 
                    }
                
                }
                 stage('Deploy blue container') {
                    steps{
                        // Deploy run a developing instance. 
                    }
                }

                stage('Switch green container') {
                    steps{
                        // Deploy and run production instance
                    }
                }   

            }
        }

* Jenkins run each steps and show logs beneth. If error presents, it will stop and indicate it with cross.

![Example Jenkin Pipeline](/assets/img/jenkin_pipeline.png)




