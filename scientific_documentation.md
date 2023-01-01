<h1>Scientific documentation of the research project</h1>
<h3>Topic: Optimization in visualization of knowledge graphs: Grouping of clusters.</h3>

Team members who contribute to the Knowledge Graph Visual Browser:
- Štěpán Stenchlák
  - Basic implementation as a bachelor's thesis
- Jiří Resler
  - [Faceted filtering](#https://github.com/JiriResler/knowledge-graph-browser-frontend)
- Oskar Razyapov
  - [Grouping of clusters](https://github.com/Razyapoo/knowledge-graph-browser-frontend-grouping-of-clusters)

Full implementation and basic principals used in the "Grouping of clusters" extension are described in the [technical](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/technical_documentation.md) and [user](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/user_documentation.md) documentations.

This paper proposes several prototypes (developed by the student or found in other research papers) used to optimize the visualization of knowledge graphs so that they are well organized and easy to understand for ordinary users. It is also supposed to compare prototypes and implement at least one of them.

Table of content:
- [Introduction](#introduction)
- [Motivation](#motivation)
- [Approaches](#approaches)
- [Grouping of clusters](#grouping-of-clusters-extension)
- [References](#references)

<h2 id="introduction">1. Introduction</h2>

A [knowledge graph](https://en.wikipedia.org/wiki/Knowledge_graph), also known as a semantic network, represents a network of real-world entities: objects, events, situations, or concepts, and illustrates the relationship between them. This information is typically stored in a graph database and visualized as a graph structure, giving rise to the term "knowledge graph" [[1]](#references). The most common way to represent knowledge graphs is to use the [RDF standard](https://en.wikipedia.org/wiki/Resource_Description_Framework).

However, it can be difficult for non-specialists to study knowledge graphs due to background technicalities. To solve this problem, the paper “Interactive and iterative visual exploration of knowledge graphs based on shared and reusable visual configurations” [[2]](#references) proposes the [Knowledge Graph Visual browser](https://try.kgbrowser.opendata.cz/) - interactive tool which allows non-specialists to explore knowledge graphs without knowing the underlying technical details.

<h2 id="motivation">2. Motivation</h2>

Often the graphs are quite large, contain too much detail, which slows down their processing time and makes them difficult to visualize. And as a result, this can worsen the visual perception and study of the graph for ordinary users.

A good visualization must show (reveal) patterns in the graph that are of value to the user. Such visualization, for example, can be used for the presentation purposes.

<h2 id="approaches">3. Increasing the level of visual perception of large graphs</h2>

There are several approaches that can improve runtime at both rendering and layout stages. The first approach is aimed at reducing the rendering time of large graphs, but not the amount of detail displayed, i.e. it optimizes the internal methods used in graph rendering phase. This approach is beyond the scope of this article.

The second approach is to make the graph more readable and understandable for non-specialists, i.e. optimize layout phase. For example, to reduce the amount of detail such as edges or nodes, or to use a different, more efficient, layout algorithm. As a side effect, it can also reduce rendering time. This article is based on this approach.

Relying on the articles [[3]](#references), [[4]](#references), we can establish several following criteria indicating a good level of perception of a graph:

- Intuitiveness / Perceptibility / Easy navigation
- Simplicity
- Usefulness
- Highlighting key details

It is also useful to consider the following several criteria that can improve the level of perception of a graph:

1. Nodes connected by an edge should be close to each other
2. There should be a minimum number of intersections of edges, or even no intersections
3. Large amount of nodes should be split into chunks, or clusters
4. Clusters should contain related nodes
5. Overall graph representation should represent (tell) a good story in understandable way

Recent works [[5]](#references), [[6]](#references) approach the problem of visualization of large graphs, replacing it with "proxy graphs" - smaller representatives of large graphs. The main challenge is to find a proxy graph as *faithful*[[7]](#references) to the original graph as possible. 

In the rest of this section, we will describe and compare several techniques that can be used to increase the level of perception of a graph visualization.

<h3 id="filtering">Filtering</h3>

There may be a case when it makes no sense to visualize the entire graph. In this case, it may be useful to use filtering, as it helps users extract the information they need from the graph and hide unnecessary visual clutter. This approach allows users to filter nodes and relationships based on selected attributes and then use them to create a small and informative graph.

Filtering corresponds to a function that reduces a given set of elements to a new, smaller set that match a particular query or property. This is also applicable at a structural level where the user can select an element of interest and thus hide elements that are not connected to the selected element. The example is shown in the Figure 1 below.

<p align="center">
    <img src="img/path_filtering.png" alt="sparsification" title="Sparsification" width="700"/><br/>
    <em>Figure 1. Path filtering. A user selects "2" element (screenshot 1 to the left). The result is shown on the screenshot 2 to the right - algorithm shows only nodes connected to the selected element.</em>
</p>

However, it can be difficult for the ordinary user to find the area of interest on the graph. In this case, it may be useful to help him make a choice and identify elements that may represent the object of interest. There are several algorithms that may analyze structural graph patterns and reveal such elements, e.g., centrality algorithms - measuring the relative importance of nodes within a graph. 

Well-known representatives are [[6]](#references): 

- The Eigenvector Centrality measures the transitive (or directional) influence of nodes. Relationships of a node to high-scoring nodes contribute more to the score of that node than connections to low-scoring nodes. 
- The Closeness Centrality is a way of detecting nodes that are able to spread information efficiently through a subgraph. Nodes with a high closeness score have, on average, the shortest distances to all other nodes. 
- The Betweenness Centrality is detecting the amount of influence a node has over the flow of information or resources in a graph. It can be used to find nodes that serve as bridges from one part of a graph to another. 
- The Degree Centrality algorithm counts the number of incoming and outgoing relationships from a node. It is used to find popular nodes in a graph.

All of these methods can help the user determine the site of interest to him, with which to start interacting.

<h3 id="nodes-edges-eliminations">Sparsification. Elimination of redundant edges</h3>

Sparsification is an approximation of a given graph by a graph with fewer edges.

An example is shown in Figure 1 below.

<p align="center">
    <img src="img/sparsification.png" alt="sparsification" title="Sparsification" width="230"/><br/>
    <em>Figure 1. Sparsification.</em>
</p>

<h3 id="summarization">Summarization</h3>

Graph summarization transforms graphs into more compact representations while preserving structural patterns. They produce summary graphs in the form of supergraphs. Supergraphs are the most common representation, which consists of supernodes (represent aggregated original nodes) and original nodes that are connected by edges and superedges (represent aggregated original edges) [[7]](#references).

This approach sums up the [clustering](#clustering) and [coarsening](#coarsening).

An example is shown in Figure 2 below.

<p align="center">
    <img src="img/summarization.png" alt="summarization" title="Summarization" width="400"/><br/>
    <em>Figure 2. Summarization.</em>
</p>

<h3 id="clustering">Clustering</h3>

Graph clustering is the process of grouping nodes into clusters, taking into account the structure of edges and nodes of the graph, so that there are several edges in each cluster and very few (or even no edges) between clusters [[8]](#references). Graph clustering clusters the nodes based on their similarity measure.

#TODO Modularity

<h3 id="coarsening">Coarsening</h3>

The goal of this method is to replace the original graph by one which has fewer nodes, but whose structure and characteristics are similar to those of the original graph. Usually nodes with similar properties are grouped into a clusters. These similarity clusters form the new nodes of the coarsened graph and are hence termed as supernodes [[9]](#references).

<h3 id="condensation">Condensation</h3>

Graph condensation returns a directed graph whose nodes represent the strong components of the original graph. This reduction provides a simplified view of the connectivity between components. 

The main advantage of this approach is that it simplifies the original graph so that the various algorithms that run on the graph become faster. 

The disadvantage of this method is that as its output we get a directed acyclic graph (DAG), in which each strongly connected component (SCC) does not take into account node similarities (for example classes) and consists of a mix of nodes. As an improvement, we can add a criterion that the SCC should only contain node having similar properties.

---

Let's find out what types of layouts exist that are used to present large graphs and can be useful in Knowledge Graph Visual browser.

<h3 id="sequential-layout">Sequential layout</h3>

Because in the Knowledge Graph Visual Browser we always expand the neighborhood of a node, it can be useful to use sequential layout.

<p align="center">
    <img src="img/sequential_layout.png" alt="sequential_layout" title="Sequential layout" width="400"/><br/>
    <em>Figure 3. Sequential layout</em>
</p>

The sequential layout (shown in the Figure 3 above) is designed to display data that contains a clear sequence of distinct levels of nodes. It takes multiple components into account and minimizes link crossings [[10]](#references).

This layout meets 1, 2, and 5 criteria listed at the beginning of the [Layouts](#layouts) section. It satisfies the first criterion because we expand the neighborhood in the same direction (the direction must be determined at the beginning), so we can place nodes in the neighborhood close to the expanding node. It meets the second criterion because we are expanding the nodes in the same direction, so we have room to place its neighbors in such a way that there are no edge intersections. It also meets the fifth criterion because it is visually understandable to non-specialists.

However, this layout is only good if we always expand nodes that were not in the graph up to this point. Because otherwise we would have back edges leading in the opposite direction. It is also hard to make clusters of nodes. This requires node swapping that might be time and resource consuming.

<h3 id="visual-tricks">Visual tricks</h3>

There are many layouts with interesting features, but unfortunately they are not supported by the Cytoscape library. But we can still integrate the visual tricks used in these layouts into existing layouts already implemented in the Cytoscape library.

The main visual tricks:
- Background hiding
- Highlighting key nodes and edges
- Using node aggregation

<h4 id="background-hiding">Background hiding</h4>

This technique allows to show only those nodes that are of interest to the user. This method also hides redundant nodes (or background) or makes them less visible to the user. A possible implementation of such a technique is shown in Figure 4 below.

<p align="center">
    <img src="img/hiding_background.png" alt="hiding_nodes" title="Hiding nodes" width="600"/><br/>
    <em>Figure 4. Hiding redundant nodes [11].</em>
</p>

However, this technique has already been implemented in the Knowledge Graph Visual browser by Štěpán Stenchlák.

<h4 id="highlighting-key-nodes-and-edges">Highlighting key nodes and edges</h4>

The second approach is to highlight key nodes or areas (clusters) of nodes that may be of interest to the user. The most important nodes can also be larger than others. An example of such a representation is shown in Figure 5.

<p align="center">
    <img src="img/node_highlighting.png" alt="node_highlighting" title="Node highlighting" width="750"/><br/>
    <em>Figure 5. Node and edge highlighting [12].</em>
</p>

The disadvantage of this method is that it only improves visual perception, but does not simplify the graph and does not make it faster.

<h4 id="node-aggregation">Node aggregation</h4>

Node aggregation is an example of [graph coarsening](#coarsening). A coarsened graph consists of aggregated nodes. But this is exactly not what we want to do, because in this way we will lose other nodes and edges that may need to be restored (shown) in future. It would be much better to still be able to reveal nodes and edges hidden within aggregated nodes.

However, this can cause a problem in the Knowledge Graph Visual browser, because there are detailed queries that are used to display node details, and if we aggregate nodes, it will not be possible to display details of aggregated nodes because they will be auxiliary and will not exist in the database. The same can be said for expansion and preview queries. Thus, we may lose all the functionality that the Knowledge Graph visual browser provides.

To solve this problem, can use a trick: if the user clicks on the aggregated node, a browser will display the internals of that aggregated node. The Cambridge Intelligence call this approach as [combos](https://cambridge-intelligence.com/combos/). 

<h3 id="comparison">Comparison</h3>

To see which approach is preferable for possible non-specialist users, I chose different approaches and discussed them with work colleagues, school colleagues and a teacher (supervisor).

List of approaches: 
- [Filtering](#filtering)
- [Sparsification](#nodes-edges-eliminations)
- [Condensation](#condensation)
- [Summarization](#summarization) / [Node aggregation](#node-aggregation)
- [Sequential layout](#sequential-layout)
- [Background hiding](#background-hiding)
- [Highlighting key nodes and edges](#highlighting-key-nodes-and-edges)

As the result of discussions I can conclude following:

- [Filtering](#filtering) is a good approach, but to use it, the user must know the graph in advance so that he can filter the nodes he wants to see. This approach also correlates with the topic of another student (Jiří Resler), so my supervisor and I decided not to consider.
- Users didn't like [Sparsification](#nodes-edges-eliminations) approach because it removes all redundant edges, which can be helpful in understanding the story the graph represents.
- [Condensation](#condensation). This is the first technique implemented. I implemented it just to understand the Knowledge Graph Visual Browser implementation and how it works. The idea was to use the graph condensation method and create a new graph consisting of nodes that represent strongly connected components in the original graph. 
- [Summarization](#summarization) (also [Node aggregation](#node-aggregation)) sums up clustering and coarsening approaches. This approach was preferred by working colleagues with only the remark that it would be useful to add the possibility to restore the details.
- Personally, I did not like the [Sequential layout](#sequential-layout), but still wanted to receive feedbacks from colleagues. They said it is a very user-friendly layout, easy to understand and looks like "Tree of life". So I left it in case I didn't find a better solution.
- The [background hiding](#background-hiding) method has also received good reviews. However, this can only display the area of the graph that the user wants to see and does not make it easier to present the graph as a whole.
- I showed users the [node highlighting](#highlighting-key-nodes-and-edges) example shown in Figure 5. They found the graph too complex, but they liked that the clusters of nodes can be easily seen and that the key nodes are shown larger than the other nodes. 


#TODO Draft
Filtering problem
A common problem when reducing large graphs or networks by filtering techniques is that users do not exactly know what they are looking for and, thus, require tools that assist them in identifying the critical elements.
https://www.yworks.com/pages/interactive-filtering-of-large-diagrams

Centrality 
 Except for degree, the
other two centrality measures, closeness and betweenness,
are prohibitively expensive to compute, and thus impractical
for large networks. On the other hand, although simple to
compute, degree centrality gives limited information since
it is based on a highly local view of the graph around each
node. We need a set of centrality measures that enrich our
understanding of very large networks, and are tractable to
compute
shortest-path or
random walk betweenness [6, 25] have complexity at least
O(n
3
) where n is the number of nodes in a graph
https://www.cs.cmu.edu/~ukang/papers/CentralitySDM2011.pdf









Based on the results of the survey, I convinced myself that the approach described in section "[Node aggregation](#node-aggregation)" is best suited. I presented this approach to my supervisor and two weeks later he told me that he had found a customer who is interested in this approach. The customer was the Charles University and the topic was "Departments and subjects".

After several studies, we can state the following several conceptual criteria:

- An aggregation node must be a cluster of related nodes (for example, nodes of the same class or similar property)
- An aggregation node must somehow show the user which nodes it has inside
- An aggregation node might have separate aggregation nodes internally representing the types of nodes it aggregates (in case it aggregates different types of nodes at the same time, such as universities and subjects) - **not implemented**.
- An aggregation node may display the number of nodes it has inside - **not implemented**.

But there are more questions:
- Based on what criteria to cluster nodes?
- How to name and represent the aggregation node?
- Can cluster have nodes of different equivalence classes (based on similarity)?
- How to represent edges between nodes that represent an aggregation of the original nodes (create only one aggregated edge, i.e. its thickness will show how many edges it combines, or show them all)?
- How to show the internals of an aggregation node?

---

The first draft of the approach is shown in the Figure 6 below.

<p align="center">
    <img src="img/firts_draft_diagram.png" alt="first_draft" title="First draft" width="1000"/><br/>
    <em>Figure 6. First draft. It is a concept representing the main idea used in the final approach. It represents different hierarchies and hierarchical as well as non-hierarchical relationships.</em>
</p>

From this point on, we can introduce the term "[hierarchy](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/user_documentation.md#hierarchical-relationship)" into use and formulate the following main criteria:
- There can be [hierarchical](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/user_documentation.md#hierarchical-relationship) and [non-hierarchical](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/user_documentation.md#non-hierarchical-relationship-) (ordinary, represented by an edge) relationships between nodes
- A parent node may be interpreted as an aggregation of child nodes placed inside
- The [hierarchical expansion](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/user_documentation.md#hierarchical-expansions) (from the list of available expansions) must show the expansion [within (inside)](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/user_documentation.md#hierarchical-relationship) the expanded node
- It should be possible in the configuration to determine if the relationship is hierarchical or non-hierarchical
- A non-hierarchical edge can lead between nodes placed in the different hierarchies, even if one of them or both contain child nodes inside
- Child nodes can be collapsed into parent nodes. In such case, all edges of child nodes are moved to the parent node (more in the [user documentation](user_documentation.md#edge_moving))
- Nodes having the same [hierarchical class](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/user_documentation.md#hierarchical-class) must be place in the same [hierarchical group](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/user_documentation.md#hierarchical-group). Here hierarchical class represents an equivalence class and hierarchical group contains only one equivalence class
- It should be possible to define [visual groups](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/user_documentation.md#visual-group) - big clusters of nodes that might not belong to some hierarchy. Here visual class represents an equivalence class and visual group contains only one equivalence class

We also introduce the map-style zoom used in mapping platforms, so that when you zoom in, you see more detail in terms of nodes, and when you zoom out, you see less detail in terms of nodes. In analogy with maps, we introduce the concept of [hierarchical levels](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/user_documentation.md#hierarchical-level). An example is shown in the Figure 7 below. 

<p align="center">
    <img src="img/map_style_zoom.png" alt="map_style_zoom" title="Map style zoom" width="900"/><br/>
    <em>Figure 7. Map-style zoom (screenshots 1-3). Czechia (google maps, screenshot 1, left), which is placed at the level 0, is an aggregation of cities placed at the level 1 (google maps, screenshot 2, left), and analogously, MFF (screenshot 1, right), which is placed at the level 0, is an aggregation of departments placed at the level 1 (screenshot 2, right). In case there are several different hierarchical groups, the highest abstract level will show the last ancestor of each hierarchy and they will not be grouped or collapsed (screenshots 3, left - google maps, countries and right - departments and subjects).</em>
</p>

The point is not to stick with the "Departments and Subjects" topic only, but to make it abstract and reusable with other topics.

The following section describes the final approach proposed in this paper.

<h2 id="grouping-of-clusters-extension">4. Grouping of clusters</h2>

This part (also available [here](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/Research%20paper%20for%20Knowledge%20Graph%20Browser.docx)) is used as a contribution to [the original article](https://www.sciencedirect.com/science/article/pii/S1570826822000105#b2).

The "Grouping of clusters" algorithm first clusters the nodes into a [cluster](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/user_documentation.md#cluster), and then collapses that cluster into a single group node. The clustering of nodes is determined based on the [hierarchical class](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/user_documentation.md#hierarchical-class), the parent node, the [level of the hierarchy](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/user_documentation.md#hierarchical-level) in which the node resides, and the visual class. Full description of the approach is available [here](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/user_documentation.md#grouping-of-clusters).

The visual configuration is extended with [visual layout constraints](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/technical_documentation.md#visual-layout-constraint) which can be used to restrict the way the knowledge graph is visualized. To support visual layout constraints, we extend the Knowledge Graph Visual Browser ontology with new terms.  

Figure 8 below shows the extension of the ontology as a UML class diagram. 

<p align="center">
    <img src="img/extension_diagram.jpg" alt="extension_diagram" title="Extension diagram" width="800"/><br/>
    <em>Figure 8. Extended Knowledge Graph Visual Browser ontology for defining extended visual configurations.</em>
</p>

In the visual configuration we extend every entity node with additional hierarchical and visual group classes, representing hierarchical and visual groups to which the node belongs, respectively. 

A [visual group](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/user_documentation.md#visual-group) is a cluster of nodes assigned to the same visual group class and placed under the same pseudo-parent node.  

A [hierarchical group](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/user_documentation.md#hierarchical-group) is a cluster of nodes related to each other by parent-child relationships and together forming a hierarchy. In a hierarchy, relationships are not represented as a line, but in such a way that the child node is placed inside the parent node. 

Expansion and preview queries defined in the visual configuration are extended with a new variable `?groupclass` that is bound to the visual class representing either the hierarchical or visual group class. It is common to consider a hierarchical class as a group class as well, because we can interpret a root ancestor node in a hierarchy as a pseudo-parent node. 

The visual knowledge graph 𝒱 is associated with two new functions 𝜎 and 𝜏 that map each entity node to a set of literals called visual group classes and hierarchical group classes, respectively. Entity node is placed under the [visual group](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/technical_documentation.md#visual-group) of class defined by 𝜎. And similarly, entity node is placed in the [hierarchical group](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/technical_documentation.md#hierarchical-group) of class defined by 𝜏. It is also possible that the node is not assigned to a visual group class or a hierarchical group class.  

A visual configuration extended with [visual layout constraints](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/technical_documentation.md#visual-layout-constraint) is bounded to a concrete 𝒢. As for now, the Knowledge Graph Visual browser supports only “[visual group](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/technical_documentation.md#visualgrouplayoutconstraint-class)”, “[hierarchical groups to cluster](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/technical_documentation.md#hierarchicalgroupstoclusterlayoutconstraint-class)”, “[classes to cluster together](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/technical_documentation.md#classestoclustertogetherlayoutconstraint-class)” and “[hierarchical parent-child or child-parent](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/technical_documentation.md#childparentlayoutconstraint-and-parentchildlayoutconstraint-classes)" layout constraints.  

The “[visual group](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/technical_documentation.md#visualgrouplayoutconstraint-class)” visual layout constraint defines the visual class, namely visual group class, that represents a [visual group](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/technical_documentation.md#visual-group). The visual layout constraint is expressed as an instance of the `browser:VisualGroupLayoutConstraint` class. The visual group class is assigned to this class by the `browser:clusteringSelector` property. 

The “[hierarchical groups to cluster](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/technical_documentation.md#hierarchicalgroupstoclusterlayoutconstraint-class)” visual layout constraint defines the visual class, namely hierarchical group class, that represents a [hierarchical group](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/technical_documentation.md#hierarchical-group). All nodes within such a hierarchical group allowed to be clustered and grouped. All other nodes within other hierarchical groups cannot be clustered and grouped. The visual layout constraint is expressed as an instance of the `browser:HierarchicalGroupToClusterLayoutConstraint` class. The hierarchical group class is assigned to this class by the `browser:clusteringSelector` property.  

By default, grouping of clusters algorithm clusters nodes that only have the same visual class. The “[Classes to cluster together](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/technical_documentation.md#classestoclustertogetherlayoutconstraint-class)” visual layout constraint defines a set of visual classes that can be clustered together and then grouped into one group. The visual layout constraint is expressed as an instance of the `browser:ClassesToClusterTogetherLayoutConstraint` class. Each visual class from a set is assigned to this class by the `browser:clusteringSelector` property. 

The “[parent-child](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/technical_documentation.md#childparentlayoutconstraint-and-parentchildlayoutconstraint-classes)” (respectively “[child-parent](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/technical_documentation.md#childparentlayoutconstraint-and-parentchildlayoutconstraint-classes)”) visual layout constraint defines the visual class of the node that plays the role of the parent (respectively child) node and the visual class of the edge, which should be rendered as a hierarchical transition between the parent and the child rather than a line. The visual layout constraint is expressed as an instance of the `browser:ParentChildLayoutConstraint` (respectively `browser:ChildParentLayoutConstraint`) class. The visual class of the parent node is assigned to this class with the `browser:parentNodeSelector` (respectively `browser:childNodeSelector`) property. The visual class of the edge is assigned to the class with the `browser:hierarchicalEdgeSelector` property. 

The stateless [server](https://github.com/linkedpipes/knowledge-graph-browser-backend) is extended with a new request handler that prepares visual layout constraints and sends them to the client, which then uses them to change the way the knowledge graph is visualized. 

<h3>Example</h3>

<p align="center">
    <img src="img/choose_configuration.jpg" alt="choose_configuration" title="Choice of the configuration" width="900"/><br/>
    <em>Figure 9. Choice of the configuration and starting node (screenshots 1-3).</em>
</p>

Let us demonstrate our extension on a visual knowledge graph about Matematicko-fyzikální fakulta. 

At the beginning, the user chooses the Charles Explorer meta-configuration (Figure 9, screenshot 1). The user then chooses the configuration supporting the [visual layout constraints](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/technical_documentation.md#visual-layout-constraint) (Figure 9, screenshot 2). Not all the configurations support visual layout constraints. The user then chooses the starting node from the list of starting nodes, in our case it is Matematicko-fyzikální fakulta (Figure 9, screenshot 3). The client then visualizes the selected starting node (Figure 10 below) and reads layout constraints from the server. The rest of the visualization part is as usual. 

<p align="center">
    <img src="img/starting_node_after_load.png" alt="starting_node_after_load" title="Starting node after load" width="600"/><br/>
    <em>Figure 10. Choice of the configuration and starting node (screenshots 1-3).</em>
</p>

An interesting part happens when the user starts exploring a graph. When the user selects the node on the graph, the client loads the node’s preview. If the node has assigned a [hierarchical class](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/user_documentation.md#hierarchical-class) (in the preview query) representing a [hierarchical group](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/user_documentation.md#hierarchical-group) that is allowed to be clustered and grouped, the client shows this class labeled as hierarchical class in the preview section on the side panel (Figure 11 screenshot 1). 

<p align="center">
    <img src="img/user_interaction.jpg" alt="user_interaction" title="User interaction" width="1000"/><br/>
    <em>Figure 11. A possible user’s scenario of exploring the knowledge graph with Matematicko-fyzikální fakulta in KGBrowser (screenshots 1-3).</em>
</p>

Expansions that expand the node with its neighborhood using hierarchical relationships are [hierarchical expansions](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/user_documentation.md#hierarchical-expansions). There are hierarchical and non-hierarchical expansions (Figure 11, screenshot 2).  

The user selects the Podřazená pracoviště view and clicks the Expand button. The client then performs the expansion action. Since the expansion is hierarchical, the client connects the neighborhood of the node using hierarchical relationships (result is shown on the Figure 11, screenshot 3). Here the Matematicko-fyzikální fakulta node represents a parent node. 

<p align="center">
    <img src="img/(non)hierarchical_expansions.png" alt="hierarchical_expansions" title="(non)Hierarchical expansions" width="900"/><br/>
    <em>Figure 12. A possible user’s scenario of exploring the knowledge graph with Matematicko-fyzikální fakulta in KGBrowser (screenshots 1-2).</em>
</p> 

The user then selects the Matematicka sekce node, clicks Podřazená pracoviště expand button in the list of available views and selects Katedra algebry node (Figure 12, screenshot 1). 

The user then selects the Témata pracoviště view of the Katedra algebry node and clicks the Expand button. Now, the expansion is non-hierarchical, so the client connects the expanded neighborhood with a visual line (edge) (Figure 12, screenshot 2). 

This way, the user opens a group of nodes having tema visual class. The visual configuration specifies nodes assigned to tema visual class as a visual group. Therefore, the client creates the pseudo-parent node representing the tema visual group (Figure 12, screenshot 2, right side). 

<p align="center">
    <img src="img/scaling_options.png" alt="scaling_options" title="Scaling options" width="150"/><br/>
    <em>Figure 13. Scaling options. The user selects “Grouping of clusters” option.</em>
</p>

The user then decides to utilize the “[Grouping of clusters](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/user_documentation.md#grouping-of-clusters)” technique which brings the zoom approach used in mapping platforms to the Knowledge Graph Visual Browser. The user selects the “Grouping of clusters” option in the checkbox (Figure 13) and clicks the “minus” button. The client then groups nodes placed on the [current hierarchical level](https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/user_documentation.md#current-hierarchical-level) (Figure 14, screenshot 1). Then the user decides to go further and clicks multiple times the “minus” button. After a few iterations, the user gets the result shown on Figure 14, screenshot 2. The user clicks the “minus” button once again, and at this time the client collapses the child group into its Matematicka sekce parent node (Figure 14, screenshot 3). After a few more iterations with the “minus” button, the user gets to the state where the graph cannot be collapsed further (Figure 14, screenshot 4). This state represents the highest abstract level of the hierarchy. In this state, the graph shows the least amount of detail possible. 

<p align="center">
    <img src="img/grouping_of_clusters.png" alt="grouping_of_clusters" title="Grouping of clusters" width="900"/><br/>
    <em>Figure 14. A possible user’s scenario of exploring the knowledge graph with Matematicko-fyzikální fakulta in KGBrowser (screenshots 6-9).</em>
</p> 

The user then decides to click the “plus” button multiple times. With each iteration, the user gradually returns to the state from which he started (Figure 12, screenshot 5). 

<h2 id="references">References</h2>

1. ["What is a Knowledge Graph?"](https://www.ibm.com/cloud/learn/knowledge-graph), by IBM Cloud Education, April 12, 2021
2. [Interactive and iterative visual exploration of knowledge graphs based on shareable and reusable visual configurations](https://www.sciencedirect.com/science/article/pii/S1570826822000105#b2) by Martin Nečaský, Štěpán Stenchlák
3. [The 10 rules of great graph design](https://cambridge-intelligence.com/10-rules-great-graph-design/), by Corey Lanum, January 10, 2014
4. [Data Visualization Effectiveness Profile](http://perceptualedge.com/articles/visual_business_intelligence/data_visualization_effectiveness_profile.pdf), by Stephen Few, 2017
5. [Proxy graph: visual quality metrics of big graph sampling](https://www.researchgate.net/publication/314028734_Proxy_Graph_Visual_Quality_Metrics_of_Big_Graph_Sampling), by Quan Hoang Nguyen at al, February 2017
6. [Graph Drawing and Network Visualization](https://link.springer.com/book/10.1007/978-3-030-35802-0#about-this-book), pp 272–286, Eades, P., Nguyen, Q., Hong, SH., 2018.
7. [On the faithfulness of graph visualizations](https://link.springer.com/chapter/10.1007/978-3-642-36763-2_55)
8. [Centrality Algorithms](https://neo4j.com/developer/graph-data-science/centrality-graph-algorithms/)
9. [NetworkX. Summarization](https://networkx.org/documentation/stable/reference/algorithms/summarization.html)
10. [Graph clustering](https://paperswithcode.com/task/graph-clustering)
11. [Coarsening Graphs with Neural Networks](https://karush27.github.io/posts/2012/08/blog-post-24/), October 11, 2021
12. [Sequential layout: the best way to handle tiered data](https://cambridge-intelligence.com/sequential-layout-the-best-way-to-handle-tiered-data/), by Julia Robson, June 15, 2022
13. [Customer behavior analysis with data visualization](https://cambridge-intelligence.com/customer-behavior-analysis/), by Rosy Hunt, August 30, 2022
14. [Pharma data visualization](https://cambridge-intelligence.com/use-cases/pharma/)