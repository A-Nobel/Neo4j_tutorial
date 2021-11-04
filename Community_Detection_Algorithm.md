11  

WITH "https://gitee.com/lfshao/book/raw/master/data/" AS base  
WITH base + "sw-nodes.csv" AS uri  
LOAD CSV WITH HEADERS FROM uri AS row  
MERGE (:Library {id: row.id});  

2.  
WITH "https://github.com/neo4j-graph-analytics/book/raw/master/data/" AS base  
WITH base + "sw-relationships.csv" AS uri  
LOAD CSV WITH HEADERS FROM uri AS row  
MATCH (source:Library {id: row.src})  
MATCH (destination:Library {id: row.dst})  
MERGE (source)-[:DEPENDS_ON]->(destination);  
