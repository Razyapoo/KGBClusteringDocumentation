<h1>Scientific documentation of the research project</h1>
<h3>Topic: Optimization in visualization of knowledge graphs: Grouping of clusters.</h3>

Team members who contribute to the Knowledge Graph Visual Browser:
- Štěpán Stenchlák
  - Basic implementation as a bachelor's thesis
- Jiří Resler
  - [Faceted filtering](#https://github.com/JiriResler/knowledge-graph-browser-frontend)
- Oskar Razyapov
  - [Grouping of clusters](https://github.com/Razyapoo/knowledge-graph-browser-frontend-grouping-of-clusters)

The focus of this research paper is to explore and analyze different existing techniques in the field of graph simplification and use those insights to develop the own prototype. The goal is to make large and complex knowledge graphs well-organized and easily understandable for non-specialists.

Table of content:
- [Introduction](#introduction)
- [Motivation](#motivation)
- [Enhancing the visual perception of large graphs](#approaches)
- [Proposed algorithm: Grouping of clusters](#grouping-of-clusters-extension)
- [References](#references)

<h2 id="introduction">1. Introduction</h2>

A [knowledge graph](https://en.wikipedia.org/wiki/Knowledge_graph), also known as a semantic network, is a representation of a network of real-world entities - objects, events, situations, or concepts - and relationships between them. These entities are usually stored in a graph database and visualized as a graph [^1]. The most common way to represent knowledge graphs is through the [RDF (Resource Description Framework) standard](https://en.wikipedia.org/wiki/Resource_Description_Framework).

However, non-specialists may find it difficult to study knowledge graphs due to the background technicalities involved. In order to solve this problem, the paper “Interactive and iterative visual exploration of knowledge graphs based on shared and reusable visual configurations” [^2] introduces the [Knowledge Graph Visual browser](https://try.kgbrowser.opendata.cz/) - an interactive tool that allows non-specialists to explore knowledge graphs without knowing the underlying technical details.

<h2 id="motivation">2. Motivation</h2>

When working with large graphs, it can be challenging to effectively visualize and interpret the data shown due to the high volume of the information present. The big amount of detail can slow down the processing time and make it difficult to focus on the most important aspects of the graph. 

Main challenges include:

- Limited screen area: Large graph can be difficult to fit onto a single screen, making it hard to see the overall structure or identify patterns
- Overcrowding: When too many nodes or edges are present, the graph can become cluttered and difficult to read
- Link overlap: When there are many edges between nodes, it can lead to link overlap, making it hard to distinguish individual edges and understand the connectivity of the graph
- Lack of context: In big graphs, it can be hard to see the relationships between nodes or understand how they fit onto the overall structure 
- High-Dimensional, complex data: When nodes and edges are associated with many attributes, handling and visualizing them can become challenging
- Scalability: Visualizing large graphs can be computationally expensive
- Dynamic data: Real data are usually dynamic and it may be challenging and expensive to run layout every time a new data appears on the graph area 
  
A good visualization of a large graph should be able to reveal patterns that are of the value to the user. Such a visualization can be particularly useful for presentation purposes, as it help to convey important insights and trends in a clear and concise manner. It is important to choose strategies and techniques that will help to highlight the most relevant and meaningful aspects of the data.

<h2 id="approaches">3. Enhancing the visual perception of large graphs</h2>

There are several approaches that can be taken to improve large graph visualizations. One approach is focused on optimizing the internal methods used in the rendering phase, in order to reduce the time it takes to render the graph without reducing the amount of detail displayed. While this approach is effective, it falls outside the scope of this article.

Another approach is to optimize the layout phase in order to make the graph more readable and understandable for non-specialists. This can involve reducing the amount of detail present in a graph, such as edges or nodes, or using a more efficient layout algorithm. As a result, this approach can help to improve the efficiency of the visualization as well as the speed of graph rendering algorithms. 

The focus of this research work is on the second approach, which aims to enhance the visual perception of large graphs. 

**Graph definition**. A *plain graph* $G$ is a data structure consisting of a finite set of vertices (nodes) $V$ and a set of edges (links) $E$ usually representing a relationship between nodes. In this paper, we support a general graph model, where every node has a set of associated attributes describing the features of an object that a node represents. A graph is often represented by its adjacency matrix $A$, which can be binary, corresponding to whether there exists a relationship between two nodes, or numerical, corresponding to the strength of the relationship. In this article, the adjacency matrix is considered to be binary unless otherwise noted. Additionally, we define a drawing $D$ as the visual representation of the graph $G$.

According to the articles [^3] and [^4], there are several criteria that can help to determine a good level of visual perception in a graph:

- Intuitiveness/easy navigation: The graph should be easy to navigate, with a clear and intuitive layout. 
- Perceptibility: The graph should be easy to understand.
- Simplicity: The graph should be free of clutter. 
- Usefulness: The graph should be clear and useful to the user, and should provide valuable insights and information. 
- Highlighting key details: The graph should highlight the most important and relevant details, and should allow the user to easily focus on these aspects.

In order to improve the level of perception in a graph, it is also useful to consider the following criteria:

1. Nodes connected by an edge should be close to each other: This helps to clearly show the relationships between nodes and can make the graph easier to understand
2. There should be a minimum number of edge intersections or no intersections at all: Avoiding edge intersections can help to reduce clutter and improve the overall readability of the graph
3. A large number of nodes should be divided into chunks or clusters: This can help to organize the nodes and make it easier to understand the structure of the graph 
4. Clusters should contain related nodes: Grouping related nodes together can help to emphasize the relationships between them
5. The area of the graph shown should be small: A smaller, more focused area of the graph can help to improve the readability and clarity of the information
6. The overall graph representation should present (tell) a good story in an understandable way.

Perceptibility and readability are necessary, but not sufficient criteria for the quality of graph visualization. In the paper [^5], the authors introduced another criterion called *faithfulness*, which refers to the ability of the visualization to accurately represent the data being displayed. They found that while reducing the amount of detail shown in a graph can improve the overall perception, it can also reduce the level of accuracy in representing the data.

To address the challenge of visualizing large graphs while maintaining both a good level of perception and faithfulness, recent works [^6] [^7] have proposed the use of "proxy graphs" - smaller representatives of large graphs. Formally, a proxy graph can be defined as follows: given an input graph $G$ and its drawing $D$, a proxy graph is a smaller graph $G'$ with a drawing $D'$, such that if $G'$ is a good approximation of the $G$, then $D'$ is a good visualization of $G$ in which the user can see all the structure of $G$. In other words, the main challenge is to find a proxy graph that balances the competing objectives of perception and faithfulness.  

It is worth mentioning that we can distinguish between *homogeneous* and *heterogeneous* graphs. In a homogeneous graph, all the nodes and edges have the same types and characteristics, while in a heterogeneous graph, they may have different types and characteristics. Knowledge graphs are known representatives of heterogeneous graphs. 

It is also important to note that there are different approaches to simplifying graphs, depending on whether the graph is considered *static* or *dynamic*. Static graph simplification techniques create simplified version of a graph by analyzing its structure at a single point in time, after the graph has already been built. Dynamic techniques, on the other hand, take into account changes in the graph over time. The simplest way to work with dynamic graphs is to treat them as a series of static graph snapshots, which can be analyzed and simplified separately. In this paper, we will focus on working with static snapshots of a dynamic heterogeneous graph.

Furthermore, we can distinguish between *lossy* and *lossless* graph simplification techniques. Lossy techniques are used in situations where it is more important to reduce the complexity of the graph and make it more interpretable, even if it means losing some information from the original graph. In contrast, lossless techniques are used in situations where it is essential to preserve all the information of the original graph, even if it means that the simplified graph may be less interpretable.

In the rest of this section, we will describe and compare several techniques that can be used to find a proxy graph which preserves a good level of perceptibility and faithfulness.

<h3 id="filtering">Filtering</h3>

Filtering is a useful technique for optimizing graph visualization when it is not necessary or practical to display the entire graph. By allowing users to select specific attributes or elements of interest, filtering can help to reduce visual clutter and improve the readability and clarity of the graph. As a result, users can create a small, informative graph that is tailored to their specific needs and interests, making it easier to extract valuable insights and information from data.

The advantage of filtering is that it can be applied at both the attribute level, where nodes are filtered based on the selected attributes, and the structural level, where users can directly select elements of interest on the graph and hide unrelated data. The last approach is illustrated in Figure 1 below.  

<p align="center">
    <img src="img/path_filtering.png" alt="sparsification" title="Sparsification" width="800"/><br/>
    <em>Figure 1. Path filtering. A user selects "2" element (screenshot 1 to the left). The result is shown on the screenshot 2 to the right - algorithm shows only nodes connected to the selected element.</em>
</p>

However, it can be challenging for the ordinary user to identify the area of interest on the graph and select the appropriate elements of interest. Various algorithms can be used to analyze structural patterns in the graph and reveal such elements.  One such class of algorithms are centrality algorithms, which measure the relative importance of nodes within a graph based on structural properties.  

Well-known representatives are[^8]: 

- Eigenvector Centrality: Measures the influence of an element based on the influence of its neighbors, allowing it to identify elements that are connected to other influential elements 
- Closeness Centrality: Measures the distance of an element to all other elements in the graph, indicating its proximity to the rest of the graph
- Betweenness Centrality <span id="betweenness-centrality"/>: Measures the number of shortest paths that pass through an element, indicating its importance as a bridge between other elements 
- Degree Centrality: Measures the number of incoming and outgoing relationships a node has in the graph
  
By using centrality algorithms, users can more easily identify elements of interest and focus on the most important or influential elements in the graph. There are also other techniques such as [PageRank](https://en.wikipedia.org/wiki/PageRank), [LeaderRank](https://www.centiserver.org/centrality/LeaderRank/#:~:text=Definition,the%20need%20of%20frequent%20calibration.), [HITS (Hyperlink-Induced Topic Search)](https://en.wikipedia.org/wiki/HITS_algorithm), etc.

However, centrality algorithms typically consider only one dimension of importance, such as degree, closeness, or betweenness, which can lead to the bias in the results. They often make an assumptions about the original graph, such as that it is connected. They are computationally expensive: except for degree centrality, they have the complexity at least $O(n^3)$ where $n$ is the number of nodes, which makes them usually impractical for large networks[^9].

Filtering can be applied to both heterogeneous and homogeneous graphs. In a homogeneous graph, filtering can be done based on certain properties or attributes of the nodes and edges. For example, filter can be applied to remove nodes with low centrality or low degree. In a heterogeneous graph, on the other hand, filtering can be done based on the type of the node or edge, and also based on certain properties or attributes of the nodes and edges. Additionally, filtering can be lossless and lossy, depending on the goal of the filtered graph. Lossless filtering, for example, only hides redundant nodes, while lossy filtering removes them. 

The main advantage of filtering is that it simplifies the graph according to the user's preferences. However, to apply the filter, users must know the graph in advance and filtering can also lead to loss of information, ambiguity and inaccuracy, so it is important to carefully evaluate the quality of the filtering technique and the filtered graph.

<h3 id="graph-sampling">Graph sampling</h3>

The goal of the graph sampling is to extract a small subset of the large graph that preserves its structure, patterns, and properties. The result of this approach is a graph induced by the selected set.

There are several methods for heterogeneous and homogeneous graph sampling. Techniques for sampling homogeneous graphs include: 

*Random sampling*. Random node/edge sampling is a method for selecting subsets of nodes/edges from a graph, respectively. The method works by randomly selecting a set of nodes/edges from the graph according to a predefined probability distribution, such as uniform or degree-based probabilities. 

One of the main advantages of random sampling is that it is easy to implement and computationally efficient, making it suitable for processing large graphs. In the paper [^10], Spielman and Srivastava show that we can achieve an approximation of the original graph with only $O(\epsilon^{-2}nlogn)$ edges, where $\epsilon$ is a parameter that is used to control the sparsity of the resulting graph. The choice of $\epsilon$ can affect the trade-off between the preservation of the original graph properties and the sparsity of the resulting graph.

However, as the selection of nodes/edges is based on random choice, there is a possibility of missing important features and properties of the original graph. For example, random node sampling method may have difficulty preserving the degree distribution, if there are many high-degree nodes in the sample. Similarly, graphs sampled using random edge sampling are typically very sparsely connected, and thus do not accurately reflect the community structure present in the original graph [^7] [^11].

Another variations of random sampling are Random Node-Edge and Random Edge-Node samplings. They differ in how the subset of nodes and edges is selected: 

- In the *Random Edge-Node* sampling we first uniformly at random pick an edge and then uniformly at random pick a node incident to the edge.

- In the *Random Node-Edge* sampling we first uniformly at random pick a node and then uniformly at random pick an edge incident to the node. 

These techniques aim to reduce the bias to high-degree nodes that is present on the original random edge sampling [^11].

It may be beneficial to use a *Hybrid* approach [^12], where with probability $p$ we perform a step of random edge-node/random node-edge sampling and with probability $1 − p$ we perform a step of random edge sampling.

*Sampling by exploration*. In this family of the sampling techniques we first choose a node uniformly at random and then explore nodes in its neighborhood. These techniques include: 

- Breads-first search (BFS): This technique is inspired by well-known [breadth-first search](https://en.wikipedia.org/wiki/Breadth-first_search) algorithm. It starts at a randomly selected node and then visiting all of its neighbors before moving to the next node.
- Depth-first search (DFS): This technique is inspired by the [depth-first search](https://en.wikipedia.org/wiki/Depth-first_search) algorithm. It starts at random node and then follows a path of edges as deep as possible, then it performs backtracking and moves onto the next path. 
- Random walk [^11]: This technique uniformly at random picks a starting node and then simulate a random walk on the graph. With the probability *c* it revisits a starting node at every step and restarts the walk. The probability is usually set to a low value, such as 0.15, to ensure that the random walk explores a large portion of the graph. However, random walk can get stuck if the starting node belongs to a small, isolated component. In this case, the graph structure in the sample is distorted, as random walker remains inside a subgraph. To solve this problem, some variations of random walk have been proposed, such as *lazy random walk*, where the probability c is set ot zero, or the *random jump*, where the random walker has a small probability *c = 0.15* to jump to a random node in the graph with the goal to explore different parts of the graph. 
- Random path: At each iteration, method randomly chooses two nodes from the original node set, finds the shortest path between them, and adds all previously unselected nodes and edges that are present in that path to the sample graph. The process is repeated until the desired sample size is obtained [^7] [^13]. 

There are many other sampling techniques such as Random PageRank Vertex sampling, Random Degree Vertex, Snowball sampling etc. 

Another family of sampling methods includes methods that can be used to generate a smaller representative sample of a heterogeneous graph. Some representatives are:

- Multi-graph sampling [^14]: A multi-graph is a type of graph that can contain multiple edges between the same pair of nodes. Multi-graph sampling is taking into account the multiple edges between nodes and performing a random walk on the union graph, which consists of union of simple graphs.
- Type‐distribution preserving sampling [^15]: Given a graph $G$ and its proxy graph $G'$, the node type distribution of $G'$ is expected to be the same as in $G$. There are different ways to perform type-distribution preserving sampling, including *Stratified sampling*: where different types are treated as separate strata, and a random sample is drawn from separate strata; *Proportional sampling*: where graph sample preserves a fixed proportion of nodes/edges for each type, and *Scoring-based sampling*: where nodes/edges are assigned scores based on their types, and sample graph is then created from the nodes and edges having the highest scores. 

A homogeneous graph is simpler and easier to analyze and draw than a heterogeneous graph because it contains less information. In general, graph sampling overcomes scalability issues, it helps to identify and isolate subsets of the graph that are important and of interest to the user.

The disadvantage of the graph sampling techniques is that it involves removing information from the graph, that can lead to loss of information. Furthermore, if sampling technique is not done randomly, it can introduce bias into the sample, which can affect the accuracy of the results.

<h3 id="nodes-edges-eliminations">Sparsification. Elimination of redundant edges</h3>

In the best case, a large graph is assumed to be sparse, so that $|E| \leq c|V|$ for some constant *c*. Sparse graphs are often easier to handle than dense graphs and allow to perform graph algorithms faster.

However, in practice, usually holds that $|E| = |V|^{1 + c}$ for a small constant like *c = 0.1*, or even *c = 0.5*. That is, the number of edges grows polynomially faster than the number of nodes. And it might be quiet challenging to visualize and analyze these dense graphs. 

Sparsification is a technique used to reduce the number of edges in a graph while preserving its structural properties. It is an approximation of a given graph by a graph with fewer edges [^16]. In other words, the proxy graph $G'$ is a sparsification of the original graph $G$ if it is a subgraph of $G$, and its edge density is smaller than the edge density of $G$. Some sparsification techniques may keep the original number of nodes, while other techniques reduce the number of nodes as well. 

In the field of graph sparsification, we can distinguish between techniques that are designed for homogeneous and heterogeneous graphs.

*Homogeneous*. Sparsification techniques for homogeneous graphs can be divided into three types: stochastic-based, heuristic-based and importance-based sampling:
- *Stochastic-based* sampling aimed to preserve the overall connectivity structure of the original graph. It is useful for cases when the degree distribution of the graph is more important than specific edges or nodes [^7] [^17]. Some examples of stochastic sparsification techniques for homogeneous graphs include: RE/RV (Random Edge/Vertex), RW (Random Walk), RNN (Random Node Neighbor), or RJ (Random Jump) sampling techniques. 
- *Heuristic-based* sampling selects a subset of edges and nodes using a simple, easily computable rule or an educated guess, without taking into account their relative importance. Examples of this type of methods are Edge and Vertex sampling. 
- *Importance-based* sampling that is aimed to preserve the most important edges and nodes in the graph, rather than a random subset. Its representatives are: Importance sampling: this method uses a measure of importance, such as degree centrality, or betweenness centrality, to assign scores to each edge or node in the graph, and then selects subset of edges and nodes having the highest scores; High-Dimensional Embedding [^18]: This method uses techniques such as t-SNE or multi-dimensional scaling to embed the graph to a low-dimensional space, preserving the most representative and important attributes, nodes and edges of the graph.

*Heterogeneous*. All methods used for homogeneous graphs can be applied on the heterogeneous graphs as well but with small changes.

It is worth to mention spectral sparsification, which is an importance-based sparsification technique used with heterogeneous graphs. This technique preserves spectral properties of the original graph. These properties are characterized by the eigenvalues and eigenvectors of the graph's Laplacian matrix.

A spectral sparsifier is a subgraph of the original graph whose Laplacian quadratic form is approximately the same as the that of the original graph [^19]. Spectral sparsifiers use the information provided by the eigenvalue of the Laplacian matrix as a guide to preserve the spectral properties of a graph and reduce the number of edges. Some examples of spectral sparsifiers are the effective resistances [^10], the normalized Laplacian [^19] and the cut-sparsifier [^20].

The *adjacency matrix* of an *n*-vertex graph $G=(V, E)$ is the $n \times n$ matrix *A*, such that $A_{uv}=1$ if $u,v \in V$, $(u, v) \in E$ and $A_{uv} = 0$ otherwise. The *degree matrix Deg* of $G$ is the diagonal matrix, where a value in the $i^{th}$ diagonal entry corresponds to the degree of node $i$. The *Laplacian* of $G$ is $L=Deg-A$. The *spectrum* of $G$ is the sequence $\lambda_1, \lambda_2, \dots, \lambda_n$ of eigenvalues of $L$, such that $\lambda_i \geq 0$, $i \in \{1, n\}$ and $\lambda_1 = 0$. [^21]. 

The spectrum of a graph is closely related to many structural properties of the graph, including:

- Connectivity: The eigenvalues of the graph's Laplacian matrix can provide information about the connectivity of the graph. The number of connected components of $G$ is largest $i$, such that $\lambda_i = 0$ [^21]. The smallest eigenvalue is always 0 corresponding to the main component. The second smallest eigenvalue is known as *algebraic connectivity of the graph G* [^22], or spectral gap, which gives a notion of the density of the graph. It measures how connected the graph is and how "well-connected" the different components are.
- Clustering: [Spectral clustering](#spectral-clustering) is a technique that relies on the eigenvectors corresponding to the smallest eigenvalues of the Laplacian matrix. These eigenvectors capture the low-dimensional structure of the graph and can be used to find the best way to divide the graph into clusters. Such eigenvalues are also known as Fiedler values [^23].

Suppose that $G$ is a *n*-vertex graph with Laplacian $L$, and $G'$ is its *n*-vertex proxy graph with Laplacian $L'$. If there is an $\epsilon \gt 0$ such that for every $x \in R^n$ holds the following:

$$
\begin{equation}
(1-\epsilon)\frac{x^TL'x}{x^Tx} \leq \frac{x^TLx}{x^Tx} \leq (1+\epsilon)\frac{x^TL'x}{x^Tx},
\end{equation}
$$

then $G'$ is an $\epsilon$-spectral approximation of $G$. 

Using the Courant-Fischer Theorem [^21] with above inequality, it is possible to prove that if a proxy graph $G'$ is an $\epsilon$-spectral approximation of $G$, then the eigenvalues and eigenvectors of $G'$ are close to those of $G$, which means that $G'$ preserves structural properties of the original graph $G$. Also, Spielman and Teng showed that every *n*-vertex graph with a probability $\frac{1}{2}$ has an $\epsilon$-spectral approximation with $O(\frac{1}{\epsilon}nlogn)$ edges, where $\frac{1}{\sqrt{n}} \leq \epsilon \leq 1$.

The most common technique of spectral sparsification is Effective Resistance Sampling [^10]. This method is based on the concept of the effective resistance, which is related to the eigenvectors and eigenvalues of the Laplacian matrix. The effective resistance is the measure of how difficult it is to travel from one node to another through the edges of the graph, or in other words, it is the measure of the resistance to current flow between two nodes in a graph. The calculation of the effective resistance of an edge can be done by using the Kirchhoff's Current Law (KCL) which states that the sum of the current entering the node is equal to the sum of the current leaving the node. Edges with higher effective resistance can be then removed without affecting the eigenvalues and eigenvectors of the Laplacian matrix.

<h3 id="clustering">Clustering</h3>

Clustering techniques are aimed to identify natural groupings or communities of nodes that have similar properties such as structural characteristics or attributes. 

Clustering techniques use the distance/similarity measures, that can be based on:

- Structural properties: These measures take into account the topological structure of the graph, such as the number of common neighbors, the shortest-path length, or similarity of the node degrees. The goal is to minimize the cross-cluster edges and focus on the connectivity patterns of the nodes, i.e., to group together nodes that are more densely connected to one another than to the rest of the graph [^24]. Some representatives are: Common neighbors, which counts the number of neighbors that two nodes have in common; [Jaccard coefficient](https://en.wikipedia.org/wiki/Jaccard_index), which is defined as the ratio of common neighbors of two nodes to the number of their total neighbors; or [Betweenness centrality](#betweenness-centrality), which is defined as the number of shortest paths between any two nodes that pass through a given node, because nodes with high betweenness similarity are more likely to be in the same community.
- Attributes: These measures use attributes of the nodes, such as node labels or features, to measure their similarity. Some representatives are: [Euclidean distance](https://en.wikipedia.org/wiki/Euclidean_distance), which measures the distance between two nodes based on their attributes as the squared root of the sum of the squared differences between the attributes of two nodes; [Manhattan distance](https://en.wikipedia.org/wiki/Taxicab_geometry), which is similar to Euclidean distance, but the distance between two nodes is calculated as the sum of the absolute differences between the attributes of two nodes; and other measures like [Minkowski Distance](https://en.wikipedia.org/wiki/Minkowski_distance), [Jaccard Similarity](https://en.wikipedia.org/wiki/Jaccard_index), [Levenshtein distance](https://en.wikipedia.org/wiki/Levenshtein_distance), etc.
- Hybrid approach: These measures combine structural and attribute similarity to take into account both the topological structure of the graph and the attributes of the nodes. They are based on the idea that similar nodes should have similar connectivity patterns in a graph.

In general, we can distinguish between two types of clustering approaches:

- *Agglomerative clustering (AC)*. This is a bottom-up approach which starts with each node as it own cluster, and then iteratively merges the most similar clusters until a stopping criterion is met. The similarity between clusters can be defined using different similarity measures, such as single linkage, complete linkage, average linkage, or Ward linkage [^42]. The specific choice of the linkage measure affects both the clustering quality and the computational complexity of the agglomerative clustering algorithm. For example, single linkage tends to create elongated clusters, when some nodes are close to each other, and the rest of the points are far away, even if all of them at the end will belong to the same cluster. On the other hand, complete linkage tends to create more compact clusters, where nodes that are far away from each other will be in the different clusters. Ward linkage tries to minimize the variance of the distances between points in different clusters.

- *Divisive clustering (DC)*. This is a top-down approach which utilizes a similarity measure to iteratively split a cluster and create more fine-grained clusters by removing edges connecting nodes with low similarity. Divisive clustering techniques tend to increase the cohesiveness of a community. 

In a homogeneous graph, methods that focus on the connectivity pattern of the nodes, such as *community detection* or *modularity optimization*, tend to be more efficient, while in a heterogeneous graph it is better to use methods that focus on the attributes of the nodes, such as k-means, k-medoids or hierarchical agglomerative clustering. 

<em id="community-detection">Community detection.</em> A community refers to a set of entities that are more closely connected to one another in comparison to other entities within graph. The level of connection or proximity between the entities within a community can be quantified using similarity or distance measures [^25]. 

A comprehensive survey of community detection has been done in many researches such as [^26] [^27] [^28] [^29]:

- Girvan and Newman [^30] [^31] proposed a divisive method based on [betweenness centrality](#betweenness-centrality), which focuses on those edges that are least central. If a network or graph contains communities that are only loosely connected by a few inter-group edges, then all shortest paths between different communities must go along one of these edges. Such edges then have high betweenness centrality. By removing these edges iteratively, we separate groups from one another and so reveal the underlying community structure of the graph [^30]. This algorithm is, however, sensitive to the resolution parameter, which is a measure of the number of groups of nodes that are not connected to each other. The worst-case time complexity of the edge betweenness algorithm is $O(m^2n)$ and is $O(n^3)$ for sparse graphs, where $m$ denotes the number of edges and $n$ is the number of vertices. Despite the complexity, the algorithm can handle large and complex graphs, identify overlapping communities and it is robust to outliers. Many recent works uses this approach as a base, such as [^32] [^33] [^34] [^35] [^36].

- Another popular algorithm used for community detection is a *Louvain algorithm* proposed in [^37] by Blondel et al, which is based on a greedy optimization approach. The algorithm works by iteratively optimizing a quality function called modularity, which is a measure of the density of edges within communities as compared to edges between communities. The modularity value ranges between -0.5 and 1, with higher values indicating a better community structure. The algorithm starts by assigning each node to its own community and then searches for the highest modularity gain of moving a node from one community to another. Then the algorithm updates the community assignments of the nodes based on the best possible move found in the first step. These last two steps are continuously repeated until no further improvement in the modularity value can be obtained [^37]. 

- *SCAN (Structural Clustering Algorithm for Networks)* [^38]. This algorithm was proposed by Xiaowei Xu et al and is aimed to identify dense subgraphs, hubs (vertices that bridge clusters) and outliers (vertices that are marginally connected to clusters) within a graph by using the structure and the connectivity of the vertices as clustering criteria. For example, outliers have a little or no impact, and may be isolated as noise in the data. The SCAN algorithm starts by identifying dense regions in the graph, and then it recursively expands these regions by adding the neighboring nodes that satisfy a density threshold. The algorithm stops when no more nodes can be added to the clusters. Time complexity is $O(m)$, where $m$ is the number of edges in a graph, which is pretty good complexity in comparison to other community detection algorithms. 

- <em id="scc">[Strongly connected components](https://en.wikipedia.org/wiki/Strongly_connected_component) (SCC)</em>. The SCC algorithms finds the connected components in a directed graph, where each component is a group of nodes that are strongly connected to each other, i.e., there is a directed path between any two nodes in the component in both directions. The algorithm can be used to identify communities in directed graph, where a community is defined as a group of strongly connected nodes, which are "strongly" connected among themselves and "lightly" connected with the rest of the graph.

<em id="hierarchical-clustering">Hierarchical clustering</em>. Communities often have a hierarchical structure, where communities at lower levels of the hierarchy are more cohesive, and nodes within these communities are located close to each other. Small and cohesive communities are nested into larger and less cohesive communities in hierarchical manner [^39].

This method builds a hierarchy of clusters by successively merging or splitting existing clusters. Such hierarchy is often represented as a tree-like structure called [dendogram](https://en.wikipedia.org/wiki/Dendrogram), which illustrates the arrangement of the clusters produced by the corresponding analyses [^40] [^41]. 

- *Hierarchical agglomerative clustering (HAC)* is a type of clustering algorithm that builds a hierarchy of clusters by iteratively merging smaller clusters into larger ones. The difference between AC and HAC is that HAC creates a hierarchy of clusters, where each cluster is a subset of the previous one and the final cluster represents the whole data set whereas agglomerative filtering ends with a clustering that is a flat partition of the data set, and it does not create hierarchy of clusters. 

- *Divisive hierarchical clustering*. In general, divisive clustering algorithms start with all nodes placed in one cluster, then they identify the criterion for splitting into two or more smaller clusters, and then they assign each node to one of the new clusters based on the criterion used for splitting. Last two steps are repeated until a stopping criterion is met, which can be a fixed number of clusters or a certain threshold for the similarity [^43]. These methods build a hierarchy of clusters, with each cluster at a particular level of the hierarchy being broken down into smaller clusters at the next level.

<em id="partitional-clustering">Partitional clustering</em>. Common node clustering techniques group similar nodes together, resulting in communities that can overlap, meaning that a single node can belong to multiple communities. However, there is also a variation of clustering techniques called partitional clustering, which divides a graph into non-overlapping subsets of nodes, where a node can only belong to one community. The goal of this type of clustering is to iteratively find the best partition of set of nodes of a graph into $k$ clusters. Some representatives are: 

- [K-Means](https://en.wikipedia.org/wiki/K-means_clustering): This algorithm takes into account different characteristics and properties of a set of nodes and aims to partition that set into $k$ clusters, such that it minimizes within-cluster variance. Each cluster is associated with its own centroid that represents a mean of a cluster. The goal is to iteratively minimize the sum of distances between the nodes and the centroid of the cluster they belong to. First of all, the graph is represented as an attribute matrix, where each row represents a node and each column represents an attribute of the node, such as its degree, or community membership. Then, the algorithm is applied to the attribute matrix. At the beginning, centroids are chosen randomly from the attribute matrix, or computed by an aggregation function from attributes of several randomly chosen nodes. The iterative part begins when the algorithm assigns each node to the closest centroid based on the attribute matrix, and then updates the centroids by moving them to the mean of the attribute values of the nodes in the cluster. The last two steps are repeated until the centroids no longer changes or a stopping criterion is met. The algorithm can be sensitive to the initial conditions and can get stuck in a local minimum. This can be solved by running the algorithm multiple times with different initial centroids and choosing the best solution. It is also sensitive to the scale of the data, so it is recommended to normalize the data before applying the algorithm.  
- [K-Medoids](https://en.wikipedia.org/wiki/K-medoids): This algorithm is a variation of the K-Means with a slight difference. Instead of centroids or means, it uses medoids, which are real nodes from a set of nodes of a graph. In the iteration step, instead of computing the mean of a cluster, it selects a new node as a medoid such that it minimizes the sum of distances between the nodes and the medoid within a cluster. This algorithm has an advantage that it is more robust to the outliers and noise in the data, and it is less sensitive to the scale of the data. However, it is computationally more expensive than K-Means and it is sensitive to the initial conditions. This can be solved by running the algorithm multiple times with different initial medoids and choosing the best solution.   

<em id="spectral-clustering">Spectral clustering</em>. Spectral clustering can be used to detect clusters of nodes that are densely connected internally and have fewer connections with the rest of the graph, and based on the eigenvalues of the Laplacian matrix of the graph, which encodes the connectivity information of the graph. It can be applied to both homogeneous and heterogeneous graphs. In comparison to K-Means and K-Medoids, spectral clustering is useful for detecting clusters in graphs that have a non-convex structure, overlapping clusters, or where a number of clusters is not known in advance and may have different sizes. It is also less sensitive to initial settings. For homogeneous graphs, the algorithm assigns $n$ node to $k$ clusters as follows [^44]:

1. Given the adjacency matrix $A$ and degree matrix $D$, calculate Laplacian matrix as $L=D^{-1/2}AD^{-1/2}$
2. Find $k$ orthonormal eigenvectors $X_1, \dots, X_k$ corresponding to $k$ largest in absolute value eigenvalues of the Laplacian matrix $L$ 
3. Perform K-Means clustering on matrix $X=[X_1,\dots,X_k]$
4. assign $i^{th}$ node to $k^{th}$ cluster if the $i^{th}$ raw was assigned to $k^{th}$ cluster in step 3.

For a $t$-typed heterogeneous graph, it is possible to find $tk$ clusters by performing similar algorithm as for homogeneous graphs, but considering $t$ simultaneous, separate K-Means clustering procedures, each for a specific type. These clusters are then mapped back to the original graph to assign the nodes to clusters. 

<h3 id="summarization">Summarization</h3>

Graph summarization transforms graphs into more compact summary representations while preserving structural patterns and properties. This is a broad term that encompasses different techniques that can be used to create a simplified graph, including clustering, graph sparsification, sampling, or a combination of them. One example of a summary graph that is commonly used is a condensed graph, also known as a directed acyclic graph (DAG), which is the result of applying a strongly [connected component (SCC) algorithm](#scc). 

Graph summarization methods can be categorized as follows [^45]: 

- *Static or Dynamic*: Despite the prevalence of large dynamic graphs, only a small number of researches are aimed at their efficient summarization like [^46] or [^47]. Dynamic graph summarization techniques include online and incremental algorithms, which continuously update the summary of the graph in response to its changes over time. An example of dynamic technique is *Sliding window*, which involves dividing the dynamic graph into a series of fixed-size windows, and summarizing the graph within each window. Some previously stated techniques such as Random Walks, K-Means, hierarchical clustering, or community detection are also applicable to dynamic graphs.

- *Homogeneous or Heterogeneous*: Homogeneous summarization methods assume that an input graph is homogeneous. These methods create summary of the graph based on a single criterion, such as node degree or edge weight. On the other hand, heterogeneous summarization methods assume that an input graph is heterogeneous. These methods create summary based on the multiple criteria and take into account the different types and properties of nodes and edges.

The summarization result can vary as follows:

- *Summary type*: It can be (i) a *supergraph*, consisting of supernodes and/or original nodes, and superedges and/or original edges; (ii) a *list* of nodes and edges that have the most significant influence on the overall structure of the graph. The shape of the summary can be (a) *flat*, with nodes simply grouped into supernodes, and (b) *hierarchical*, with multiple levels of abstraction.
- *Overlapping and non-overlapping nodes*: Each node can belong only to one summary element such as supernode, or it can belong to multiple supernodes at the same time.

The use of the supernodes is a popular approach in graph summarization to maintain a high level of information preservation. A supernode is often represented as a collapsed [compound node](https://cambridge-intelligence.com/combos/), which is used to represent a cluster of nodes or a subgraph within a single visual element. The main advantage of using supernodes is their ability to expand and collapse, allowing to show individual nodes inside the cluster when expanded, and a high-level overview of the entire graph when collapsed.

<em id="grouping-and-aggregation-summarization">Grouping/aggregation based techniques</em>. These methods are aimed to identify and combine similar nodes/edges into a supernodes/superedges based on their structural and attribute similarities. In the case of grouping-based techniques, the resulting supernode is typically given by the most important node in a cluster. This is done, for example, by identifying the node with the highest centrality, such as degree centrality or betweenness centrality. On the other hand, in the case of aggregation, the resulting supernode is created by aggregating the attributes of the nodes in a cluster using an aggregation function. This can be done, for example, by taking average, sum, or median of the attributes of the nodes in a cluster. In this case, the resulting supernode is not necessarily the most important node in the cluster.  

We distinguish two main types of the grouping-based graph summarization techniques: (i) *node-grouping* and (ii) *edge-grouping*.

- <em id="node-grouping">Node-grouping methods</em>. This technique is aimed to group similar nodes in a graph. This can be done by identifying clusters or communities of nodes in the graph that are more densely connected to each other than to the rest of the graph and then combining them into one supernode. Each such community is considered to be a summary of a group (cluster) of nodes in the original graph. 

- <em id="edge-grouping">Edge-grouping methods</em>. Unlike node-grouping methods that group similar nodes together into supernodes, edge-grouping methods aggregate edges into edge-compressors or virtual nodes to reduce the number of edges in a graph in either lossless or lossy manner:

  - Lossless edge-grouping techniques often use an edge-compressor function that compresses a set of similar edges into a single, representative edge. The compressed edges can be decompressed to exactly reconstruct the original graph.

  - Lossy edge-grouping techniques often use virtual nodes, which serve as a representation of a group of edges. This approach allows to use less storage space for the summary graph. However, it also involves some loss of information as a trade-off for making the graph more readable.

*Dedensification*. Usually large graphs have lots of high-degree nodes, which can be useful to compress. Maccioni and Abadi proposed a lossless compression technique, called dedensification [^48], which summarizes the multiple connections of the same kind to high-degree nodes by replacing them with virtual nodes. In other words, this technique separates high-degree nodes from the incoming connections they have by means of intermediate nodes that summarize those connections. An example is shown on Figure 2 below.

<p align="center">
    <img src="img/dedensification.png" alt="dedensification" title="Dedensification" width="700"/><br/>
    <em>Figure 2. Dedensification. The original graph is shown on the screenshot 1 to the left. The resulting graph is shown on the screenshot 2 to the right.</em>
</p>

The main advantage of graph summarization is that it produces a summary graph that is again graph which can be further compressed. One of the main challenges of graph summarization, is to create a summary graph that preserves important information present in the original graph, and from which we can restore the original graph. By grouping similar nodes together, we can reduce clutter and make it easier to understand the overall structure of the graph. Additionally, the use of supernodes allows to navigate and explore the graph at different levels of detail, while preserving the context and the relationships among the nodes.

<h3 id="coarsening">Coarsening</h3>

This technique replaces the original graph by one which has fewer nodes, but whose structure and characteristics are similar to those of the original graph. The nodes with similar properties are grouped into a clusters, called supernodes [^49]. It is very similar to the graph summarization, however, the main goal of the coarsening it to create a smaller representation of the original graph for the purpose of improving the efficiency and scalability of graph-based computations, while graph summarization techniques are aimed, in turn, to make a graph more simpler to understand, and preserve the important information of the graph structure.  

Coarsening is often used as a preprocessing step for multi-level algorithms [^50] [^51], which iteratively compress a graph into smaller, more manageable graphs in a multi-level manner. The multi-level algorithm is an iterative process that repeatedly applies a specific operation, such as graph partitioning or clustering, on current-level graph. Graph partitioning is a computationally expensive task, especially for large graphs, so coarsening is often used as a preprocessing step to reduce the size of the graph and make it more manageable for the partitioning algorithm. The process typically starts by replacing the original graph $G$ with a coarse approximation $G_c$, which is then partitioned into smaller subgraphs, called partitions. The partition of $G_c$ then used to create a rough partition of $G$, by mapping the supernodes of $G_c$ back to the original nodes of $G$ [^52] [^53]. In the case if partition is still big, the whole process can be applied recursively. 

---

<h3 id="visual-tricks">Visual tricks</h3>

Visual tricks are techniques used to improve the readability and perceptibility of large graphs. Some examples of visual tricks include:

- <em id="highlighting-key-nodes-and-edges">Highlighting key nodes and edges</em>. Highlighting key nodes or areas of the graph can be a very useful technique for drawing attention to the most important or relevant parts of the graph, and making them more easily identifiable. This can be done using different visual cues, such as color, size, shape, or transparency. Furthermore, highlighting can be used in conjunction with filtering, which can be used to show only those nodes that are of the interest to the user and hide the rest of the graph, or make it less visible.

- <em id="layout-combination">Combination of different layout algorithms</em>. By using different layout algorithms for different parts of the graph it is possible to improve its level of perceptibility and readability. This approach can be used to emphasize and reveal different features of the graph.

    Additionally, it may be useful to change the layout depending on the zoom level. This can help to adjust the level of detail and the level of abstraction of the graph, depending on the user's needs and preferences. For example, when the graph is zoomed out, it may be useful to use layout that emphasizes the overall structure of the graph, and when the graph is zoomed in, it may be useful to use layout that emphasizes the details of the graph.

- <em id="zooming-and-panning">Graph zooming and panning</em>. The zooming feature allows users to change the magnification of the graph, while the panning feature allows users to move around the graph. They are both used to focus on a specific area of interest.

- <em id="tooltips">Tooltips</em>. Tooltips are small boxes that appear when the user hovers over the element or clicks on a specific element in a graph. They are used to provide additional information or context about the element. 

<h2> 4. Proposed algorithm: Grouping of clusters</h2>

After conducting a survey of different graph simplification techniques and evaluating the benefits they provide, it may be determined that using a combination of these techniques can result in more effective way to simplify the graph, and help to address specific challenges that may arise during the simplification process. 

Our algorithm specifically focuses on the use of compound nodes, because they provide many benefits and improvements to the large knowledge graph visualization process, such as:

- Improved reasoning and query capabilities: Compound nodes can serve as clusters of nodes, which can help to reduce the size of a knowledge graph by grouping together related nodes, making the graph more manageable and easier to work with, while also enabling more powerful reasoning and querying capabilities. For example, instead of traversing a graph and collecting elements as an answer to the query, one can simply return the compound node having all necessary elements inside.
- High level of information preservations: Compound nodes can be expanded and collapsed as needed, making it easier to restore the original graph.
- Improved perceptibility and readability: Compound nodes can make a knowledge graph more understandable for humans, making it easier to understand the relationships and connections between different entities, while also preserving a high level of faithfulness of the graph. 

Important remark: compound nodes are usually represented as additional visual elements containing inside a cluster of nodes. Our proposed algorithm combines the use of compound nodes with the idea used in graph sparsification, specifically edge reduction. In many large heterogeneous networks, there are relationships that can be interpreted as hierarchical. For example, parents have children, or cities are part of states. Classifications in social and biological fields are also based on hierarchical relationships, for example classification in animal kingdom. 

We propose a lossless process of edge reduction that aims to efficiently and accurately represent hierarchical relationships in knowledge graphs by eliminating edges that represent these relationships and replacing them with compound nodes. Specifically, the parent node in a hierarchy is represented as a larger node, while child nodes are represented as smaller nodes inside the parent compound node. This approach has the benefit of reducing the size of the graph while improving the reasoning and querying capabilities of the graph. The concept of using hierarchical compound nodes can be applied recursively, which means that a compound node can contain other compound nodes within it. This allows for multi-level representation of hierarchies, and further reduction of the graph while preserving its level of faithfulness. We call this multi-level representation *hierarchical group*. We then differentiate between hierarchical and non-hierarchical relationships in the graph, where hierarchical relationships have a parent-child structure and non-hierarchical relationships are represented by edges. Similarly, we differentiate between hierarchical expansions, which will expand a node's neighborhood in a parent-child structure, and non-hierarchical expansions, which will expand the neighborhood connected to the expanded node by edges. The type of expansion is set by technician in the visual configuration file.

We represent the hierarchy in an efficient manner, including the ability to expand and collapse the hierarchy and handle multiple hierarchies. We also allow for non-hierarchical relationships between nodes placed on different levels of the hierarchy or placed in different hierarchical groups.

However, not all nodes in a knowledge graph may belong to some hierarchy. In this case, it is still useful to cluster similar nodes together and improve manageability of the graph. To accomplish this, we can use compound nodes as a visual element encapsulating a cluster of similar nodes. In this way, compound nodes further improve the overall manageability of the graph, regardless of the type of relationships present between nodes. We call these compound nodes *visual groups*, as they serve as a visual representation of a cluster of related nodes.

Our proposed algorithm also incorporates the use of supernodes and superedges, which are commonly used in the node-grouping and edge-grouping approaches of graph summarization. We also use the idea of edge-compressor, which is an edge that is used to represent a compressed set of edges. As we are working with heterogeneous graphs, it is important to note that a compressed set of edges must consist of edges of the same type. This implies that in the resulting graph, between two supernodes, there would be a set of superedges, each representing a set of edges of the same type in the original graph.

The combination of compound nodes and supernodes/superedges provides a powerful tool for simplifying large and complex heterogeneous graphs by taking into account the hierarchical structure of relationships between entities and compressing nodes and edges. By grouping related nodes together and reducing the number of edges, our algorithm makes the graph more manageable to work with while also preserving the original information. 

There are several questions that need to be considered:

- Which nodes to cluster: In some cases, it may be clear that only nodes of a certain type should be clustered together (e.g. all species in an animal family), while in other cases it may be less clear. And thus, it is important to consider if nodes of different types can also be clustered together. 
    
    By default, our algorithm clusters similar nodes which have the same type. However, we also provide the flexibility to cluster nodes of different types together. The specific types of such nodes are defined in the visual configuration file by technician.

- How to cluster: Once it has been determined which nodes to cluster together, next question to consider is how to cluster them. There are several clustering techniques that can be used here, such as "Community detection" together with "Jaccard similarity" measure to cluster nodes that have a certain number of neighbors in common, "K-Means" together with "Euclidean distance" measure to cluster nodes that are placed close to each other, "Agglomerative clustering" together with "Jaccard similarity" measure to cluster nodes that have a certain number of attribute values in common, or other clustering techniques. The best technique to use depends on the specific attributes of the nodes and the desired outcome.

    Our approach for clustering nodes in the graph is to take advantage of the layout algorithm used for graph visualization, which is based on a force-directed physics simulation. This simulation forces attraction between connected nodes, causing them to be placed close to each other. Based on this, it can be concluded that nodes that have common neighbors are not only structurally similar but also placed close to each other, making it sensible to cluster them together. Therefore, we rely on this approach and cluster nodes based on their position on the graph area and use "K-Medoids" partitional clustering, such that no two clusters share the same elements.

- How to represent supernodes: After a cluster of nodes has been collapsed into a supernode, it is important to consider how to represent this supernode in the graph. For example, supernode can be represented as a simple static node, or it can be represented as a compound node that can be collapsed and expanded as needed. 

    We do not collapse child nodes into the parent node in a one step, but instead, we first group a cluster of nodes into a single node group, and then iteratively group other nodes and group nodes further until only one group node remains as a single child in the compound node.
    
    There are several ways how to represent group nodes and supernodes, such as using different shapes and colors, or setting a specific label. In our approach, a group node is represented as a single node with a predefined shape to easily differentiate it from other nodes in the graph. Additionally, the group node is named based on the most frequent type of nodes appearing within that group. 
    
    Supernodes, which have collapsed nodes within, are represented as collapsed compound nodes. We indicate that a supernode has collapsed child nodes by a special "plus" icon which is placed within the graph element.

- How to represent superedges: For example, superedge can be represented as a single edge that represents a compressed set of edges that can be collapsed and expanded as needed. Additionally, we can use an edge thickness, which shows how many edges a superedge aggregates.
    
    We represent a superedge as a compressed set of edges of the same type, and therefore, we do not make it expandable, as it would not add much value and would only increase the overall complexity.

    When child nodes are collapsed into a parent node, we move all their non-hierarchical relationships represented by edges or superedges to the parent node.

- How to represent supernode/superedge attributes: The resulting attributes can be aggregated, or each supernode/superedge may have its own attributes. 

    In our approach, supernodes represent parent nodes with child nodes collapsed. Therefore they have their own attributes. However, we do not assign any attribute to groups and visual groups.

- How to represent the content of groups, supernodes/superedges, and visual groups: It is important to provide to the user content of a collapsed node or edge in an understandable manner. For example, it could be represented by providing a summary of the information contained within collapsed node, such as the total number of nodes inside. This can help users understand the contents of the supernodes/superedges and make it easier to navigate the graph.   

    We display the number of nodes within the group along with the name of the group, and use a tooltip to show the list of nodes within the group. Additionally, we provide the detail panel that shows the list of nodes within the group.

    Similarly, for each supernode, we provide a list of collapsed child nodes in the detail panel. This allows users to understand the content of the supernode and how it relates to the original graph.   

- How to handle changes in a graph: As the graph evolves over time, it is important to consider how to handle changes to the graph and ensure that the compound nodes, supernodes and hierarchical groups continue to accurately represent the original story that the graph represents.

    Our algorithm is designed to handle changes in the graph over time, which ensures that the overall structure of the graph is accurately preserved. We provide support for dynamic updates of groups and supernodes, which allows to reflect the changes of the original graph in its simplified version. This enables users to interact and manipulate the graph in real-time.
  
- How to handle the complexity of the approached algorithm: It is important to consider the complexity of the algorithm and ensure that it is not too computationally expensive to run on large graphs.

    Our approach provides a more compact representation of the graph, without incurring significant computational costs. It aims to change the way the graph is visualized, rather than applying a new layout to the already rendered graph. The idea behind this is that other algorithms perform more efficiently on compact summary graphs, rather than on large and complex networks. 

- How to handle user interaction: It is important to provide users with an intuitive way to interact with the graph.

    Our approach supports interactive user experience by providing various options for manipulating the nodes and edges in the graph. Users can easily pan and zoom the graph, as well as perform basic update operations on nodes. Additionally, we offer different variations of zooming, such as common zoom-in/zoom-out, zoom-to-fit, or changing the amount of detail displayed (more details below), that allow users to easily navigate and explore the graph.   

We also propose a group compact mode, which allows to uncover the internals of groups without cluttering the graph. It is an easy way to view the nodes within a group and explore inner groups. However, as follows from the name, the mode can only be activated on group nodes. Once it is activated, it allows and facilitates the recursive exploration of the nodes inside the group by allowing for navigation through inner groups. However, it is important to note that the group compact mode is a view mode, and once it is turned off, the state when the mode was activated will be restored. In general, it is useful tool for exploring and understanding large and complex networks, as it allows users to easily drill down into specific groups and see the nodes within them in a more organized and structured way. 

<h3>Map-style zoom</h3>

Map-style zoom is a type of zooming that is often used in map-based applications, such as Google Maps or Maps.cz. It refers to the ability to change the magnification together with the level of detail shown on the map, typically by using a zoom in/out features, such as the zoom buttons or scroll wheel on the mouse. This allows the user to zoom in to see more detail, and zoom out to see a broader overview of the map. This type of zooming is different from the traditional zooming, which only changes the magnification or size of an image; instead it changes magnification as well as the amount of detail. Map-style zooming also refers to the ability to pan and move around the map, which can be useful for navigating large and complex networks.

Supernodes/superedges and groups proposed in our approach are closely related to map-style zoom in that they both allow for changing the amount of detail displayed in the graph. As map-style zooming consists of two main phases: change of the image magnification and change of the detail displayed, we can utilize the traditional zooming to change the magnification along with compound nodes, supernodes/superedges and groups to change the amount of detail displayed. This helps us to implement a multi-level hierarchy, where each level shows a different amount of detail of the graph.

We propose two different types of map-style zooming: global and local. Global zooming allows for zooming in or our on the entire graph, while local zooming allows for zooming in or out only on specific area of the graph. Additionally, we provide the option to use only one phase of map-style zooming, such as only changing the magnification or only changing the level of detail.

<h3>Local zooming</h3>

Global grouping of clusters change the amount of the detail displayed on the entire graph at once, which can make it difficult to understand the relationships and patterns within specific subsets of the graph. In some cases it may be beneficial to use grouping of clusters technique only locally, which will allow to work only with the area of the graph in which we are interested while still maintaining an overall understanding of the whole graph.

Local grouping allows to zoom in and out of specific regions of the graph, giving more control over the level of detail displayed and adjust the visualization to specific needs. Another advantage of local grouping over global grouping is that it can improve the performance of graph rendering algorithms by reducing the amount of data that needs to be changed in a zooming step.

There are several methods for applying local zooming in graph visualization. Some examples include:

*Zoom context*. This type of zooming allows to focus on a specific area of the graph while providing additional context and information about the surrounding area. This can be achieved by using different local zooming techniques such as *Fish eye* view or *Zoom lens* view. The Fish eye view is a non-linear technique that magnifies the area of interest while distorting/compressing the surrounding area to keep it visible. This allows to see the big picture while focusing on a specific area. On the other hand, the Zoom lens view allows to magnify a specific area of the graph while keeping the rest of the graph at standard scale without distorting. This allows to focus on a specific area while still being able to see the context of the entire graph. These techniques are used to magnify a specific area on the image, but not the amount of detail displayed. Fish eye or zoom lens can be adjusted to mouse pointer, such that the user can magnify the area where the mouse pointer is located. 

*Zoom brushing*. This technique allows users to magnify a specific area of a graph by drawing a rectangular box around the desired area. It is often used in conjunction with panning, which allows users to quickly and easily zoom a specific areas of a graph without having to manually zoom in or out using a mouse or keyboard. Additionally, because the zoom area is limited to the area enclosed by the brush, it can help users avoid getting lost in the details of a large and complex networks. However, one of the main disadvantages of zoom brushing is that it can be difficult to select a specific area of interest with precision, particularly when working with small regions of the graph.

*Multi-scale zooming*. This technique enhances the level of detail displayed in a specific area of the graph while keeping the surrounding area at lower level of detail. This allows the user to focus on specific parts of the graph while still maintaining an overall understanding of the graph's structure. The nodes on which multi-scale zooming must be applied can be selected in several ways: by explicitly clicking on a node with a mouse button, by hovering over a node with a mouse pointer, or buy applying a filter to select a specific nodes. Additionally, the core idea of zoom brushing can be achieved by selecting multiple objects using a rectangular box around the desired area and applying multi-scale zooming on them.  

Multi-scale is a powerful tool that allows to explore hierarchical relationships within the graph. The ability to select multiple nodes and apply multi-scale zooming on them can also make it easier to compare different parts of the graph and to identify relationships between different clusters. In our extension, the local grouping of clusters is an implementation of multi-scale zooming. It uses the same idea used in the global grouping of clusters, but the only difference is that it is applied locally on selected nodes.

It is also possible to combine local and global options of cluster grouping. This means that first, the local version can be applied to a specific set of selected nodes, and then the global version can be applied to the entire graph when no nodes are selected.

<h2 id="summary">Summary</h2>

The Grouping of clusters extension is a powerful tool for working with large and complex networks, providing a range of features that make it easy to understand and manipulate the data. It combines different graph simplification approaches to achieve better simplification result and improve the user's experience of working with the graph. It allows users to group clusters of similar nodes, interact with them in a manageable and intuitive way, and utilize the concept of multi-level hierarchy. The extension supports local and global versions of map-style zooming, which allow the users to easily adjust the necessary level of detail displayed and focus only on the data of interest. Additionally, the extension provides a group compact mode which allows the user to uncover insights of groups without cluttering the graph.

<h2 id="references">References</h2>

[^1]: ["What is a Knowledge Graph?"](https://www.ibm.com/cloud/learn/knowledge-graph), by IBM Cloud Education, April 12, 2021 
[^2]: [Interactive and iterative visual exploration of knowledge graphs based on shareable and reusable visual configurations](https://www.sciencedirect.com/science/article/pii/S1570826822000105#b2), by Martin Nečaský, Štěpán Stenchlák 
[^3]: [The 10 rules of great graph design](https://cambridge-intelligence.com/10-rules-great-graph-design/), by Corey Lanum, January 10, 2014 
[^4]: [Data Visualization Effectiveness Profile](http://perceptualedge.com/articles/visual_business_intelligence/data_visualization_effectiveness_profile.pdf), by Stephen Few, 2017 
[^5]: [On the faithfulness of graph visualizations](https://link.springer.com/chapter/10.1007/978-3-642-36763-2_55) 
[^6]: [Graph Drawing and Network Visualization](https://link.springer.com/book/10.1007/978-3-030-35802-0#about-this-book), pp 272–286, Eades, P., Nguyen, Q., Hong, SH., 2018. 
[^7]: [Proxy graph: visual quality metrics of big graph sampling](https://www.researchgate.net/publication/314028734_Proxy_Graph_Visual_Quality_Metrics_of_Big_Graph_Sampling), by Quan Hoang Nguyen at al, February 2017 
[^8]: [Centrality Algorithms](https://neo4j.com/developer/graph-data-science/centrality-graph-algorithms/) 
[^9]: [Centralities in Large Networks: Algorithms and Observations](https://www.researchgate.net/publication/220907224_Centralities_in_Large_Networks_Algorithms_and_Observations), U. Kang, S. Papadimitriou, J. Sun 
[^10]: [Graph Sparsification by Effective Resistances](https://www.academia.edu/14009496/Graph_Sparsification_by_Effective_Resistances), D.A. Spielman and N. Srivastava, SIAM Journal on Computing, 40(6):1913–1926, 2011. 
[^11]: [Sampling from large graphs](https://www.academia.edu/2957458/Sampling_from_large_graphs), Jure Leskovec, Christos Faloutsos 
[^12]: [Reducing Large Internet Topologies for Faster Simulations](https://link.springer.com/chapter/10.1007/11422778_27) 
[^13]: [Impact of Sampling Design in Estimation of Graph Characteristics](https://www.researchgate.net/publication/264543191_Impact_of_Sampling_Design_in_Estimation_of_Graph_Characteristics) 
[^14]: [Multigraph Sampling of Online Social Networks](http://www.minasgjoka.com/papers/jsac11_multigraph_sampling.pdf), Minas Gjoka, Carter T. Butts, Maciej Kurant, Athina Markopoulou, 2011 
[^15]: [On Sampling Type Distribution from Heterogeneous Social Networks](https://link.springer.com/chapter/10.1007/978-3-642-20847-8_10), Li, JY., Yeh, MY. (2011) 
[^16]: [Spectral sparsification of graphs: theory and algorithms](https://dl.acm.org/doi/10.1145/2492007.2492029), D. Spielman at al, pp87-94 
[^17]: [Evaluation of graph sampling: a visualization perspective](https://ieeexplore.ieee.org/document/7539318), Y. Wu, N. Cao, D. Archambault, Q. Shen, H. Qu and W. Cui, pp. 401-410, Jan. 2017 
[^18]: [Deep Recursive Embedding for High-Dimensional Data](https://www.computer.org/csdl/journal/tg/2022/02/09585419/1y11cQpf9nO), Z. Zhou, X. Zu, Y. Wang, B. Lelieveldt and Q. Tao, Feb. 2022, pp. 1237-1248, vol. 28 
[^19]: [Spectral Sparsification of Graphs](https://epubs.siam.org/doi/10.1137/08074489X), 4 (2011), 981–1025. Spielman, D.A., Teng, S.H. 
[^20]: [Benczúr, A.A., Karger, D.R. Approximating s-t minimum cuts in O(n2) time.](https://dl.acm.org/doi/abs/10.1145/237814.237827) In (1996), 47–55. 
[^21]: Chung, F.: Spectral Graph Theory. American Maths Society (1997) 
[^22]: [Handbook of Graph Theory.](https://doi.org/10.1201/9780203490204), CRC Press, Boca Raton (2004) 
[^23]: [Algebraic connectivity](https://en.wikipedia.org/wiki/Algebraic_connectivity) 
[^24]: [Graph clustering](https://paperswithcode.com/task/graph-clustering) 
[^25]: [Community detection in social networks](https://www.researchgate.net/publication/295395520_Community_detection_in_social_networks), Bedi, Punam & Sharma, Chhavi. (2016) 
[^26]: [Community detection in graphs](https://www.sciencedirect.com/science/article/pii/S0370157309002841), Physics Reports 2010, Fortunato S.
[^27]: [A classification for community discovery methods in complex networks](https://dl.acm.org/doi/abs/10.1002/sam.10133), Coscia M, Giannotti F, Pedreschi D., 2011 
[^28]: [Community structure in graphs](https://link.springer.com/referenceworkentry/10.1007/978-1-4614-1800-9_33), Fortunato S, Castellano C., 2012 
[^29]: [Community detection and graph partitioning](https://www.researchgate.net/publication/236878828_Community_detection_and_graph_partitioning), Newman M, May 2013
[^30]: [Community structure in social and biological networks](https://pubmed.ncbi.nlm.nih.gov/12060727/), M. Girvan, M. E. J. Newman, 2002 Jun 11 
[^31]: [Finding and Evaluating Community Structure in Networks](https://www.researchgate.net/publication/7659762_Finding_and_Evaluating_Community_Structure_in_Networks), M. Girvan, M. E. J. Newman, 2004 July
[^32]: [An algorithm to find overlapping community structure in networks](https://dl.acm.org/doi/10.5555/3120747.3120761), Steve Gregory, 2017 
[^33]: [Self-similar community structure in a network of human interactions](https://www.academia.edu/es/14480330/Self_similar_community_structure_in_a_network_of_human_interactions), Francesc Giralt, Alex Arenas, 2003 
[^34]: [Community analysis in social networks](https://www.academia.edu/14514794/Community_analysis_in_social_networks), Arenas A, Danon L, Diaz-Guilera A, Gleiser PM, Guimera R. 2004 
[^35]: [Email as Spectroscopy: AutomatedDiscovery of Community Structurewithin Organizations](https://www.academia.edu/en/18178198/Email_as_Spectroscopy_Automated_Discovery_of_Community_Structure_within_Organizations), Joshua R. Tyler, Dennis M. Wilkinson, Bernardo A. Huberman, 2005 
[^36]: [Defining and identifying communities in networks](https://www.pnas.org/doi/10.1073/pnas.0400054101), Radicchi F, Castellano C, Cecconi F, Loreto V, Parisi D., 2004 
[^37]: [Fast unfolding of communities in large networks](https://www.academia.edu/1382204/Fast_unfolding_of_communities_in_large_networks), Blondel VD, Guillaume JL, Lambiotte R, Lefebvre E. 
[^38]: [SCAN: A Structural Clustering Algorithm for Networks](https://dl.acm.org/doi/10.1145/1281192.1281280), Xiaowei Xu, Nurcan Yuruk, Zhidan Feng, Thomas A. J. Schweiger, August 2007 
[^39]: [A survey on hierarchical community detection in large-scale complex networks](https://ajmc.aut.ac.ir/article_4913_b27d5315a5e853db2e5b555dd2b915f8.pdf), M. Rezvani, F. S. Kazemian AUT J. Math. Com., 3(2) (2022) 173-184 DOI: 10.22060/AJMC.2022.21715.1103, September 2022 
[^40]: "Hierarchical Clustering of Complex Networks: Structural Clustering Algorithms and Applications", Zhi-Qiang Liu, Hong-Xing Yu 
[^41]: "Hierarchical Community Detection in Complex Networks: A Survey", C. Cattuto, A. Chessa, V. Latora, A. Cardillo 
[^42]: [Hierarchical Agglomerative Graph Clustering in Nearly-Linear Time](https://www.semanticscholar.org/paper/Hierarchical-Agglomerative-Graph-Clustering-in-Time-Dhulipala-Eisenstat/cef585ee17dfc400009444dc76fdb602f08a4a18), L. Dhulipala, D. Eisenstat, J. Lacki, V. Mirrokni, J. Shi, 18–24 Jul 2021, pp. 2676–2686 
[^43]: [Finding Groups in Data: An Introduction To Cluster Analysis](https://www.researchgate.net/publication/220695963_Finding_Groups_in_Data_An_Introduction_To_Cluster_Analysis), pp. 253-279, Kaufman, L.; Rousseeuw, P.J. (2009) [1990]. 
[^44]: [Spectral Clustering in Heterogeneous Information Networks](https://www.researchgate.net/publication/332606853_Spectral_Clustering_in_Heterogeneous_Information_Networks), Li, Xiang & Kao, Ben & Ren, Zhaochun & Yin, Dawei. (2019) 
[^45]: [Graph Summarization Methods and Applications: A Survey](https://www.researchgate.net/publication/326028431_Graph_Summarization_Methods_and_Applications_A_Survey), Liu, Yike & Safavi, Tara & Dighe, Abhilash & Koutra, Danai, 2018, 1-34. 
[^46]: [Dynamic network summarization using convex optimization](https://www.researchgate.net/publication/261462106_Dynamic_network_summarization_using_convex_optimization), Ali Yener Mutlu, Selin Aviyente, 2012 
[^47]: [Efficient online summarization of large scale dynamic networks in social network](https://www.academia.edu/82684328/Efficient_online_summarization_of_large_scale_dynamic_networks_in_social_network), Deepa Parasar, 2018 
[^48]: [Scalable Pattern Matching over Compressed Graphs via Dedensification](https://dl.acm.org/doi/10.1145/2939672.2939856), Maccioni, A., & Abadi, D. J. (2016, August) 
[^49]: [Coarsening Graphs with Neural Networks](https://karush27.github.io/posts/2012/08/blog-post-24/), October 11, 2021 
[^50]: [HARP: Hierarchical Representation Learning for Networks](https://www.researchgate.net/publication/317930440_HARP_Hierarchical_Representation_Learning_for_Networks), H. Chen, B. Perozzi, Y. Hu, and S. Skiena, 2018 
[^51]: [MILE: A Multi-Level Framework for Scalable Graph Embedding](https://www.researchgate.net/publication/323444008_MILE_A_Multi-Level_Framework_for_Scalable_Graph_Embedding), J. Liang, S. Gurukar, and S. Parthasarathy, 2018 
[^52]: [A Multi-Level Algorithm For Partitioning Graphs](https://www.researchgate.net/publication/4118126_A_Multi-Level_Algorithm_For_Partitioning_Graphs), B. Hendrickson, R. Leland February 1995 
[^53]: [A Fast and High Quality Multilevel Scheme for Partitioning Irregular Graphs](https://www.researchgate.net/publication/242479489_Kumar_V_A_Fast_and_High_Quality_Multilevel_Scheme_for_Partitioning_Irregular_Graphs_SIAM_Journal_on_Scientific_Computing_201_359-392), G. Karypis, V. Kumar, January 1999 \
