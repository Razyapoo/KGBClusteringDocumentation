# Grouping of clusters

_This page describes how the grouping of clusters approach is implemented in the Knowledge Graph Browser._

---
### Table of Contents

- [Main goal](#main-goal)
- [Glossary](#glossary)
- Knowledge Graph browser
  - [Frontend](#frontend)
  - [Backend](#backend)
  - [Configuration](#configuration)

---
 
<h1 id="main-goal">Main goal</h1>

So far, in the Knowledge Graph browser, it was only possible to group nodes in such a way that the user selects a couple of nodes and makes a group out of them manually. But in many cases it is more efficient and easier for the user to escape such work and allow the application to automatically group nodes based on similar attribute values.

The main purpose of the implemented grouping of clusters approach is to make a large graph more user-friendly, i.e. more readable and understandable. 


<h1 id="glossary">Glossary</h1>

<details>
    <summary>expand</summary>
    
    This part of the documentation contains necessary terms that are used later in the documentation. They will help you understand the basic principle of how the extension works.

    Terms described here may differ from usual terms you may be familiar with. This glossary is slightly different from the [glossary in the user documentation](user_documentation.md#glossary).

    <h3 id="visual-layout-constraint-glossary">Visual layout constraint</h3>

    > **Definition** \
    > A visual layout constraint is a rule (constraint) applied to a graph to change a way it is visualized.

    <h3 id="parent-child-or-child-parent-hierarchical-relationship-glossary">Parent-child or child-parent hierarchical relationship</h3>

    Typically, nodes in a graph are related to each other, for example, a company has employees, university has scientists, scientist has awards, scientist writes scientific papers, university has departments, and many other examples. 

    One possible way to visualize such relationship is to create an edge between parent and child. But there is also another way, namely adding a hierarchy between nodes. In such case, parent node is visualized as a larger node containing child nodes inside.

    Figure 1 below shows an example with universities and departments. A university can be thought of as a larger node (blue) containing child nodes (light blue) inside, representing departments: 

    <p align="center">
        <img src="img/child_parent_relation.png" alt="parent-child-relationship" title="Parent-child relationship" width="600"/><br/>
        <em>Figure 1. Parent-child relationship</em>
    </p>

    Each such node hierarchy represents a [hierarchical group](#hierarchical-group-glossary).

    > **Warning** \
    > Hierarchical relationships are predefined by a technician in the [visual configuration](#https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/technical_documentation.md#childparentlayoutconstraint-and-parentchildlayoutconstraint-classes).

    Expansion query can be triggered from a parent node (expand child nodes) as well as form a child node (expand parent node). In both cases it may use same predicate in SPARQL CONSTRUCT, for example `skos:broader`.

    <h3 id="non-hierarchical-relationships-glossary">Non-hierarchical relationships </h3>

    > **Definition** \
    > Non-hierarchical relationships are represented by edge between nodes.

    Non-hierarchical relationships are also possible. 

    For example, "the department teaches the subject" relationship can be visualized as non-hierarchical. Example is shown in the Figure 2 below.

    <p align="center">
        <img src="img/non_hierarchical_edge.png" alt="non-hierarchical-edge" title="Non-hierarchical edge" width="600"/><br/>
        <em>Figure 2. Non-hierarchical edge</em>
    </p>

    > **Note** \
    > Non-hierarchical relationships are all relationships other than [hierarchical](#parent-child-or-child-parent-hierarchical-relationship-glossary).

    <h3 id="hierarchical-class-glossary">Hierarchical class</h3>

    > **Definition** \
    > A hierarchical class is a visual class that defines which [hierarchical group](#hierarchical-group-glossary) a node belongs to. A node can only be assigned to one hierarchical class.

    A hierarchical class, if it exists, is shown along with a label of a node on the detail panel. See Figure 4 below for more details.

    <p align="center">
        <img src="img/hierarchical_class.png" alt="hierarchical-class" title="Hierarchical class" width="350"/><br/>
        <em>Figure 4. Hierarchical class</em>
    </p>

    A hierarchical class (or hierarchical group class) is a common class for all nodes to be placed in a same hierarchical group. It is assigned to a node in a SPARQL CONSTRUCT query in the same way as a visual class, i.e. using the `browser:class` predicate. 

    > **Warning** \
    > Each node must be assigned to some hierarchical group class in case it is to be placed in any hierarchy.

    <h3 id="hierarchical-level-glossary">Hierarchical level</h3>

    > **Definition** \
    > A hierarchical level of a node indicates the depth of a hierarchy at which a node resides.

    The amount of detail displayed on maps (in mapping platforms) depends on a zoom level. Grouping of clusters approach uses the same idea. At the deepest level of the hierarchy, the graph shows all possible details. And at the highest level of the hierarchy, the graph shows only those single nodes that are representatives of hierarchies themselves. 

    <h3 id="current-hierarchical-level-glossary">Current hierarchical level</h3>

    > **Definition** \
    > A current hierarchical level is the deepest [hierarchical level](#hierarchical-level-glossary) shown in the graph area.

    At the moment when child nodes collapse into their parents, the current hierarchical level decreases by 1, and when child nodes with a hierarchical level higher (deeper) by 1 than the current hierarchical level appear, the current hierarchical level increases by 1.

    The `globalHierarchyDepth` attribute value indicates the current hierarchical level.

    <h3 id="hierarchical-group-glossary">Hierarchical group</h3>

    > **Definition** \
    > A hierarchical group is a cluster of nodes that are related to each other by [parent-child relationships](#parent-child-or-child-parent-hierarchical-relationship-glossary). 

    Each node in a hierarchical group must have a [hierarchical class](#hierarchical-class-glossary) that represents that hierarchical group.

    An example of one such hierarchical group is shown in Figure 1 above.

    > **Warning** \
    > A hierarchical group is predefined by a technician in the [visual configuration](#https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/technical_documentation.md#hierarchicalgroupstoclusterlayoutconstraint-class).

    <h3 id="visual-group-glossary">Visual groups</h3>

    > **Definition** \
    > A visual group is a cluster of nodes located in the same area on a graph. Nodes that belong to the same visual group are placed under the same "pseudo-parent" node representing the visual group itself.

    An example of a visual group is shown in the Figure 2 below. The "pseudo-parent" node is a gray node with white nodes inside.

    <p align="center">
        <img src="img/visual_group.png" alt="visual-group" title="Visual group" width="600"/><br/>
        <em>Figure 2. Visual group</em>
    </p>

    > **Warning** \
    > A visual group is predefined by a technician in the [visual configuration](#https://github.com/Razyapoo/KGBClusteringDocumentation/blob/main/technical_documentation.md#visualgrouplayoutconstraint-class).

    Each node in a visual group must have an additional visual class representing that visual group. It can be identical to the hierarchical class.

    > **Note** \
    > Hierarchical groups themselves can be interpreted as visual groups. In such a case, there is no need for a "pseudo-parent".

    An example of two visual groups "pracovisteVisualGroup" and "tema" is shown in Figure 3 below.

    <p align="center">
        <img src="img/visual_groups.png" alt="visual-groups" title="Visual groups" width="600"/><br/>
        <em>Figure 3. Visual groups</em>
    </p>

    > **Note** \
    > The main advantage of visual groups is that you can easily move all the nodes that belong to the same group across the entire graph area at the same time. This way they won't be scattered all over the graph area. 

</details>

<h1 id="implementation">Implementation</h1>

Implementation of "Grouping of clusters" extension is split into two parts: 

1. [Backend](#backend)
2. [Fronted](#frontend)

--- 

<h2 id="backend">Backend</h2>

<h2 id="backend-service">Backend service</h2>

The original backend service is extended with a new request handler *"/layout-constraints"*.

This handler sends a query defined in the visual configuration to a SPARQL endpoint and receives [visual layout constraints](#visual-layout-constraint-glossary) to apply in the main application.

The output of the request handler is a JSON object containing all the constraints.

<h3 id="backend-implementation">Implementation</h3>

See the implementation of [backend service](https://github.com/Razyapoo/knowledge-graph-browser-backend).

<h2 id="visual-configuration">Visual configuration</h2>

The original visual configuration is extended with a set of [visual layout constraints](#visual-layout-constraint-glossary) that are assigned using the *browser:hasLayoutConstraints* property. 

A visual layout constraint set is a set of [visual layout constraints](#visual-layout-constraint-glossary) to be applied to a graph. It is expressed as an instance of the *browser:LayoutConstraintSet* class. Individual [visual layout constraints](#visual-layout-constraint-glossary) are instances of an individual layout constraint classes and are assigned to a visual layout constraints set using the *browser:hasConstraint* property. 

The next few sections describe each layout constraint class in more details. It is expected that each node has a [hierarchical class](#hierarchical-class-glossary) assigned. 

<h3 id="visual-group">"VisualGroupLayoutConstraint" class</h3>

A [visual layout constraint](#visual-layout-constraint-glossary) defining [visual group](#visual-group-glossary) is expressed as an instance of the *browser:VisualGroupLayoutConstraint* class. Visual class is assigned to the visual group layout constraint via *browser:groupingSelector* property. It is usually identical to the [hierarchical class](#hierarchical-class-glossary), so there is no need for an extra visual class.

> **Warning** 
> Each visual class must be assigned to a separate instance of *browser:VisualGroupLayoutConstraint* class.

<h3 id="hierarchical-groups-to-cluster">"HierarchicalGroupsToClusterLayoutConstraint" class</h3>

It can be useful not to group nodes that belong to the same [hierarchical group](#hierarchical-group-glossary) (within that group). 

A [visual layout constraint](#visual-layout-constraint-glossary) that determines [hierarchical groups](#hierarchical-group-glossary), in which we can run ["*grouping of clusters*"](#grouping-of-clusters-KCluster), is expressed as an instance of the *browser:HierarchyGroupToClusterLayoutConstraint* class. The [hierarchical (group) class](#hierarchical-class-glossary) is assigned using the *browser:clusteringSelector* property. 

> **Warning** 
> Each hierarchical group class must be assigned to a separate instance of the *browser:HierarchyGroupToClusterLayoutConstraint* class.

<h3 id="classes-to-cluster-together">"ClassesToClusterTogetherLayoutConstraint" class</h3>

By default the ["*grouping of clusters*"](#grouping-of-clusters-KCluster) algorithm only cluster and group nodes of the same visual classes (may very from hierarchical classes) within parent node. But, it is possible to define which visual classes can be clustered and grouped together (within the parent node). 

A [visual layout constraint](#visual-layout-constraint-glossary) that defines classes to cluster and group together is expressed as an instance of the *browser:ClassesToClusterTogetherLayoutConstraint* class. Classes are assigned using the *browser:clusteringSelector* property.

> **Warning**
> Use this layout constraint only if you want to create groups containing nodes of different classes. Put all classes you want to cluster and group together in the one instance of the *browser:ClassesToClusterTogetherLayoutConstraint* class.

<h3 id="child-parent-or-parent-child-layout-constraint">"ChildParentLayoutConstraint" and "ParentChildLayoutConstraint" classes</h3>

A [visual layout constraint](#visual-layout-constraint-glossary) defining [child-parent](#parent-child-or-child-parent-hierarchical-relationship-glossary) (resp. [parent-child](#parent-child-or-child-parent-hierarchical-relationship-glossary)) relationships is expressed as an instance of the *browser:ChildParentLayoutConstraint* (resp. *browser:ParentChildLayoutConstraint*). Classes playing a child role (resp. parent role) are assigned using the *browser:childNodeSelector* (resp. *browser:parentNodeSelector*) property. Expansion property in expansion SPARQL query (also a class of an expansion edge in the graph) is selected using the *browser:hierarchyEdgeSelector*.

> **Warning** 
> Each pair of node and edge selectors must be assigned to a separate instance of the *browser:ChildParentLayoutConstraint* (resp. *browser:ParentChildLayoutConstraint*) class.


<h3 id="backend-configuration-implementation">Implementation</h3>

See a visual configuration example [here](https://github.com/linkedpipes/knowledge-graph-browser-configurations/blob/main/configurations/university-topic-map-with-constraints.ttl) (base configuration is [here](https://github.com/linkedpipes/knowledge-graph-browser-configurations/blob/main/configurations/university-topic-map.ttl)).

---

<h2 id="frontend">Frontend script</h2>

We all know how zoom in/out works on mapping platforms such as [google maps](https://maps.google.com), maps.cz, etc. Zoom is used to increase or decrease the zoom level at a specific point and show more or less detail on a map.

The "grouping of clusters" extension of the original Knowledge Graph browser is inspired by such mapping platforms.

The next few sections describe extensions to the main components of the source code. 

> **Note**
> Implementation of each component extension is described in more details in the code comments.

<h3 id="extension-of-the-grapharea">Extension of the GraphArea.vue</h3>

The original GraphArea component is extended with a checkbox that allows the user to choose whether to zoom or run the [*"grouping of clusters"*](#grouping-of-clusters-KCluster) algorithm.

<h3 id="extension-of-the-graphareastylesheetmixin">Extension of the GraphAreaStylesheetMixin.ts</h3>

The original component is extended with visual styles for the parent node. When a node becomes a parent, having at least one child node placed inside, its visual style changes so that its label appears at the top and center. Also, the shape of the node becomes octagonal.

<h3 id="extension-of-the-graphelementedge">Extension of the GraphElementEdge.ts</h3>

This component is extended to check if the ends of an edge are in a [parent-child relationship](#parent-child-or-child-parent-hierarchical-relationship-glossary) relationship. If so, creating an arrow-shaped [non-hierarchical](#non-hierarchical-relationships-glossary) edge between the parent and child nodes is redundant.

<h3 id="extension-of-the-graphelementnode">Extension of the GraphElementNode.ts</h3>

This component is extended with method setHierarchicalInfo() which establishes the[hierarchical class](#hierarchical-class-glossary), the [hierarchical level](#hierarchical-level-glossary) and the pseudo-parent (see [visual group](#visual-group-glossary) for more information) of a node and updates *globalHierarchicalDepth* attribute if a node is opened at a new lower [hierarchical level](#hierarchical-level-glossary).

<h3 id="extension-of-the-graphelementnode">Extension of the GraphElementNode.ts</h3>

This component is extended with method setHierarchicalInfo() which establishes the[hierarchical class](#hierarchical-class-glossary), the [hierarchical level](#hierarchical-level-glossary) and the pseudo-parent (see [visual group](#visual-group-glossary) for more information) of a group node and updates *globalHierarchicalDepth* attribute if a group node is opened at a new lower [hierarchical level](#hierarchical-level-glossary).

<h3 id="extension-of-the-graphelementnodemixin">Extension of the GraphElementNodeMixin.ts</h3>

By default (property of [Cytoscape](https://js.cytoscape.org/) library) selecting a child node also selects its parent.

Therefore, when selecting a node, it is necessary to make all its ancestor nodes unselectable, and after selecting a node, make the ancestor nodes selectable again.

The [Cytoscape](https://js.cytoscape.org/) library uses the "parent" property of the element to visualize a [parent-child](#parent-child-or-child-parent-hierarchical-relationship-glossary) hierarchy. Therefore, when expanding a parent node from a child node, it is necessary to explicitly specify the parent for a Cytoscape element that representing a child node.

<h3 id="extension-of-the-configuration">Extension of the Configuration.ts</h3>

The original component is extended with a new attribute *constraints* that is used to store the IRI representing a [set of visual layout constraints](#visual-configuration) to be loaded.

<h3 id="extension-of-the-application">Extension of the Application.vue</h3>

This component is extended with a new method **loadConstraints()** to get [visual layout constraints](#visual-layout-constraint-glossary) from the server, as well as new *isHierarchyView* and *constraintRulesLoaded* attributes to indicate whether a visual style of a parent node needs to be changed (more in "[Extension of the ViewOptions.ts](#extension-of-the-view-options)" section) and whether constraints are loaded from the server successfully.

<h3 id="extension-of-the-edge">Extension of the Edge.ts</h3>

Edge is extended with a new *isEdgeFromChild* attribute showing whether an edge is coming from a child node.

<h3 id="extension-of-the-graph-area-manipulator">Extension of the GraphAreaManipulator.ts</h3>

When you zoom in on a specific point on the mapping platforms, at each zoom level, you see more and more details about the region you zoom in, and also otherwise, when you zoom out, some details disappear. The same principle is used in this "Grouping of clusters" extension, namely, when you zoom in, you see more detail in terms of nodes, and when you zoom out, you see less detail in terms of nodes.

The original component is extended with a new private *groupingOfClustersManager* method that can filters nodes to cluster and group, ungroup existing groups, collapse child nodes or show child nodes collapsed in their parent nodes. The component is also extended with the *globalHierarchyDepth* attribute, whose value indicates the [current hierarchical level](#current-hierarchical-level-glossary).

When grouping, the *groupingOfClustersManager* method filters all nodes that have a [hierarchical group class](#hierarchical-class-glossary) that is allowed to be grouped in the visual configuration (more in [Hierarchical groups to cluster](#hierarchical-groups-to-cluster) section). The algorithm then filters out of all previously filtered nodes only those that reside at the [current hierarchical level](#current-hierarchical-level-glossary) (based on the value of the *globalHierarchyDepth* attribute). 

> **Note** 
> Value of the *globalHierarchyDepth* attribute does not show the deepest hierarchical level that has been achieved in the graph during the research, but the deepest level of nodes that are still visible in the graph.

As the map (in the mapping platforms) scales down and details disappear, new correlated details appear in their place that generalize the disappeared details. In our case, the parent node is such a generalization. Therefore, the second condition of the grouping algorithm must be the grouping of nodes that have the same parent node.

From now on, the algorithm groups all filtered nodes, but based on the parent and visual classes (may vary from the [hierarchical class](#hierarchical-class-glossary) of a node). First, it filters all nodes that have the same parent, then from the filtered nodes, it filters out nodes that have the same visual class, unless multiple visual classes are explicitly set in the visual configuration (more in [Classes to cluster together](#classes-to-cluster-together) section).

Two cases can occur during grouping:

- In the first case (example is shown in the Figure 8 below), at the end of the filtering, there are several nodes that can be clustered and grouped (within same parent). The algorithm then calls the *groupingOfClusters* function, which performs the clustering and grouping of the filtered nodes. This function is described in more detail in the [KCluster](#KCluster) section.

<p align="center">
    <img src="img/grouping_of_clusters_several_child_nodes.png" alt="grouping-of-clusters-several-child-nodes" title="Grouping of clusters several child nodes" width="600"/><br/>
    <em>Figure 8. Grouping of clusters (use-case of several child nodes)</em>
</p>

- In the second case, only one child node remain (per parent) at the end of the filtering (example is shown in the Figure 9 below). 

  <p align="center">
      <img src="img/grouping_of_clusters_one_child_node.png" alt="grouping-of-clusters-one-child-node" title="Grouping of clusters with one child node" width="600"/><br/>
      <em>Figure 9. Grouping of clusters (use-case of one child node)</em>
  </p>

  This child node can represent a single node or a group containing all of the parent's child nodes. In this case, the remaining child node (in each parent) should be collapsed into the parent node, but this should only happen when all the child nodes having [current hierarchical level](#current-hierarchical-level-glossary) are the only child nodes of their parents (as shown in the Figure 9 above).

  > **Note**
  > After collapsing child nodes, the algorithm switches the [current hierarchical level](#current-hierarchical-level-glossary) one level higher (*globalHierarchyDepth* attribute value is increased by one). During this operation, all [non-hierarchical](#non-hierarchical-relationships-glossary) edges from child nodes are moved to the parent node.

When ungrouping, only nodes at the [current hierarchical level](#current-hierarchical-level-glossary) can be ungrouped. 

There are two cases:

- In the first case, there is at least one group at the [current hierarchical level](#current-hierarchical-level-glossary). 
  > **Warning**
  > In such case, algorithm ungroups random number of random groups. 

- In the second case, there are only single nodes representing the parents of the nodes that were collapsed during the previous calls of the ["groupingOfClusters"](#grouping-of-clusters-KCluster) algorithm in the past (see [Extension of the NodeCommon.ts](#extension-of-the-node-common) for more details). In such case, after a call of the *groupingOfClustersManager* operation, collapsed nodes appear on the graph area as child nodes. In

    > **NOTE**
    > In such case the [current hierarchical level](#current-hierarchical-level-glossary) **may** decrease by 1.

<h3 id="extension-of-the-graph-manipulator">Extension of the GraphManipulator.ts</h3>

In this component, only group management methods are extended to set a parent node, a [hierarchical level](#hierarchical-level-glossary), and a [hierarchical group](#hierarchical-group-glossary). 

> **Warning**
> Make sure that when you ungroup a group you need to remove that group from the parent list of child nodes and also otherwise when you group nodes you need to remove them from the parent list of child nodes but add the newly created group as a child node.

<h3 id="extension-of-the-node">Extension of the Node.ts</h3>

When deleting a node, you must also delete all its descendant nodes in the hierarchy, as this may violate the principle of the hierarchy.

An extension of this component is the *remove* method, which is extended to handle the recursive removal of child nodes (including nodes and groups).

<h3 id="extension-of-the-node-common">Extension of the NodeCommon.ts</h3>

The NodeCommon.ts component is extended with attributes that set the hierarchical attributes of a node, namely a parent node, child nodes, a [hierarchical group](#hierarchical-group-glossary), [hierarchical level](#hierarchical-level-glossary) //TODO ?getters? and their getters.

<h3 id="extension-of-the-node-group">Extension of the NodeGroup.ts</h3>

The *remove* method is changed to handle node hierarchy.

<h3 id="extension-of-the-node-view">Extension of the NodeView.ts</h3>

The *expand* method is changed to handle [child-parent/parent-child relation layout constraint](#child-parent-or-parent-child-layout-constraint). At the moment, the implementation only supports [child-parent relationships](#parent-child-or-child-parent-hierarchical-relationship-glossary), but it's easy to add support for [parent-child](#parent-child-or-child-parent-hierarchical-relationship-glossary) as well.

<h3 id="extension-of-the-view-options">Extension of the ViewOptions.ts</h3>

This component is extended with the *isHierarchyView* attribute to indicate if the visual style of the parent node needs to be changed. See [Extension of the GraphAreaStylesheetMixin.ts](#extension-of-the-graphareastylesheetmixin) for more details.

<h3 id="extension-of-the-cola-layout">Extension of the ColaLayout.ts</h3>

The *onExpansion* method is changed to handle node hierarchy.

<h3 id="extension-of-the-layouts">Extension of the Layouts.ts</h3>

The Layouts.ts and all its descendant components are extended with boolean *supportsHierarchicalView* and *constraintRulesLoaded* attributes. The first one indicates whether the layout supports a hierarchical view. The *constraintRulesLoaded* attribute indicates whether layout constraints were successfully loaded.

<h3 id="extension-of-the-layout-manager">Extension of the LayoutManager.ts</h3>

The *switchToLayout* method is extended to handle the case when the current layout is changed to a different one, so that all loaded constraint rules are preserved.

<h3 id="extension-of-the-remote-server">Extension of the RemoteServer.ts</h3>

This component is extended with the requestor, which receives layout constraints from the backend server.

<h3 id="extension-of-the-response-interfaces">Extension of the ResponseInterfaces.ts</h3>

This component is extended with a new interfaces used for layout constraints received from the backend server.

<h3 id="grouping-of-clusters-KCluster">New KCluster.ts component</h3>

The new KCluster.ts component is added to the main application. This component contains a groupingOfClusters() method which performs a clustering and grouping of chosen nodes. As a parameter it accepts a set of nodes filtered in [groupingOfClustersManager()](#extension-of-the-graph-area-manipulator) method.

> Grouping of clusters is the process of creating clusters of nodes with the same or similar attribute values ​​and then combining them into a single group.

The "grouping of clusters" algorithm must first cluster the nodes into a [cluster](#cluster-glossary), and then collapse this [cluster](#cluster-glossary) into a single group node. Which nodes to cluster and then group into a single group node is determined by an algorithm using positions of the nodes. This algorithm uses well-known clustering methods: k-Means clustering [1] and k-Medoids clustering [2] (the method used is set by the technician).

The basic approach of the algorithm is that it creates several centroids, combines them into an empty group (k-Means clustering [1]) or into a group consisting of a single node (k-Medoids clustering [2]), and then adds surrounding nodes to the closest group.

The groupingOfClustersManager() method is explained in more detail in the code comments. See implementation of [groupingOfClustersManager()](https://github.com/Razyapoo/knowledge-graph-browser-frontend/blob/master/src/cluster/clusters/KMeans/KMeans.ts) for more details.




// TODO
- zoom in + clustering (scale, onZoom)
- to change zoom in/ zoom out in grouping of clusters description
- 