# Neo4j_tutorial
For beginner

1.Install java jdk 15 because 16 is not capatable with neo4j desktop 4.33

https://mac.filehorse.com/download-java-development-kit/16362/

2.how to change jdk version in mac os 

3.find /Library/Java/JavaVirtualMachines

![image](https://user-images.githubusercontent.com/49715687/140242568-879d4364-3e2c-467b-9386-b7eee7a6325a.png)

4.open terminal

input java -version

![image](https://user-images.githubusercontent.com/49715687/140242738-623227c3-ed5f-4387-badd-98b4d45f8289.png)

export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk-15.0.1.jdk/Contents/Home/ 

export PATH=$JAVA_HOME/bin:$PATH

![image](https://user-images.githubusercontent.com/49715687/140242983-e2132b7f-4c06-445f-be3c-edc08f7d44b7.png)



1.Download neo4j desktop.

WITH "https://github.com/neo4j-graph-analytics/book/raw/master/data/" AS base

WITH base + "transport-nodes.csv" AS uri

LOAD CSV WITH HEADERS FROM uri AS row

MERGE (place:Place {id:row.id})

SET place.latitude = toFloat(row.latitude),

 place.longitude = toFloat(row.longitude),
 
 place.population = toInteger(row.population);
 
2.
WITH "https://github.com/neo4j-graph-analytics/book/raw/master/data/" AS base

WITH base + "transport-relationships.csv" AS uri

LOAD CSV WITH HEADERS FROM uri AS row

MATCH (origin:Place {id: row.src})

MATCH (destination:Place {id: row.dst})

MERGE (origin)-[:EROAD {distance: toInteger(row.cost)}]->(destination);

or

WITH "https://gitee.com/lfshao/book/raw/master/data/" AS base

WITH base + "transport-relationships.csv" AS uri

LOAD CSV WITH HEADERS FROM uri AS row

MATCH (origin:Place {id: row.src})

MATCH (destination:Place {id: row.dst})

MERGE (origin)-[:EROAD {distance: toInteger(row.cost)}]->(destination);


3.
![image](https://user-images.githubusercontent.com/49715687/140248544-69bf911e-2f01-44c3-80ae-c457e2a91579.png)

4.
MATCH (source:Place {id: "Amsterdam"}),
 (destination:Place {id: "London"})
CALL gds.alpha.shortestPath.stream({
 startNode: source,
 endNode: destination,
 nodeProjection: "*",
 relationshipProjection: {
all: {
type: "*",
 orientation: "UNDIRECTED"
 }
 }
})
YIELD nodeId, cost
RETURN gds.util.asNode(nodeId).id AS place, cost;

5.Random algorithm

MATCH (source:Place {id: "London"})
CALL gds.alpha.randomWalk.stream({
start: id(source),
 nodeProjection: "*",
 relationshipProjection: {
all: {
type: "*",
 properties: "distance",
 orientation: "UNDIRECTED"
 }
 },
 steps: 5,
 walks: 1
})
YIELD nodeIds
UNWIND gds.util.asNodes(nodeIds) as place
RETURN place.id AS place
