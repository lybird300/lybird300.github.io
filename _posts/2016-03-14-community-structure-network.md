---
layout: post
title: "Recover community structure in a dynamic network (predict missing links)"
date: 2016-03-14
---
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
