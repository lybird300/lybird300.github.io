---
layout: post
title: "Large-scale network analysis"
date: 2016-03-14
---
<h2>Network analysis on a cluster</h2>
On Linux you can try some simple scripts to get a quick intuition about the network data before plotting the entire network, which can be too large to handle easily. Bash scripts are often more convenient to use than coding with other programming languages.
Suppose the network is stored as an edge list (each line defines an edge) as below
<pre>nodeA nodeB weight</pre>
Then you can use the following command to compute node degrees (assuming indirected networks). The degree report should give you intuition about how well connected the network is.
<pre><code>
#Compute out-degree of all nodes in nodeA column
cat network.vna |  " " | sort | uniq -c | sort -n
#Compute in-degree of all nodes in nodeB column
cat network.vna | cut -f 2 -d " " | sort | uniq -c | sort -n
</code></pre>
With AWK you can easily filter your network based on the weight column.
<pre><code>
#Only print edges which have weight larger than 20:
awk 'NR <= 2 { print; next; } NR > 2 { if($3 >= 20) {print}}' network.vna > network-w20.vna
#NR stands for line number - in the example we skip first 2 lines because they are usually the vna format headers (*Tie data\nfrom to weight).
#When NR is larger than 2 the script prints the line if the third weight parameter is larger than 20.
#Output is redirected to a file network-w20.vna.
</code></pre>
HOWEVER, things will become a lot different (in a bad way) when the edge list is extremely big, say 10G. Why? Well, for starters, the sort command stores working data in temporary disk files (usually in /tmp). When the file to sort is large, the temporary files will soon fill up the directory! I ended up redirecting the temporary file using -T option, so that the sorting process can finish. For example, I used the following command to find out the list of unique nodes from the 10G edge list file (lrrkPairsAtSite_3999.out, the separator is comma rather than tab) and output the results to the file 3999_node.tmp.
<pre><code><lrrkPairsAtSite_3999.out <lrrkPairsAtSite_3999.out awk -F',' '{ print $1; print $2; }' | sort -n -T /scratch/linly --buffer-size=500M | uniq > 3999_node.tmp</code></pre>
<h3>igraph</h3>

<h3>networkX</h3>
I use the following Python command to read a graph from a gzipped edge list file with a header like "hap1,hap2,3999"
<pre><code>g <- networkx.read_weighted_edgelist("/projects/.../lrrkPairsAtSite_3999.out.gz",comments='h',delimiter=",", nodetype=str)</code></pre>
Then I use the following Python command to export the graph as a pajek format file so that it can be used in other programs.
<pre><code>networkx.write_pajek(g, "/projects/.../lrrkPairsAtSite_3999.net")</code></pre>
In the generated .net file, there is useless coordinate information for visualization (0.0 0.0 ellipse) after the definition of each vertex (per line). I use the following Linux command to remove this information from EVERY .net file
<pre><code>find . -type f -name "lrrkPairsAtSite_*.net" -exec sed -i 's/ 0.0 0.0 ellipse//g' {} +</code></pre>
<h2>Recover community structure in a dynamic network (predict missing links)</h2>
In general, very little is known about the community
structure of a graph. It is uncommon to know the number
of clusters in which the graph is split, or other indications
about the membership of the vertices. In such
cases clustering procedures like graph partitioning methods
can hardly be of help, and one is forced to make some
reasonable assumptions about the number and size of the
clusters, which are often unjustified. On the other hand,
the graph may have a hierarchical structure, i. e. may
display several levels of grouping of the vertices, with
small clusters included within large clusters, which are
in turn included in larger clusters, and so on. Social networks,
for instance, often have a hierarchical structure
(Section III.C.1). In such cases, one may use hierarchical
clustering algorithms (Hastie et al., 2001), i. e. clustering
techniques that reveal the multilevel structure of the
graph. Hierarchical clustering is very common in social
network analysis, biology, engineering, marketing, etc.
The starting point of any hierarchical clustering
method is the definition of a similarity measure between
vertices. After a measure is chosen, one computes the
similarity for each pair of vertices, no matter if they are
connected or not. At the end of this process, one is left
with a new n×n matrix X, the similarity matrix. In Section
III.B.4 we have listed several possible definitions of
similarity. Hierarchical clustering techniques aim at identifying
groups of vertices with high similarity, and can be
classified in two categories:
1. Agglomerative algorithms, in which clusters are iteratively
merged if their similarity is sufficiently
high;
2. Divisive algorithms, in which clusters are iteratively
split by removing edges connecting vertices with
low similarity.

Hierarchical clustering has the advantage that it does
not require a preliminary knowledge on the number and
size of the clusters. However, it does not provide a way
to discriminate between the many partitions obtained by
the procedure, and to choose that or those that better
represent the community structure of the graph. The
results of the method depend on the specific similarity
measure adopted. The procedure also yields a hierarchical
structure by construction, which is rather artificial
in most cases, since the graph at hand may not have a
hierarchical structure at all. Moreover, vertices of a community
may not be correctly classified, and in many cases
some vertices are missed even if they have a central role
in their clusters (Newman, 2004a). Another problem is
that vertices with just one neighbor are often classified
as separated clusters, which in most cases does not make
sense. Finally, a major weakness of agglomerative hierarchical
clustering is that it does not scale well. If points
are embedded in space, so that one can use the distance
as dissimilarity measure, the computational complexity
is O(n
2
) for single linkage, O(n
2
log n) for the complete
and average linkage schemes. For graph clustering, where
a distance is not trivially defined, the complexity can become
much heavier if the calculation of the chosen similarity
measure is costly

Partitional clustering indicates another popular class
of methods to find clusters in a set of data points. Here,
the number of clusters is preassigned, say k. The points
are embedded in a metric space, so that each vertex is
a point and a distance measure is defined between pairs
of points in the space. The distance is a measure of dissimilarity
between vertices. The goal is to separate the
points in k clusters such to maximize/minimize a given cost function based on distances between points and/or
from points to centroids, i. e. suitably defined positions in
space

Random walks on graphs have been considered for computing
node proximities in large graphs
The unsupervised methods for link prediction were extensively
evaluated by Liben-Nowell and Kleinberg [21] who found that the
Adamic-Adar measure of node similarity [1] performed best. Adamic and Adar [1]
consider a related measure, in the context of deciding when two personal home pages are strongly
“related.” To do this, they compute features of the pages, and define the similarity between two
pages to be This refines the simple counting of common features by weighting rarer features more heavily.
