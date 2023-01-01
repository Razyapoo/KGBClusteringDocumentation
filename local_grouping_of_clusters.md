# Local grouping of clusters

_This page describes how local grouping of clusters is working._

---

### Table of content

- [Motivation](#motivation)
- [Research part](#research)
- [Implementation](#implementation)
- [Conclusion](#conclusion)

<h1 id="motivation">Motivation</h1>

The use of local grouping allows users to work only with the area of the graph in which they are interested. The main advantage of local grouping over global grouping is that it requires less processing time and usage of resources.

<h1 id="research">Research part</h1>

This section describes the various methods for selecting nodes. We can state the following ones:

1. A node(s) can be selected using mouse click.
2. A node can be considered selected when the mouse pointer is over the node.
3. All nodes located in the visible graph area can be implicitly considered selected.
4. All nodes returned by the filter are considered selected.
5. The [configuration file](#TODO) may define a list of nodes. 

Selecting a node with a mouse click is a simple and straightforward method. At the same time, it is convenient and understandable to users on an intuitive level. 

Selecting a node while the mouse is over is also intuitive, but it can cause local grouping on a node that is accidentally under the mouse pointer. 

The implicit selection of all nodes visible in the graph area is less intuitive and may confuse the user. To take advantage of this feature, it's best to explicitly select all the nodes on which you want to perform local grouping.

It is also possible to perform a local grouping of previously filtered nodes. This method can simply be broken down into several steps. First filter the nodes, select them all, and then perform local grouping on selected nodes.

The current version of the cluster grouping method supports grouping of specific hierarchical groups. Such groups can be listed by the technician in the visual configuration file. However, clustering of only predefined nodes is not supported because this use case is very rare and has a specific domain.

Selecting nodes with a mouse click is considered the best option to use as a starting point for local grouping. This method can be easily improved and used as a basis for other more complex methods.

<h1 id="implementation">Implementation</h1>

The main advantage of the [`groupingOfClustersManager`](https://github.com/Razyapoo/knowledge-graph-browser-frontend-grouping-of-clusters/blob/5ee77643da16800807253298f984bcf5b13ec336/src/graph/GraphAreaManipulator.ts#L124) method is that it groups a predefined list of nodes. In the case of a global grouping, the list of predefined nodes consists of all nodes on the graph area, and in the case of a local grouping, it consists of the selected nodes and all their descendants (if any).

The general logic of cluster grouping has undergone a slight change. The previous version of the algorithm performed grouping sequentially with respect to the hierarchical level, that is, in each subsequent iteration of the algorithm, nodes were taken from the [current hierarchical level](#TODO) or the next (+/- 1) level. This method, however, cannot be used with local grouping, because the user can select an arbitrary node on which he wants to perform local grouping, and this level may differ from the current hierarchical level. To solve this problem, the logic of the current hierarchical level is changed, but it is still valid that when zooming in, nodes having lower level have preference, and otherwise when zooming out. Each iteration of the cluster grouping performs a new search for the current hierarchical level. For example, when zooming out, `globalHierarchicalDepth` is set to the lowest level (keeping in mind that the level is increasing according to depth of hierarchy, i.e. descendants have higher hierarchical level in the hierarchy) among the nodes chosen to be grouped. However, when zooming in, it is not enough to simply find the highest level, but you also need to keep in mind that there may be nodes that have a lower hierarchical level, and at the same time have collapsed children. In this case, we need to give preference to such nodes.

This method has an advantage when nodes that have an intermediate hierarchical level are removed. In this case, gaps remain in the sequence of hierarchical levels, for example, nodes located at hierarchical levels 1,2,3,7,8,9 exist, and nodes located at levels 4,5,6 do not exist on the graph. In this case, algorithm understand the gap in the sequence of levels and looks for next existing level.

It is also possible to combine local and global options of cluster grouping. In this case, if some nodes are selected, the local version is executed on them, and when no node is selected, the global version is executed.

<h1 id="conclusion">Conclusion</h1>

The concept used to implement [global grouping](#TODO) made it easy to extend it to support local grouping. In the local version grouping is performed only on the selected nodes and all their descendants (if any).