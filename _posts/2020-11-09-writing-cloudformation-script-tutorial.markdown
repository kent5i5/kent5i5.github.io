---
layout: post
title:  "Writing a simple Amazon Cloudformation script"
date:   2020-11-08 12:05:24 -0800
categories: spring-boot JAVA Restful-API
---

Cloudformation script allows user to create/destory resources from Amazon cloud 
Making it easy to manage all of the resources as well as quickly generate resources 
for scaling. 

Basic: 

Each cloud formation requires at least one resources: 

For a simple networking, we need to defend the VPC, InternetGateway,
InternetGatewayAttachment, publicsubnet, NatGateway, and PublicRouteTable.

{% highlight yaml %}
Resources: 
    VPC: 
        Type: AWS::EC2::VPC
        Properties:
            CidrBlock: !Ref VpcCIDR
            EnableDnsHostnames: true
            Tags: 
                - Key: Name 
                  Value: !Ref EnvironmentName
            
    InternetGateway:
        Type: AWS::EC2::InternetGateway
        Properties:
            Tags:
                - Key: Name
                  Value: !Ref EnvironmentName
            
    InternetGatewayAttachment:
        Type: AWS::EC2::VPCGatewayAttachment
        Properties:
            InternetGatewayId: !Ref InternetGateway
            VpcId: !Ref VPC

    PublicSubnet1: 
        Type: AWS::EC2::Subnet
        Properties:
            VpcId: !Ref VPC
            AvailabilityZone: !Select [ 0, !GetAZs '' ]
            CidrBlock: !Ref PublicSubnet1CIDR
            MapPublicIpOnLaunch: true
            Tags: 
                - Key: Name 
                  Value: !Sub ${EnvironmentName} Public Subnet (AZ1)

    NatGateway1EIP:
        Type: AWS::EC2::EIP
        DependsOn: InternetGatewayAttachment
        Properties: 
            Domain: vpc

    NatGateway1: 
        Type: AWS::EC2::NatGateway
        Properties: 
            AllocationId: !GetAtt NatGateway1EIP.AllocationId
            SubnetId: !Ref PublicSubnet1


    PublicRouteTable:
        Type: AWS::EC2::RouteTable
        Properties: 
            VpcId: !Ref VPC
            Tags: 
                - Key: Name 
                  Value: !Sub ${EnvironmentName} Public Routes

    DefaultPublicRoute: 
        Type: AWS::EC2::Route
        DependsOn: InternetGatewayAttachment
        Properties: 
            RouteTableId: !Ref PublicRouteTable
            DestinationCidrBlock: 0.0.0.0/0
            GatewayId: !Ref InternetGateway

    PublicSubnet1RouteTableAssociation:
        Type: AWS::EC2::SubnetRouteTableAssociation
        Properties:
            RouteTableId: !Ref PublicRouteTable
            SubnetId: !Ref PublicSubnet1
{% endhighlight %}

references:
[cloudformation-docs]

[cloudformation-docs]:https://docs.aws.amazon.com/cloudformation/index.html