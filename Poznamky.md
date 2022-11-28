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