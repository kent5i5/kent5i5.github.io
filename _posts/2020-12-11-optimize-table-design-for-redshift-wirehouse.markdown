---
layout: post
title:  "Optimizing Table Design for redshift data wirehouse"
date:   2020-12-03 9:46:00 -0800
author: yinkin
categories: 
---

##### 2 possible strategy of handling data with redshift 
1. distribution style
     

* even distribution
    - round-robin over all slices to avhieve load-balancing tasks among each CPU
    - Good if a table wont't be joined which will result in lots of shuffling of data.
* all distribution A.K.A boardcasting
    - tables will be replicated on all slices to speed up joins
    - When join, there are no shuffling.
* auto distribution
    - leaving decision to redshift
* key distribution
    - rows having similar values are placed in the same slice 

2. sorting key
    - Define its columns as sort key
    - rows are sorted befoer distribution to slices
    - reduce the query time for each node
    - good for columns that are used frequently in sorting such as primary of dimension table