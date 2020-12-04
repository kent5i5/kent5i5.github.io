---
layout: post
title:  "Create Postgre database with Jupyter notebook remotely"
date:   2020-12-03 9:46:00 -0800
author: yinkin
categories: spring-boot JAVA Restful-API
---

# Install Jupyter notebook 

* Since anaconda provides many data science tools within its package. It is nice to install 
python and jupyter with anaconda. 
[install anaconda datascience tools](https://docs.anaconda.com/anaconda/install/linux/)

* [install Jupyter without conda](https://jupyter.org/install)


# Install Postgre database
[Postgre office guide](https://www.postgresql.org/download/linux/ubuntu/)

# Make sure pip is updated and python3 is intalled. Install Psycopg2 for jupyter notebook to connect to 
our database server
``pip install Psycopg2 ``

# After installation, we can open the databse 
``psql -d databsename``

# Inside our psql database 
`` databasename=# CREATE USER postgre WITH PASSWORD 'postgre' CREATEDB; ``

# To login into our jupyter notebook remotely

* First, we start our jupyter server on remote server with root access 

``jupyter notebook --allow-root --no-browser --port=8100``

* Redirect our local machine port to the remote machine, and connect the jupyter note server

``ssh -L 8100:localhost:8100 name@server_ip``

* Open a browser, and fire up jupyter notebook

``localhost:8100``

# Create a sample table with our postgre database using jupyter notebook

{% highlight shell-script %}

try: 
    conn = psycopg2.connect(host="localhost", port=5432, dbname="postgre", user="postgre", password="postgre")
    cur = conn.cursor()

    cur.execute("CREATE TABLE IF NOT EXISTS store_items (transaction_id int, \
                                                         customer text,  \
                                                         year int);")

    cur.execute("INSERT INTO store_items (transaction_id, customer, year) \
                 VALUES (%s, %s, %s)", \
                 (3, "Max",  2018))

    cur.execute("SELECT * FROM store_items;")
except psycopg2.Error as e: 
    print("Error: Could not make connection to the Postgres database")
    print(e)

conn.set_session(autocommit=True)

cur.execute("DROP table store_items")

cur.close()
conn.close()

{% endhighlight %}


