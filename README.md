Highest-connectivity vertex iterator
====================================

Specialised undirected graph implementation with functionality to efficiently iterate vertices in order of highest degree (connectivity)

Extraction of the most connected vertex reduces the degree of its immediate siblings, and typically changes the ordering of subsequent removal.

This re-ordering is handled efficiently; the implementation is similar to pigeonhole sort - a non-comparison-based sort, which isn't subject to the lower bounds on comparison sorts' running time of O(n log n)

Note: once built, this is a single-pass stateful iterator - removing each vertex mutates the graph for subsequent calls.

Algorithm
---------

    Build an ordered map M of {integer degree : [set of all vertices with that degree]}
    While M is not empty:
        an arbitrary vertex V is removed from the set S with highest degree
        for each of V's immediately connected vertices O :
            V is removed from O's connections
            O's degree is lowered by 1, and moved to the correct set within M
 
running time scales as O((m + n) * log(D)) to iterate over all vertices, where m = number of edges, n = number of vertices, D = cardinality of degrees of all vertices

log(D) can typically be treated as a small constant factor, this isn't typically sensitive to variance in m or n.

Code usage
----------

Vertexes are created implicitly by creating edges between vertexes. Vertexes are represented by unique Strings.

    Builder b = MostConnectedVertexGraphIterator.newBuilder();
    b.addEdge("a", "b");
    b.addEdge("a", "c");
    b.addEdge("c", "d");
    ...
    MostConnectedVertexGraphIterator g = b.build();
    while (g.hasNext()){
		Vertex v = g.next();
		for (Vertex o: v.getConnections()){
			...
		}
    }

Compilation & assembly
----------------------
	mvn compile
    mvn assembly:single

FamilyIdentifier - Usage
------------------------

input file: one line per edge, tab-separated (anything after the first two columns is ignored)
eg
    a	b	<ignored>
    a	c	<ignored>
    a	d	<ignored>
    d	e	<ignored>
    ...

output file: indented blocks per most-connected mode, with the most connected node at the top with its connectivity count. change FamilyIdentifier's output to whatever you prefer

    java -Xmx2048M -jar target/graphconnectivity-jar-with-dependencies.jar <input file> <output file>