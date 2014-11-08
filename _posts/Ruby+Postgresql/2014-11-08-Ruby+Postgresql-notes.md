---
layout: post
title: Ruby+Postgresql notes
category: Ruby+Postgresql
description: ruby+postgresql notes
tags: ["ruby+postgresql"]
---

to create model in rails;

rails g scaffold ModelName field:integer field2:string field3:

useful pg commands


```
sudo su - 
su - postgres
```

(to become pg)


```
$ psql
\list
\c <databasename>
\d+ <tablename>
copy zephyr_entity_bases(zephus_id,bvd_id,"name") from '/home/alan/bvddata/entity_base_no_header.txt'
```


to create new copy of file without top row

```
$ tail -n +2 filename.txt > newfilename.txt
```
