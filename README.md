# Recommendation-system-based-on-graph-database-Neo4j

# Introduction of Graph database
•	These networks contain valuable information for many network applications:
–	Recommendation systems – collaborative filtering
–	Classification – classify the nodes
–	Key players identification – find important nodes
–	Community detection – find interesting subgraphs
–	Web search – enhance web search results
–	Trust and reputation – find experts


# Program Background
&emsp;[GoodReads](https://www.goodreads.com/) is a social cataloging website that allows individuals to freely search its database of books, annotations, and reviews. 
&emsp;Users can sign up to look up or rate the books, and make comments or interact with other users. The website pages of books contain author, language, average rating and other descriptive information.
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200201213341881.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDM3MjcxOQ==,size_16,color_FFFFFF,t_70# =x270)![在这里插入图片描述](https://img-blog.csdnimg.cn/20200201110802572.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDM3MjcxOQ==,size_16,color_FFFFFF,t_70# =x290)
&emsp;Our aim in this project is to **enhance the user adhesiveness**, so that the time per user spend on looking through the GoodReads website can be longer. This can directly or indirectly increase our revenue like advertisements fee due to a higher possibility of clicking advertisements. 
&emsp;What's more, by increasing the number of users' friends
## Data description
For this project, our data are collected from
Based on the data as follow:
**user table**
user_id|age|gender|location|friends_count|reviews_count
---|------|------|-----|----|---
**author table**
author_id|name
---|---
**book table**
|book_id|author|language|title|avg_rating|
---|------|------|----|---
**book-author table**
book_id|author_id
---|---
**rating table**
user_id|book_id| rating
---|------|---
**to read table**
user_id|book_id
---|---
We decide to do two kinds of recommendation: one is for *friends recommendation*, based on the same interest on author; one is for *book recommendation*, based on the similarity of books.
## friends recommendation
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191117114127554.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDM3MjcxOQ==,size_16,color_FFFFFF,t_70 =280x100)
The average rating from a user to an author are based on the records of books, because we have the data of the books that users have read or want to read.The formmer one is an explicit rating while the latter one is an implicit rating. And we can also put a weight to take these two rating mathod into consideration.
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191117152135189.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDM3MjcxOQ==,size_16,color_FFFFFF,t_70 =500x)
## books recommendation
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191117152440244.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDM3MjcxOQ==,size_16,color_FFFFFF,t_70 =500x)
The situation is nearlly the same with books recomendation.

# install Neo4j and open
Download from [here](https://neo4j.com/download-thanks/?edition=community&release=3.5.12&flavour=unix&_ga=2.194367781.987348809.1573811772-1854473052.1573811772) and select community for linux.


```powershell
# Open the terminal and set the path to bin folder
cd /home/cloudera/Downloads/neo4j-community-3.5.12/bin

# open the Neo4j
./neo4j start
# and the feedback is 
# Started neo4j (pid 27899). 
# It is available at http://localhost:7474/
# So open the website and use
```
# Ingestion example
## database reset
```powershell
# in case the previous data will affect data ingestion, delete the database
# because the ingestion method we will use can only input data to a vacant database
rm -rf graph.db
# restart
./neo4j console

# or you can just go to the folder /neo4j-community-3.5.12/data/database/graph.db
# and delete all the files in it manually
```
## data ingestion format
The data we should import contains two [format](https://blog.csdn.net/wzwdcld/article/details/81232489)s, one is the *node file*, another is the *relationship file*. And the csv [header format](https://neo4j.com/docs/operations-manual/current/tools/import/file-header-format/?_ga=2.81231056.675431736.1573811552-1883041481.1573811552) should strictly follow the offical settings.

1.**user detail** (node file)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191117161707387.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDM3MjcxOQ==,size_16,color_FFFFFF,t_70 =200x180)
It is necessary to write "**:ID**" for Neo4j to recognize which one is the primary key in this table, we should do this in csv file manually before data ingestion. The alias in bracket are used of distinction from two different IDs who share the same ingredient(like Book_id = 1 and User_id = 1). I f there exist no same values, you can only use:
User_id:ID|rating|:LABEL
---|---|---|
A label should be attatched to the node, and other attributes' type can be ignored (like "rating").

2.**book detail** (node file)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191117163233898.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDM3MjcxOQ==,size_16,color_FFFFFF,t_70 =280x180)
3.**user-book relationship** (relationship file)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191117163432271.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDM3MjcxOQ==,size_16,color_FFFFFF,t_70 =280x180)
Because that the ID in users file and books file are both start from 0, so there may exist many same IDs, leading to a confusion to the machine wether a "1" means a book or a user. As a result, the "[same id issue](https://zhuanlan.zhihu.com/p/83963911)" can be solved by using another name, like (Book_ID) in the book detail table. So apart from the name in node file is changed, the name in relationship file should be changed into consistency as well (like what shown in user-book relationship file).

