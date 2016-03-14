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


Random walks on graphs have been considered for computing
node proximities in large graphs
The unsupervised methods for link prediction were extensively
evaluated by Liben-Nowell and Kleinberg [21] who found that the
Adamic-Adar measure of node similarity [1] performed best. Adamic and Adar [1]
consider a related measure, in the context of deciding when two personal home pages are strongly
“related.” To do this, they compute features of the pages, and define the similarity between two
pages to be This refines the simple counting of common features by weighting rarer features more heavily.
