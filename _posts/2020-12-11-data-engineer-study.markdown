---
layout: post
title:  "Data Enginner ETL - Copy big data from s3 to Redshift database"
date:   2020-12-03 9:46:00 -0800
author: yinkin
categories: 
---
##### First I create redshit clusters with local jupyter notebook with folloing steps

1. Create an iAM role with aws redshift database full and S3 bucket access

        iam = boto3.client('iam',aws_access_key_id=KEY,
            aws_secret_access_key=SECRET,
            region_name='us-west-2'
        )

        dwhRole = iam.create_role(
            Path='/',
            RoleName=DWH_IAM_ROLE_NAME,
            Description = "Allows Redshift clusters to call AWS services on your behalf.",
            AssumeRolePolicyDocument=json.dumps(
                {'Statement': [{'Action': 'sts:AssumeRole',
                'Effect': 'Allow',
                'Principal': {'Service': 'redshift.amazonaws.com'}}],
                'Version': '2012-10-17'})
        iam.attach_role_policy(RoleName=DWH_IAM_ROLE_NAME,
                       PolicyArn="arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess"
                      )['ResponseMetadata']['HTTPStatusCode']

2. Create an Redshift with following python function 

        iam.create_cluster(        
            #HW
            ClusterType=DWH_CLUSTER_TYPE,
            NodeType=DWH_NODE_TYPE,
            NumberOfNodes=int(DWH_NUM_NODES),

            #Identifiers & Credentials
            DBName=DWH_DB,
            ClusterIdentifier=DWH_CLUSTER_IDENTIFIER,
            MasterUsername=DWH_DB_USER,
            MasterUserPassword=DWH_DB_PASSWORD,
            
            #Roles (for s3 access)
            IamRoles=[roleArn]  
        )

##### Create a table that matches the data

* Run the following query in redshift
    CREATE TABLE IF NOT EXISTS exmaple(
        blog_id varchar(100) , 
        name varchar(25) NOT NULL, 
        title VARCHAR(25) , 
        PRIMARY KEY(blog_id));

#### connect to redshift and begin the transform of data ( Here the data is json)
            {
                blog_id: HIEHOFEJOFE
                name: yinkin
                title: reshift
            }

* Run a query to copy data from s3
        staging_songs_copy = ("""
            copy blog_table from 's3://blog_data/2017/1/2017' 
            credentials 'aws_iam_role=IAM_ROLE'
            format as JSON 'auto'
            region 'us-west-2';

> blog_table is destination which is a table in the redshift

##### Problem solving

* During the process of running copy query, it is critical to have all the data in the correct order and format. One way to debug that is looking into the stl_load_errors table in the redshift pg_catalog schema. The results of the query will indicate the problem column as well as reason the query crashes.

        select * from stl_load_errors

[COPY query document](https://docs.aws.amazon.com/redshift/latest/dg/r_COPY.html)