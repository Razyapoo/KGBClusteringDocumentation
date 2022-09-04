# Grouping of clusters

_This page describes how the grouping methods are implemented in the Knowledge Graph Browser and how to configure them._

---
//TODO add table of content 

## Main goal

So far, in our Knowledge Graph Browser, it was only possible to group nodes in such a way that the user selects a couple of nodes and makes a group out of them manually. But in many cases it is more efficient and easier for the user to escape such work and allow the application to automatically group nodes based on similar attribute values.

The main purpose of the implemented grouping method is to make a large graph more user-friendly, i.e. more readable and understandable. Node grouping is the process of creating clusters of nodes with the same or similar attribute values ​​and then combining them into a single group.

<h3 id="node-hierarchy-and-hierarchical-relations">Node hierarchy and hierarchical relations</h3>

Typically, nodes in a graph are related to each other, for example, a company has employees, university has scientists, scientist has awards, scientist writes scientific papers, university has departments, and many other examples. In what follows, for simplicity, we will consider universities and departments as an example, and relation between them will be - "university has department(s)".

One possible way to visualize such relationships is to create an edge between nodes. But there is another way, namely adding a hierarchy between the nodes, that is, in our example, the university acts as a parent node, and its departments act as child nodes. In such case, "university has department(s)" relation is hierarchical one. 

In our Knowledge Graph Browser application university can be visualized as a big node containing inside child nodes representing departments as shown at the picture below: //TODO add picture

In our implementation, each such node hierarchy has its own class. Each node has an additional class indicating which hierarchy class it belongs to.

### Non-hierarchical relations

