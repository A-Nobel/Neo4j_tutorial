1.
WITH "https://gitee.com/lfshao/book/raw/master/data/" AS base  
WITH base + "sw-nodes.csv" AS uri  
LOAD CSV WITH HEADERS FROM uri AS row  
MERGE (:Library {id: row.id});  

2.  
WITH "https://gitee.com/lfshao/book/raw/master/data/" AS base  
WITH base + "sw-relationships.csv" AS uri  
LOAD CSV WITH HEADERS FROM uri AS row  
MATCH (source:Library {id: row.src})  
MATCH (destination:Library {id: row.dst})  
MERGE (source)-[:DEPENDS_ON]->(destination);  

3.  
CALL gds.alpha.triangles({  
 nodeProjection: "Library",  
 relationshipProjection: {  
 DEPENDS_ON: {  
type: "DEPENDS_ON",  
 orientation: "UNDIRECTED"  
 }  
 }  
})  
YIELD nodeA, nodeB, nodeC  
RETURN gds.util.asNode(nodeA).id AS nodeA,  
 gds.util.asNode(nodeB).id AS nodeB,  
 gds.util.asNode(nodeC).id AS nodeC;  

4.
CALL gds.localClusteringCoefficient.stream({
 nodeProjection: "Library",
 relationshipProjection: {
 DEPENDS_ON: {
type: "DEPENDS_ON",
 orientation: "UNDIRECTED"
 }
 }
 })
YIELD nodeId, localClusteringCoefficient
WHERE localClusteringCoefficient > 0
RETURN gds.util.asNode(nodeId).id AS library, localClusteringCoefficient
ORDER BY localClusteringCoefficient DESC;

5.
CALL gds.alpha.scc.stream({
 nodeProjection: "Library",
 relationshipProjection: "DEPENDS_ON"
})
YIELD nodeId, partition
RETURN partition, collect(gds.util.asNode(nodeId).id) AS libraries
ORDER BY size(libraries) DESC;

6.
CALL gds.wcc.stream({
 nodeProjection: "Library",
 relationshipProjection: "DEPENDS_ON"
})
YIELD nodeId, componentId
RETURN componentId, collect(gds.util.asNode(nodeId).id) AS libraries
ORDER BY size(libraries) DESC;

7.
CALL gds.labelPropagation.stream({
 nodeProjection: "Library",
 relationshipProjection: "DEPENDS_ON",
 maxIterations: 10
})
YIELD nodeId, communityId
RETURN communityId AS label,
collect(gds.util.asNode(nodeId).id) AS libraries
ORDER BY size(libraries) DESC;

8.
CALL gds.labelPropagation.stream({
 nodeProjection: "Library",
 relationshipProjection: {
 DEPENDS_ON: {
type: "DEPENDS_ON",
 orientation: "UNDIRECTED"
 }
 },
 maxIterations: 10
})
YIELD nodeId, communityId
RETURN communityId AS label,
collect(gds.util.asNode(nodeId).id) AS libraries
ORDER BY size(libraries) DESC;
