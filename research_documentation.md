<h1>Optimization of knowledge graphs visualization</h1>

Table of content:
- [Introduction](#introduction)
- [References](#references)

<h2 id="introduction">Introduction</h2>

A knowledge graph, also known as a semantic network, is a network of real world entities, i.e. objects, events, situations or concepts - and illustrates the relationship between them. This information is typically stored in a graph database and visualized as a graph structure, giving rise to the term "knowledge graph" [[1]](https://www.ibm.com/cloud/learn/knowledge-graph).

The most common way to build knowledge graphs is to use the RDF standard.

However, it can be difficult for non-specialists to study knowledge graphs. Therefore, in the article “Interactive and iterative visual exploration of knowledge graphs based on shared and reusable visual configurations”, an interactive tool (visual knowledge graph browser) was proposed that allows non-specialists to explore knowledge graphs without knowing the underlying technical details.

<h2 id="motivation">Motivation</h2>

Often graphs are quite large, slow and contain too much detail, making them difficult to visualize. They do not provide easy visual learning and understanding for ordinary users.

On the other hand, good visualization can show (reveal) patterns in the graph that are of value to the user. It can also be used for further presentation purposes.

This article proposes several prototypes (developed by a student or found in other research papers) used to optimize the visualization of the knowledge graph so that it is well organized and easy to understand for ordinary users. It is also supposed to compare them and implement at least one prototype.

The article is organized as follows. //TODO

<h2 id="approaches">Approaches</h2>

While researching how a good and understandable graph should look like, we relied on several criteria:
- nodes connected by an edge must be close to each other
- the minimum number of intersections of edges
- edges should not intersect
- correlated nodes should form clusters

The first thought was to use the existing layout. There are several layouts used to visualize large graphs: 
- 

<h2 id="references">References</h2>

1. ["What is a Knowledge Graph?"](https://www.ibm.com/cloud/learn/knowledge-graph)