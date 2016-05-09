---
layout: post
title: "Large-scale network analysis"
date: 2016-03-14
---
<h2>Software and programming languages</h2>
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

<h2>Theories and algorithms</h2>
<h3>Adjacency matrix</h3>
First I'd like to cite some very good intuitions on eigenvectors and eigenvalues from <a href="http://math.stackexchange.com/questions/243533/how-to-intuitively-understand-eigenvalue-and-eigenvector">this post</a>. I think the first one below centers on the matrix, while the second one centers on the vector.
<blockquote>Personally, I feel that intuition isn't something which is easily explained. <b>Intuition in mathematics is synonymous with experience and you gain intuition by working numerous examples</b>. With my disclaimer out of the way, let me try to present a very informal way of looking at eigenvalues and eigenvectors.
First, let us forget about principal component analysis for a little bit and ask ourselves exactly what eigenvectors and eigenvalues are. A typical introduction to spectral theory presents eigenvectors as vectors which are fixed in direction under a given linear transformation. The scaling factor of these eigenvectors is then called the eigenvalue. Under such a definition, I imagine that many students regard this as a minor curiosity, convince themselves that it must be a useful concept and then move on. It is not immediately clear, at least to me, why this should serve as such a central subject in linear algebra.
Eigenpairs are a lot like the roots of a polynomial. It is difficult to describe why the concept of a root is useful, not because there are few applications but because there are too many. If you tell me all the roots of a polynomial, then mentally I have an image of how the polynomial must look. For example, all monic cubics with three real roots look more or less the same. So one of the most central facts about the roots of a polynomial is that they ground the polynomial. A root literally roots the polynomial, limiting it's shape.
Eigenvectors are much the same. If you have a line or plane which is invariant then there is only so much you can do to the surrounding space without breaking the limitations. So in a sense eigenvectors are not important because they themselves are fixed but rather they limit the behavior of the linear transformation. Each eigenvector is like a skewer which helps to hold the linear transformation into place.
Very (very, very) roughly then, the eigenvalues of a linear mapping is a measure of the distortion induced by the transformation and the eigenvectors tell you about how the distortion is oriented. It is precisely this rough picture which makes PCA very useful.
Suppose you have a set of data which is distributed as an ellipsoid oriented in 33-space. If this ellipsoid was very flat in some direction, then in a sense we can recover much of the information that we want even if we ignore the thickness of the ellipse. This what PCA aims to do. The eigenvectors tell you about how the ellipse is oriented and the eigenvalues tell you where the ellipse is distorted (where it's flat). If you choose to ignore the "thickness" of the ellipse then you are effectively compressing the eigenvector in that direction; you are projecting the ellipsoid into the most optimal direction to look at. To quote wiki:
<b>PCA can supply the user with a lower-dimensional picture, a "shadow" of this object when viewed from its (in some sense) most informative viewpoint</b></blockquote>
<blockquote>First let us think what a square matrix does to a vector. Consider a matrix A∈Rn×n. Let us see what the matrix A acting on a vector x does to this vector. By action, we mean multiplication i.e. we get a new vector y=Ax.
The matrix acting on a vector x does two things to the vector x.
It scales the vector.
It rotates the vector.
However, for any matrix A, there are some favored vectors/directions. When the matrix acts on these favored vectors, the action essentially results in just scaling the vector. There is no rotation. These favored vectors are precisely the eigenvectors and the amount by which each of these favored vectors stretches or compresses is the eigenvalue.
So why are these eigenvectors and eigenvalues important? Consider the eigenvector corresponding to the maximum (absolute) eigenvalue. If we take a vector along this eigenvector, then the action of the matrix is maximum. <b>No other vector when acted by this matrix will get stretched as much as this eigenvector</b>.
Hence, if a vector were to lie "close" to this eigen direction, then the "effect" of action by this matrix will be "large" i.e. the action by this matrix results in "large" response for this vector. The effect of the action by this matrix is high for large (absolute) eigenvalues and less for small (absolute) eigenvalues. Hence, the directions/vectors along which this action is high are called the principal directions or principal eigenvectors. The corresponding eigenvalues are called the principal values.</blockquote>
<h3>Recover community structure in a dynamic network (predict missing links)</h3>
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

<h3>Clustering on a graph with negative edge weights</h3>
The need of doing so is conveyed in <a href="http://math.stackexchange.com/questions/1547225/cluster-into-communities-a-graph-with-negative-edge-weights-representing-repulsi">this post</a>.
<h2>References</h2>
