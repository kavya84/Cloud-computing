# Cloud-computing and Big-data
PROJECT-1
Map-reduce program for Netflix data analysis.The dataset contains movies and for each movie the user's ratings are calculated from 1 to 5.Calculate avg rating the user give to movies and construct histograms of the frequency distribution of these average ratings from all users using map-reduce 

------------------------------------------------------------------------------------------------------------------------------------------------
PROJECT-2 Block Matrix addition using map-reduce 
For this project, you are asked to implement block matrix addition in Map-Reduce. You will take two sparse matrices as input, convert them to block matrices, and then perform block matrix addition on them. The sum of two matrices M and N is the matrix R such that Rij=Mij+Nij. A sparse matrix is a dataset of triples (i,j,v), where i and j are the indices and v is that matrix value at the indices i and j. A triple is constructed with the Java class Triple. A block matrix is a dataset of blocks. Each block is a dense Java matrix of size rows*columns constructed with the Java class Block (here, rows=columns=100). A block matrix is stored in HDFS as a binary file (in SequenceTextInputFormat) of key-values, where the key is a pair of block coordinates (ci,cj) (constructed with the Java class Pair) and the value is a block. A matrix element Mij is stored inside the block with block coordinates (i/rows,j/columns) at the location (i%rows,j%columns) inside the block. The block matrix addition of M and N is done by finding blocks from M and N with the same block coordinates and by adding the blocks together using regular matrix addition in Java.
Your project is to convert two sparse matrices M and N which are read from files to block matrices and then add them using block matrix addition. First, you need to convert a sparse matrix to a block matrix using the following Map-Reduce pseudo-code (called twice, for M and for N):
---------------------------------------------------------------------------------------------------------------------------------------------------------

PROJECT-3
Graph processing using map-reduce
An undirected graph is represented in the input text file using one line per graph vertex. For example, the line
1,2,3,4,5,6,7
represents the vertex with ID 1, which is connected to the vertices with IDs 2, 3, 4, 5, 6, and 7. For example, the following graph: 
 
3,2,1
2,4,3
1,3,4,6
5,6
6,5,7,1
0,8,9
4,2,1
8,0
9,0
7,6
Your task is to write a Map-Reduce program that finds the connected components of any undirected graph and prints the size of these connected components. A connected component of a graph is a subgraph of the graph in which there is a path between any two vertices in the subgraph. For the above graph, there are two connected components: one 0,8,9 and another 1,2,3,4,5,6,7. Your program should print the sizes of these connected components: 3 and 7.
The following pseudo-code finds the connected components. It assigns a unique group number to each vertex (we are using the vertex ID as the initial group number), and for each graph edge between Vi and Vj, it changes the group number of these vertices to the minimum group number of Vi and Vj. That way, vertices connected together will eventually get the same minimum group number, which is the minimum vertex ID among all vertices in the connected component. First you need a class to represent a vertex:
class Vertex extends Writable {
  short tag;           // 0 for a graph vertex, 1 for a group number
  long group;          // the group where this vertex belongs to
  long VID;            // the vertex ID
long[] adjacent;     // the vertex neighbors
  ...
}
Class Vertex must have two constructors: Vertex(tag,group,VID,adjacent) and Vertex(tag,group).
First Map-Reduce job:
map ( key, line ) =
  parse the line to get the vertex VID and the adjacent vector
  emit( VID, new Vertex(0,VID,VID,adjacent) )

Second Map-Reduce job:
map ( key, vertex ) =
  emit( vertex.VID, vertex )   // pass the graph topology
  for n in vertex.adjacent:
     emit( n, new Vertex(1,vertex.group) )  // send the group # to the adjacent vertices
reduce ( vid, values ) =
  m = Long.MAX_VALUE;
  for v in values:
     if v.tag == 0
        then adj = v.adjacent.clone()     // found the vertex with vid
     m = min(m,v.group)                   // regardless of v.tag
  emit( m, new Vertex(0,m,vid,adj) )      // new group #
Final Map-Reduce job:
map ( group, value ) =
   emit(group,1)
reduce ( group, values ) =
   m = 0
   for v in values
       m = m+v
   emit(group,m)
-----------------------------------------------------------------------------------------------------------------------------------------------------------

PROJECT-5
Graph processing using Scala
Like in Project 3, your task is to write a Spark program that finds the connected components of any undirected graph and prints the size of these connected components. A connected component of a graph is a subgraph of the graph in which there is a path between any two vertices in the subgraph. As you can see in the incomplete Graph.scala program, the variable graph has type RDD[ ( Long, Long, List[Long] ) ]. That is, it is a dataset of vertices, where each vertex is a triple (group,id,adj), where id is the vertex id, group is the group id (initially equal to id), and adj is the list of outgoing neighbors. For example, from the input line 8,5,6,7, you need to return the tuple (8,8,List(5,6,7)). Then, during the for-loop, you need to construct a dataset which, for each vertex, it generates group candidates. For example, from the vertex (4,8,List(5,6,7)) you generate the group candidates (8,4), (5,4), (6,4), and (7,4), that is, 8 can be in group 4, 5 can be in group 4, etc. Then, for each vertex id, you select the minimum group candidate. This will be the new group for this vertex at this iteration. These new groups are stored in the variable groups. Then, you need to reconstruct the graph with the new groups so that your program can do more iterations. After the loop, you print the sizes of each group.
-----------------------------------------------------------------------------------------------------------------------------------------------------------

PROJECT-6
NETFLIX DATA ANALYSIS IN PIG 
You are asked to re-implement Project #1 (Netflix data analysis) using Apache Pig. In your Pig script, you can access the path of the input dataset as '$G' and the output path as '$O'. That is, you can use LOAD '$G' USING ..., to load the dataset and STORE X INTO '$O' ..., to write the relation X to the output directory. Also, if there is a read error in the input, the LOAD operation will set the result values to NULL, which can be tested using the condition: IS NOT NULL.
----------------------------------------------------------------------------------------------------------------------------------------------------------
PROJECT-7 
NETFLIX DATA ANALYSIS IN SPARK SQL
You are asked to re-implement Project #1 (Netflix data analysis) using Spark SQL. Do not use the Spark Core API (RDD methods) to process the data - but you may use RDD methods to read the data. An empty src/main/scala/Netflix.scala is provided as well as scripts to run this code on Expanse. The input data are the same as in Project #1. The output must be sorted by the ratings. You can cast a double to an int using the SQL cast function.
----------------------------------------------------------------------------------------------------------------------------------------------------------
PROJECT-8
CONNECTED COMPONENTS OF A GRAPH USING GRAPHX
You are asked to re-implement Project #5 (the connected components of a graph) using Pregel on Spark GraphX. A Scala file project8/src/main/scala/ConnectedComponents.scala is provided that contains parts of the program that you need to write. You should modify ConnectedComponents.scala only. You should use the pregel method from the GraphX Pregel API Links to an external site.only to write your code. Your main program should take the text file that contains the graph (small-graph.txt or large-graph.txt) as an argument and print the results to the output. The stopping condition is when the number of repetition reaches 5.
