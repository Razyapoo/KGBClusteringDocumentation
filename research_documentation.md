<h1>Optimization of knowledge graphs visualization</h1>

Table of content:
- [Introduction](#introduction)
- [References](#references)

<h2 id="introduction">1. Introduction</h2>

A knowledge graph, also known as a semantic network, represents a network of real-world entities: objects, events, situations, or concepts, and illustrates the relationship between them. This information is typically stored in a graph database and visualized as a graph structure, giving rise to the term "knowledge graph" [[1]](#references). The most common way to represent knowledge graphs is to use the RDF standard.

However, it can be difficult for non-specialists to study knowledge graphs due to background technicalities. To solve this problem, the paper “Interactive and iterative visual exploration of knowledge graphs based on shared and reusable visual configurations” [[2]](#references) proposes the Knowledge Graph Visual browser interactive tool which allows non-specialists to explore knowledge graphs without knowing the underlying technical details.

This paper is organized as follows. Section [2](#motivation) describes the motivation used in our extension. Section [3](#approaches) describes different approaches used to simplify large graphs. Section [4](#references) contains useful links to the papers and webpages referenced in our paper.

<h2 id="motivation">2. Motivation</h2>

Often the graphs are quite large, contain too much detail, which slows down and makes them difficult to visualize. And as a result, they do not provide easy visual learning and understanding for regular users.

A good visualization can show (reveal) patterns in the graph that are of value to the user. And such visualization can also be used for the presentation purposes.

This paper proposes several prototypes (developed by the student or found in other research papers) used to optimize the visualization of knowledge graphs so that they are well organized and easy to understand for ordinary users. It is also supposed to compare prototypes and implement at least one of them.

<h2 id="approaches">3. Approaches</h2>

There are two approaches to start with. The first approach is to reduce the time used to render large graphs, i.e., optimize the backend methods used in graph rendering. This approach does not reduce the amount of detail shown on the graph, but only the rendering time.

The second approach is to make the graph more readable and understandable for non-specialists, i.e. optimize frontend visualization. For example, to reduce the amount of detail such as edges or nodes, or to use a different, more efficient, layout algorithm. This approach takes precedence because the original proposal of the Knowledge Graph Visual Browser was to hide technical complexity such as SPARQL queries and RDF standards. Therefore, this paper will rely on this approach.

By examining what a good, understandable graph should look like, and relying on articles [[3]](#references), [[4]](#references), we can establish several criteria and rely on them:
- Intuitiveness / Perceptibility / Easy navigation
- Simplicity
- Usefulness
- highlighting key details

<h3 id="filtering">Filtering</h3>

The first way to simplify the graph is to use filtering. It helps users extract and understand basic information in graphs. The method should allow users to freely select attributes and relationships they are interested in and then use these features to create a small and informative summary graph that reveals the basic characteristics of the nodes and their relationships in the original graph.

However, this is the topic of another student, so we can skip it.

<h3 id="layouts">Layouts</h3>

The second way is to use an existing layout or implement own. 

Let's highlight a few criteria that need to be considered in a good layout that can be used to show large graphs:
1. nodes connected by an edge must be close to each other
2. there must be the minimum number of intersections of edges, or even no intersections
3. large amount of nodes should be split into chunks, or clusters
4. clusters should contain related nodes
5. overall graph representation should represent (tell) a good story in understandable way

It is very difficult and time consuming to implement the own layout, because this requires to make changes in the big part of the Cytoscape library.

---

Therefore, we can integrate graph simplification techniques with existing layouts to make the graph more user-friendly. To do this, we describe the following five techniques:
- [Elimination of redundant nodes and edges. Sparsification](#nodes-edges-eliminations)
- [Summarization](#summarization)
- [Clustering](#clustering)
- [Coarsening](#coarsening)
- [Condensation](#condensation)

<h4 id="nodes-edges-eliminations">Elimination of redundant nodes and edges. Sparsification</h4>

This technique is used to compress the original graph, remove all redundant nodes without changing the graph structure. 

A key difference between graph compression and graph summarization is that graph summarization focuses on finding structural patterns within the original graph, whereas graph compression focuses on compressions the original graph to be as small as possible. 

<h4 id="summarization">Summarization</h4>

Graph summarization transforms graphs into more compact representations while preserving structural patterns. They produce summary graphs in the form of supergraphs. Supergraphs are the most common representation, which consists of supernodes and original nodes that are connected by edges and superedges that represent aggregated original edges [[5]](#references).

As was mentioned in the [Elimination of redundant nodes and edges](#nodes-edges-eliminations) section above, sparsification technique removes unimportant (redundant) nodes and edges from the graph that are difficult to recover, so we decided to continue exploring grouping/aggregation summarization methods.

<h4 id="clustering">Clustering</h4>

Graph clustering is the process of grouping the nodes of the graph into clusters, taking into account edge and node structures of the graph in such a way that there are several edges within each cluster and very few (or even no edges) between clusters [[6]](#references). Graph clustering clusters the nodes based on their similarity measure.

<h4 id="coarsening">Coarsening</h4>

The goal of this method is to replace the original graph by one which has fewer nodes, but whose structure and characteristics are similar to those of the original graph. Usually nodes with similar properties are grouped into a clusters. These similarity clusters form the new nodes of the coarsened graph and are hence termed as supernodes [[7]](#references).

<h3 id="condensation">Condensation</h3>

Graph condensation returns a directed graph whose nodes represent the strong components of the original graph. This reduction provides a simplified view of the connectivity between components. 

The main advantage of this approach is that it simplifies the original graph so that the various algorithms that run on the graph become faster. 

The disadvantage of this method is that as its output we get a directed acyclic graph (DAG), in which each strongly connected component (SCC) does not take into account node classes and consists of a mix of nodes. As an improvement, we can add a criterion that the SCC should only contain certain node classes.

This is the first technique implemented. I implemented it just to understand the Knowledge Graph Visual Browser implementation and how it works. The idea was to use the graph condensation method and create a new graph consisting of nodes that represent strongly connected components in the original graph.

---

Let's find out what types of layouts exist that are used to present large graphs and can be useful in Knowledge Graph Visual browser.

<h3 id="sequential-layout">Sequential layout</h3>

Because in the Knowledge Graph Visual Browser we always expand the neighborhood of a node, it can be useful to use sequential layout.

<p align="center">
    <img src="img/sequential_layout.png" alt="sequential_layout" title="Sequential layout" width="600"/><br/>
    <em>Figure 1. Sequential layout</em>
</p>

The sequential layout (shown in the Figure 1 above) is designed to display data that contains a clear sequence of distinct levels of nodes. It takes multiple components into account and minimizes link crossings [[8]](#references).

This layout meets 1, 2, and 5 criteria listed at the beginning of the [Layouts](#layouts) section. It satisfies the first criterion because we always expand the neighborhood in the right direction (or whatever; the direction must be determined at the beginning), so we can place nodes in the neighborhood next to the expanding node. It meets the second criterion because we always expand nodes in the same direction, so we have room to accommodate its neighbors. It also meets the fifth criterion because it is visually understandable to non-specialists.

However, this layout is only good if we always expand nodes that were not in the graph up to this point. Because otherwise we would have back edges leading in the opposite direction. It is also hard to make clusters of nodes. This requires node swapping that might be time and resource consuming.

<h3 id="visual-tricks">Visual tricks</h3>

I have looked at many layouts, but unfortunately they were not supported by the Cytoscape library. So I decided to integrate the visual tricks used in these layouts into existing layouts already implemented in the Cytoscape library.

The main visual tricks:
- Hiding background
- Highlighting key nodes and edges
- Using node aggregation

<h3 id="hiding-background">Hiding background</h3>

This technique allows you to show only those nodes that are of interest to the user. This method also hides redundant nodes (or background) or makes them less visible to the user. A possible implementation of such a technique is shown in Figure 2 below.

<p align="center">
    <img src="img/hiding_nodes.png" alt="hiding_nodes" title="Hiding nodes" width="600"/><br/>
    <em>Figure 2. Hiding redundant nodes [9].</em>
</p>

However, this technique has already been implemented in the Visual Knowledge Graph Browser by Štěpán Stenchlák.

<h3 id="highlighting-key-nodes-and-edges">Highlighting key nodes and edges</h3>

The second approach is to highlight key nodes or areas (clusters) of nodes that may be of interest to the user. An example of such a representation is shown in Figure 3.

<p align="center">
    <img src="img/node_highlighting.png" alt="node_highlighting" title="node highlighting" width="600"/><br/>
    <em>Figure 3. Node and edge highlighting [10].</em>
</p>

<h3 id="node-aggregation">Node aggregation</h3>

Node aggregation is an example of [graph coarsening](#coarsening). A coarsened graph consists of aggregated nodes. But this is not what I wanted to do, because in this way we lost other nodes and edges. In my opinion, I imagined that it would be much better to still be able to reveal nodes and edges hidden within aggregated nodes.

However, this caused a problem in the knowledge graph explorer because there are Detail queries that are used to display node details, and if I used aggregate nodes, it would be impossible to display aggregated node details because they would be auxiliary and would not exist in the endpoint database. Same was for expansion and preview queries. Thus, we would lose all the functionality that the Knowledge Graph Visual Browser provides.

To solve this problem, I decided to use a trick: if the user clicks on an aggregated node, a browser will display the internals of that aggregated node. The Cambridge Intelligence call this approach - [combos](https://cambridge-intelligence.com/combos/). 

I presented this approach to my supervisor and two weeks later he told me that he had found a customer who is interested in this approach. The customer was the Charles University and the topic was "Departments and subjects".

I suggested several criteria:

- An aggregation node must be a cluster of related nodes (for example, nodes of the same class or similar property)
- An aggregation node must somehow show the user which nodes he has inside
- An aggregation node can have separate aggregation nodes internally representing the types of nodes it aggregates (in case it aggregates different types of nodes, such as universities and subjects).
- An aggregation node can display the number of nodes it has inside.

The questions were:
- based on what criteria to cluster nodes
- how to name and represent the aggregation node
- can cluster have different classes of nodes
- how to represent edges between nodes that represent an aggregation of the original nodes (create only one aggregated edge, i.e. its thickness will show how many edges it combines, or show them all)
- how to show the internals of an aggregation node

The first draft of the approach is shown in the Figure 4 below.

<p align="center">
    <img src="img/firts_draft_diagram.png" alt="first_draft" title="First draft" width="900"/><br/>
    <em>Figure 4. First draft.</em>
</p>

From this moment we started to use the term "hierarchy". And in discussion with my supervisor, we formulated the following main criteria:
- There can be hierarchical and non-hierarchical (ordinary, represented by an edge) relationships between nodes
- The hierarchical extension must show the extension within (inside) the expanded node
- It should be possible in the configuration to determine if the relationship is hierarchical or non-hierarchical
- A non-hierarchical edge can lead from a parent node, even if it contains child nodes inside
- Child nodes can be collapsed into parent nodes

We also decided to use the map-style zoom used in mapping platforms, so that when you zoom in, you see more detail in terms of nodes, and when you zoom out, you see less detail in terms of nodes.

The point was not to stick with the "Departments with Items" topic only, but to make it abstract and usable with other topics.
 
The approach and its full implementation is described in the [technical](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/technical_documentation.md) and [user](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/user_documentation.md) documentations.

<h2 id="references">References</h2>

1. ["What is a Knowledge Graph?"](https://www.ibm.com/cloud/learn/knowledge-graph), by IBM Cloud Education, April 12, 2021
2. [Interactive and iterative visual exploration of knowledge graphs based on shareable and reusable visual configurations](https://www.sciencedirect.com/science/article/pii/S1570826822000105#b2) by Martin Nečaský, Štěpán Stenchlák
3. [The 10 rules of great graph design](https://cambridge-intelligence.com/10-rules-great-graph-design/), by Corey Lanum, January 10, 2014
4. [Data Visualization Effectiveness Profile](http://perceptualedge.com/articles/visual_business_intelligence/data_visualization_effectiveness_profile.pdf), by Stephen Few, 2017
5. [NetworkX. Summarization](https://networkx.org/documentation/stable/reference/algorithms/summarization.html)
6. [Graph clustering](https://paperswithcode.com/task/graph-clustering)
7. [Coarsening Graphs with Neural Networks](https://karush27.github.io/posts/2012/08/blog-post-24/), October 11, 2021
8. [Sequential layout: the best way to handle tiered data](https://cambridge-intelligence.com/sequential-layout-the-best-way-to-handle-tiered-data/), by Julia Robson, June 15, 2022
9. [Customer behavior analysis with data visualization](https://cambridge-intelligence.com/customer-behavior-analysis/), by Rosy Hunt, August 30, 2022
10. [Pharma data visualization](https://cambridge-intelligence.com/use-cases/pharma/)