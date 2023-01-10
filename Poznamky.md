Nov 22, 2022

What to ask opponent: 
1. what exactly opponent did not like: presentation or something is missing in the project

Result: 

Presentation is very complex, figures are redundant. 

How to present?

1. Try to be as simple as possible
2. Knowledge graph slaid. To add example of the knowledge graph
3. Knowledge Graph Visual browser. Remove reusable configurations. And add simple example of the knowledge graph with multilevel path. 
4. Examples as Starting nodes, vkgb, expansion example are redundant. Delete them.
5. Try to draw manually examples of Visual knowledge graphs.
6. When we will frequently expand graph, in a while we can get a very big and complex graph (show an example of such graph). Those the user will want to collapse and zoom some nodes. And at this point our extension comes. We do clusters, which correlates with grouping and zooming. And at this point comes zooming that is used in the mapping platforms.
7. How to cluster nodes? Of course, we can use some heuristic method to cluster nodes, but this is very nondeterministic and can collapse nodes having different semantic meaning, like to cluster animals and automobiles. And as our tool is manageable by configurations, and we want to preserve semantic behavior(chovani), we use configurations where the user can set up behavior (chovani). 

Knowledge graphs are semantic graphs, thus preserve semantic meaning of the nodes.Therefore we want to preserve semantic meaning of nodes. And as by expansions we expand neighborhood of the node, we 


1. We can also collapse nodes based on their names, but this doesn't make sense. Because we are working with semantics of nodes. As nodes are expanded close to its "expanded-parent" node, they are semantically connected to it  and between each other and it is useful to cluster and collapse them. Also it is possible to set up in the configuration which nodes cannot be clustered together.


Where to add new methods:

- Try to add at least one method that is completely different from what I have implemented.
  - For example heuristic method that collapses all nodes heuristically. 
- At each level of abstraction of the implemented method add new different methods and show why they are less suitable there
  - For example how to add different nodes to the cluster:
    - 1. based on the similarity:
      - Position
      - Name of the node
      - Other specific properties of the elements
      - 
    - 2. based on the Topological criteria
      - graph structure
      - Strongly-connected component
      - 
  - Different clustering algorithms:
    - KMeans 
    - KMedoids
- How to show clusters
  - different style
  - user may want to interact with the resulting diagram and manually modify the clustering
- How to layout clusters


# Useful links

- https://www.yworks.com/pages/clustering-graphs-and-networks


links:

https://www.semanticscholar.org/paper/Proxy-Graph%3A-Visual-Quality-Metrics-of-Big-Graph-Nguyen-Hong/3db14edc3a191906f7203ad6b64b46d51241b038
https://arxiv.org/pdf/2004.14794.pdf
https://dl.acm.org/doi/pdf/10.1145/3186727
https://users.ics.forth.gr/~kondylak/publications/2019_edbt.pdf
https://www.academia.edu/72813229/RDF_graph_summarization_principles_techniques_and_applications
https://www.semanticscholar.org/paper/RDF-graph-summarization%3A-principles%2C-techniques-and-Kondylakis-Kotzinos/793b12435e91bb166a6047365c91bb0d9034901d
https://www.yworks.com/pages/interactive-filtering-of-large-diagrams
https://cambridge-intelligence.com/big-graph-data-visualization/
https://www.cs.cmu.edu/~ukang/papers/CentralitySDM2011.pdf
https://neo4j.com/blog/graph-algorithms-neo4j-louvain-modularity/
https://networkx.org/documentation/stable/reference/algorithms/summarization.html
file:///home/oskar/Documents/Research%20project/books/faithfulness_PacificVis2013.pdf
file:///home/oskar/Documents/Research%20project/books/proxy.pdf
https://www.academia.edu/14009514/Spectral_sparsification_of_graphs_theory_and_algorithms
https://www.academia.edu/2957458/Sampling_from_large_graphs
https://www.academia.edu/3144485/Effectively_visualizing_large_networks_through_sampling
file:///home/oskar/Documents/Research%20project/books/Drawing%20big%20graphs%20using%20spectral%20sparsification.pdf
