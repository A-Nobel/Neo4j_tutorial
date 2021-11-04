1.
WITH "https://gitee.com/lfshao/book/raw/master/data/" AS base  
WITH base + "social-nodes.csv" AS uri  
LOAD CSV WITH HEADERS FROM uri AS row  
MERGE (:User {id: row.id});  

2.  
WITH "https://gitee.com/lfshao/book/raw/master/data/" AS base  
WITH base + "social-relationships.csv" AS uri  
LOAD CSV WITH HEADERS FROM uri AS row  
MATCH (source:User {id: row.src})  
MATCH (destination:User {id: row.dst})  
MERGE (source)-[:FOLLOWS]->(destination);  
