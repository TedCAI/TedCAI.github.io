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

If we have two tables, table a,

columnname | value | date
-----------|-------|------
cc | 14 | 2014-01-01
dd | 15 | 2014-01-01
ee | 90 | 2014-01-01

table b,

name | score
-----|-------
cc | 100 
dd | 99
ee | 60

and we using the following commend

```
SELECT * FROM a INNER JOIN b ON (a.columnname = b.name);
```

We will have the result table like

columnname | value | date | score
-----------|-------|------|------
cc | 14 | 2014-01-01 | 100
dd | 15 | 2014-01-01 | 99
ee | 90 | 2014-01-01 | 60

If we want to get the result above in Ruby, the commend could be

```
A.joins(:b)
```

Also, we can run

```
A.joins(:b).group("date").count
```

In this case, we can get the result group by the column named date. Result should look like:

date | count
-----|------
2014-01-01 | 3

	/////////////
	/// DevIL ///
	/////////////
	
	Follow the same instructions as above for unzipping and getting to the right directory, then type:
	$ ./configure --enable-ILU --with-nvtt=no
	$ make
	$ sudo make install
	
If, while running VTFnix after compilation, you get the error:
	./VTFnix: error while loading shared libraries: libIL.so.1: cannot open shared object file: No such file or directory
	
First, make sure /usr/local/lib is listed in the file /etc/ld.so.conf (or one of it's includes), then run sudo /sbin/ldconfig

## Mount disk

For aggregation01, should run

```
sudo mount /dev/xvdb /var
```

to mount the large disk

## Usage of phaseapp

-- FROM  phraseapp

1. Login on phraseapp https://phraseapp.com/en/account/login

2. Modify data

3. Run command for update localization on staging (from project path) RAILS_ENV=production bundle exec rake phraseapp:pull

-- FROM files

1. Modify data

2. Run command for push changes on phraseapp: RAILS_ENV=production bundle exec rake phraseapp:push

3. Run command for cleck cache: RAILS_ENV=production bundle exec rake tmp:clear

## Sending message manually based on console

Use DealAccessConnectionMessage.created_message!(:sender, :receiver, :body)