Non-hierarchical relations are also possible. For example, we can extend our example and consider also the relation - "the department teaches the subject". We can show such a relation as hierarchical, but we chose to show it as non-hierarchical. This can be configured in the visual configuration file (it is explained further here //TODO add link). See the picture below: //TODO

### Clustering of nodes based on its classes

Each node in our Knowledge Graph Browser has an entity class or classes to which it belongs, such as the Department or the Subject. This property is used to cluster nodes and combine them into one group. Nodes can be either clustered based on the same class (by default) or a group of classes predefined in the visual configuration file. 

### Visual groups

Let's imagine that a certain class of nodes may not have hierarchical relations, but we still want to cluster them. For example, the subjects do not have a hierarchical relations, but we want them all to be clustered in the same place on the graph. In such a case, we can set up the visual group in the visual configuration so that all nodes of the "Subject" class are placed under one "pseudo-parent" node representing the visual group "Subject". See an example below: //TODO picture



















Implementation of grouping method is split into two parts: 
1. [Backend](#Backend)
2. [Fronted](#Frontend)

--- 

# Backend

## Backend service

The original backend service is extended with a new request handler *"/layout-constraints"*.

This handler sends a query to a SPARQL endpoint and receives layout constraints to apply in the main application.

The output of the request handler is a JSON object containing all the constraints.

### Implementation

See the implementation for [backend service]()

## Visual configuration

The original visual configuration is extended with a set of layout constraints that is assigned using the *browser:hasLayoutConstraints* property. 

A layout constraint set is a set of visual layout constraints to be applied to a graph. It is expressed as an instance of the *browser:LayoutConstraintSet* class. Individual layout constraints are instances of a special layout constraint classes and are assigned to a set of layout constraints using the *browser:hasConstraint* property. 

The next few sections describe each layout constraint class in more detail. It is expected that each node has an additional class indicating which hierarchy class it belongs to. 

### Hierarchical group class

A hierarchical group class is a generic class for all nodes to be placed in the same hierarchical group.

A hierarchical group class is assigned to a node in the SPARQL CONSTRUCT query in the same way as a visual class, i.e. using the *browser:class* property. 

**Warning** Each node must be assigned to some hierarchical group class in case it is to be placed in any hierarchy.

### Visual group

Nodes that belong to the same visual group are placed under the same "pseudo-parent" representing the visual group itself. Layout constraint defining visual group is expressed as an instance of the *browser:VisualGroupLayoutConstraint* class. Nodes that are to be placed under the same pseudo-parent node must belong to the same hierarchical group class, i.e. in this case the visual group corresponds to the hierarchical group class. This class is assigned to the visual group layout constraint via *browser:groupingSelector* property. 

**Warning** Each hierarchical group class must be assigned to a separate instance of *browser:VisualGroupLayoutConstraint* class.

<h3 id="child-parent-or-parent-child-relations">Child-parent or parent-child relations</h3>

These relations have a special place in our grouping of clusters extension of the Knowledge Graph Browser because they establish how hierarchical groups of nodes can be visualized. Expansion query can be triggered from a parent node (expand child nodes) as well as form a child node (expand parent node). In both cases it may use same property in SPARQL CONSTRUCT, for example *skos:broader*.

Layout constraint defining child-parent (resp. parent-child) relations is expressed as an instance of the *browser:ChildParentLayoutConstraint* (resp. *browser:ParentChildLayoutConstraint*). Classes playing a child role (resp. parent role) are assigned to the *browser:ChildParentLayoutConstraint* (resp. *browser:ParentChildLayoutConstraint*) class using the *browser:childNodeSelector* (resp. *browser:parentNodeSelector*) property. Expansion property in expansion query is selected using the *browser:hierarchyEdgeSelector*.

<h3 id="hierarchical-groups-to-cluster">Hierarchical groups to cluster</h3>

It can be useful to group together nodes that belong to the same hierarchical group (but within that group). 

The layout constraint that defines the hierarchical groups for clustering is expressed as an instance of the *browser:HierarchyGroupToClusterLayoutConstraint* class. The hierarchical group class is assigned using the *browser:clusteringSelector* property. 

**Warning** Each hierarchical group class must be assigned to a separate instance of the *browser:HierarchyGroupToClusterLayoutConstraint* class. The whole process of grouping nodes is written in //TODO (add a link).
 

<h3 id="classes-to-cluster-together">Classes to cluster together</h3>

It is possible to define which classes can be clustered and grouped together. By default, in our grouping algorithm, only nodes of the same visual and hierarchical classes are clustered and grouped together.

The layout constraint that defines classes to cluster and group together is expressed as an instance of the *browser:ClassesToClusterTogetherLayoutConstraint* class. Classes are assigned using the *browser:clusteringSelector* property.

**Warning** Use this layout constraint only if you want to create groups containing nodes of different classes. Put all these classes that you want to cluster and group together in one instance of the *browser:ClassesToCluster Together Layout Constraint* class.

### Implementation

See the implementation for [backend configuration]()

---

## Frontend script

Our extension of the original Knowledge Graph Browser is inspired by mapping platforms like [google maps](https://maps.google.com), maps.cz, etc. When you zoom in, at each zoom level, you see more and more details about the region you zoom in, and also otherwise, when you zoom out, some details disappear and more regions are shown on the map itself.

Our extension uses a similar principle, that is, when you zoom in, you see more detail, and when you zoom out, you see less detail. 

The grouping of nodes is determined based on the class of the hierarchical group, the parent node, the level of the hierarchy in which it is placed, and the visual class. First of all, the nodes must be grouped by the hierarchical group class to which they belong, because as the map scales down and details disappear, new correlated details appear in their place that generalize the disappeared details. In our case, the parent node is such a generalization. Therefore, the second condition of the grouping algorithm must be the grouping of nodes that have the same parent node.

The amount of detail displayed on the maps depends on the zoom level. Our implementation uses the same idea. At the deepest level of the hierarchy, the graph shows all possible details, and as you zoom out, it generalizes the details to the parent nodes and shows less detail in the graph. And at the highest level of the hierarchy, the graph shows only the nodes that represent the hierarchies themselves.

As described in the [Classes to cluster together](#classes-to-cluster-together) section, multiple different visual classes can be grouped together, and the default is that only nodes of the same visual class can be grouped together. Therefore, when grouping nodes, their visual classes must also be taken into account.

When switching between zoom levels and therefore hierarchy levels, the graph shows more or less detail in relation to the number of nodes. In other words, child nodes become their parents. They only disappear, but still exist on the graph (in other words, they are not mounted in the hierarchy, but still mounted in the graph). Therefore, it is necessary to preserve their incoming and outgoing edges. This is done by moving their edges towards their parents. That is, when all child nodes disappear and it's time to show only the parent node, all their edges move to the parent node.

The next few sections will describe extensions to main code components.

<h3 id="extension-of-the-grapharea-component">Extension of the GraphArea component</h3>

The original GraphArea component is extended with a checkbox that allows the user to choose whether to scale or group. 

### Extension of the GraphAreaStylesheetMixin

The original component is extended with visual styles for the parent node. When a node becomes a parent, its label is moved to the node's top and center position. Also, the shape of the node becomes octagonal.

### Extension of the GraphElementEdges

Because the visualization of the parent-child relationship is implemented as described in [Node hierarchy and hierarchical relations](#node-hierarchy-and-hierarchical-relations) section, creating an edge between parent and child nodes is redundant. Therefore, the creation of an edge is subject to the condition that the nodes in the expansion are not in a parent-child relationship with the expanded nodes.

### Extension of the GraphElementNodeMixin

There is an issue with parent-child hierarchical visualization in that when a child node is selected, the parent node is also selected because it is below the child node. Therefore, it is necessary to make the parent node unselectable when the child node is selected, and then, after the child node is already selected, make the parent node selectable again.

The Cytoscape library uses the "parent" property define a parent of a node and  visualize a parent-child hierarchy (i.e. a parent node is shown as a large node and its child nodes as nodes placed inside the parent node). Therefore, when expanding a parent node from a child node, it is necessary to explicitly specify the parent for a Cytoscape element that representing a child node and might already be present in the graph.

<h3 id='extension-of-the-configuration'>Extension of the Configuration.ts</h3>

The original component is extended with new attribute *constraints* in which the loaded constraints are stored in the configuration.

<h3 id='extension-of-the-application'>Extension of the Application.vue</h3>

It is extended with a new feature to query and retrieve layout constraints from the server.

<h3 id='extension-of-the-edge'>Extension of the Edge.ts</h3>

Edge is extended with a new attribute showing that the edge is coming from the children.

<h3 id='extension-of-the-graph-area-manipulator'>Extension of the GraphAreaManipulator.ts</h3>

The original component is extended with a new private *clustering* method that performs clustering and grouping of nodes. It is also extended with the *globalHierarchyDepth* attribute, whose value indicates the current depth of the hierarchy.

When grouping, the *clustering* method filters all nodes that have a hierarchical group class that is allowed to be grouped in the visual configuration (more in [Hierarchical groups to cluster](#hierarchical-groups-to-cluster) section). The algorithm then filters out of all previously filtered nodes only those that are at the deepest level of the hierarchy shown in the graph (based on the value of the *globalHierarchyDepth* attribute). 

**Warning** Value of the *globalHierarchyDepth* attribute does not show the deepest hierarchical level that has been achieved in the graph, but the deepest level of nodes that are still visible in the graph.

From now on, the algorithm groups all filtered nodes, but based on the parent and visual classes. First, it filters all nodes that have the same parent, then from the filtered nodes, it filters out nodes that have the same visual class, unless multiple classes are explicitly set in the visual configuration (more in [Classes to cluster together](#classes-to-cluster-together) section).

Two cases can occur during grouping. In the first case, at the end of the filtering, there are several nodes that can be grouped. The algorithm then calls the *kClustering* function, which performs the grouping of the filtered nodes. This function is described in more detail in the //TODO section.

In the second case, only one node can remain at the end of the filtering, which means that the parent contains only one child. This child node can represent a single node or a group containing all of the parent's child nodes. In this case, the remaining child node should be collapsed into the parent node, but this should only happen when all the nodes (or groups) having hierarchical level equal to *globalHierarchyDepth* value are single child nodes of their parents. If there are still pair of nodes that can be grouped, the algorithm simply remembers that in the future it must collapse single child nodes, and groups this pair of nodes. In the case where each node that has a depth equal to *globalHierarchyDepth* is the only node of its parent, the algorithm switches the hierarchy level one level higher (value of the *globalHierarchyDepth* attribute plus one) and collapses the child nodes into their parents. During this operation, all edges from child nodes are moved to the parent node.

When ungrouping, only nodes of the same hierarchical level can be ungrouped (nodes shown in the graph and having a hierarchical level equal to the value of the *globalHierarchyDepth* attribute). Here again there are two cases. The first case, when there is at least one group at a level equal to *globalHierarchyDepth*, then this group can be ungrouped and the algorithm stops its execution. In the second case, there can only be single nodes representing the parents of the nodes that disappeared during the grouping operation. This can be verified using the *isMountedInHierarchy* attribute of a node (more detail in the [Extension of the NodeCommon.ts](#extension-of-the-node-common)). Thus, nodes that are not mounted in the hierarchy but are mounted in the graph (i.e. they were expanded and then collapsed to a parent node during a grouping operation in the past) appear in the graph as child nodes and their *isMountedInHierarchy* attribute value takes the value *true*.

<h3 id="extension-of-the-graph-manipulator">Extension of the GraphManipulator.ts</h3>

In this component, only methods for managing groups are extended, namely, setting the parent, the level of the hierarchy to which the group or node belongs, and the hierarchical group. But make sure that when you ungroup a group, you need to remove that group from its parent's list of children, and otherwise, when you group a nodes, you need to remove them form its parent's list of children, but add newly created group as a child node.

<h3 id="extension-of-the-node">Extension of the Node.ts</h3>

When deleting a node, you must also delete all its descendant nodes in the hierarchy, as this may violate the principle of the hierarchy.

An extension of this component is the *remove* method, which is extended to handle the recursive removal of child nodes (including nodes and groups).

<h3 id="extension-of-the-node-common">Extension of the NodeCommon.ts</h3>

The NodeCommon.ts component is extended with attributes that allow you to set the hierarchy of nodes, namely *parent*, *children*, *hierarchyGroup*, *hierarchyLevel* and their getters.

To check if a node has been expanded and not disappeared during the grouping operation, a new *isMountedInHierarchy* attribute has also been added to indicate if the node is collapsed inside its parent.

<h3 id="extension-of-the-node-group">Extension of the NodeGroup.ts</h3>

*remove* method is changed to handle node hierarchy.

<h3 id="extension-of-the-node-view">Extension of the NodeGroup.ts</h3>

*expand* method is changed to handle *child-parent/parent-child relation* layout constraint (described in more detail in [Child-parent or parent-child relations](#child-parent-or-parent-child-relations) and [Node hierarchy and hierarchical relations](#node-hierarchy-and-hierarchical-relations)) section. At the moment, our implementation only supports child-parent relationships, but it's easy to add support for parent-child relationships as well.

<h3 id="extension-of-the-view-options">Extension of the NodeGroup.ts</h3>

The NodeCommon.ts component is extended with the *isHierarchyView* attribute to indicate if the visual style of the parent node needs to be changed. When at least one child node is collapsed within a parent node, its visual style changes so that its label appears at the top and center.

<h3 id="extension-of-the-cola-layout">Extension of the ColaLayout.ts</h3>

*onExpansion* method is changed to handle node hierarchy.

<h3 id="extension-of-the-layouts">Extension of the Layouts.ts</h3>

Layouts.ts and all its descendant components are extended with *supportsHierarchicalView* and *constraintRulesLoaded* attributes. The first one indicates whether the layout supports a hierarchical view. The *constraintRulesLoaded* attribute indicates whether layout constraints were successfully loaded.

<h3 id="extension-of-the-layout-manager">Extension of the LayoutManager.ts</h3>

*switchToLayout* method is extended to handle the case when current layout is changed to another one.

<h3 id="extension-of-the-remote-server">Extension of the RemoteServer.ts</h3>

The RemoteServer.ts component is extended with the requestor receiving layout constraints from the backend server.

<h3 id="extension-of-the-response-interfaces">Extension of the ResponseInterfaces.ts</h3>

The ResponseInterfaces.ts component is extended with a new interfaces used for layout constraints received from the backend server.




//TODO kClustering