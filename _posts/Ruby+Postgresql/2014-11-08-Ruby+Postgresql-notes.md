---
layout: post
title: Ruby+Postgresql notes
category: Ruby
description: ruby+postgresql notes
tags: ["ruby","postgresql"]
---

To create model in rails;

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


To create new copy of file without top row

```
$ tail -n +2 filename.txt > newfilename.txt
```

Backup postgresql database

```
sudo pg_dump -F c -v -U postgres -h localhost dealglobe.engine-production -f /data/backup201411041242.psql

sudo pg_dump -F c -v -U ubuntu -h localhost engine_from_prod -f /home/ubuntu/migrate/backup201411041458.psql
```