## realizing function 
### LOAD CSV
The most convient way for data ingestion is  the "LOAD CSV" function, for using this you need not to stop the Neo4j, and do ingestion offline like other tools.
```powershell
# load the node file: book as node, lables are the properties
LOAD CSV WITH HEADERS  FROM "file:///test2.csv" AS row
MERGE (p:book{id:row.book_id,authors:row.authors,average_rating:row.average_rating})
# the format is: 
# name: category{attribute name in this category:attribute data}
# test whether the inport is successful
# show all the nodes in database
start n = node(*)
return n
```
As well as the [relationship](https://blog.csdn.net/weixin_33690963/article/details/93939021) ingestion.

### neo4j-admin import
However, the "LOAD CSV" function can only deal with the data within the size of 20M, we tend to choose to use "neo4j-import" or "[neo4j-admin import](https://neo4j.com/developer/guide-import-csv/#_super_fast_batch_importer_for_huge_datasets) " to [inport](https://blog.csdn.net/xingxiupaioxue/article/details/71747284) csv. Among all the five ways to do data ingestion, **"neo4j-admin import"** is the fastest.

```powershell
# set the path to bin to execute function neo4j-admin import 
# the formal ingestion path are set to /neo4j-community-3.5.12/bin/import
# if you don't put your data in this forder
# you should write the absolute path
/home/cloudera/Downloads/neo4j-community-3.5.12/bin/neo4j-admin import 
--nodes=import/user_detail.csv 
--nodes=import/book_detail.csv 
--relationships=import/user_book.csv 
--ignore-missing-nodes
```

## Bugs during ingestion and their solution
```powershell
# bug 1
'--nodes' to have at least 1 valid item, but had 0 []
# this always means a path issue
# remove your data to the path of bin/import

# bug 2
missing header of type START_ID, among entries [START_ID:string, END_ID:string, rating:string]
# this is the original import data format issue
# should strictly follow the heading format in csv file as what I wrote in "realizing function" part

# bug 3
The store files in /neo4j-community-3.5.12/data/databases/graph.db 
are left as they are, although they are likely in an unusable state.
Starting a database on these store files will likely fail or observe inconsistent records so start
at your own risk or delete the store manually unexpected error: ERROR in input
# this is a ingestion method issue
# the "neo4j-admin import" can only put data in an empty database
# so if we didn't delete the former database, it cannot add up new data
# for now we can delete the old database
# later we'd better use "LOAD CSV" to import small to medium-sized CSV files into an existing database.

# bug 4
original error: 1 (global id space)-[5]->258 (global id space) referring to missing node 1
# this is the data from node file and relationship file matching issue
# for some of the nodes in node file do not have relationship with others
# we should add "--ignore-missing-nodes" in code to fix this
```
# Project model
## collaborate filtering
### data import format
As showed before, the ingestion data format should be as follow:
1.**user detail** (node file)
user_id:ID|age|gender|location|friends_count|reviews_count|:LABEL
---|------|------|-----|----|----|---
2.**author information** (node file)
author_id:ID|name|:LABEL
---|----|---
3.**weighted rating** (relationship file)
user|author|weighted rating|:TYPE
---|------|----|---

### weighted rating calculation
**book table**
|book_id|author|language|title|avg_rating|
---|------|------|----|---
**book-author table**
book_id|author_id
---|---
**rating table**
user_id|book_id| rating
---|------|---
**to read table**
user_id|book_id
---|---

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191118194359340.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDM3MjcxOQ==,size_16,color_FFFFFF,t_70)
## The shortest path

## Community detection
The louvain algorithm is used as a cluster function.
```powershell
call algo.louvain.stream('ENTITY','LIKE')
YIELD nodeId,community
WHERE algo.getNodeById(nodeId).Type = "AUTHOR"
return community, count(*)
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191121194458508.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDM3MjcxOQ==,size_16,color_FFFFFF,t_70)
In this outcome we can find the users who shares the same taste on book, because they have a similar behaviour on rating. As a result, we can recommend the people in one cluster to become friends and the interaction between users can add up their adhesiveness.
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191121194558167.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDM3MjcxOQ==,size_16,color_FFFFFF,t_70)
