# Grouping of clusters

_This page describes how the grouping methods are implemented in the Knowledge Graph Browser and how to configure them._

---
//TODO add table of content 

## Main goal

So far, in our Knowledge Graph Browser, it was only possible to group nodes in such a way that the user selects a couple of nodes and makes a group out of them manually. But in many cases it is more efficient and easier for the user to escape such work and allow the application to automatically group nodes based on similar attribute values.

The main purpose of the implemented grouping method is to make a large graph more user-friendly, i.e. more readable and understandable. Node grouping is the process of creating clusters of nodes with the same or similar attribute values ​​and then combining them into a single group.

### Node hierarchy and hierarchical relations

Typically, nodes in a graph are related to each other, for example, a company has employees, university has scientists, scientist has awards, scientist writes scientific papers, university has departments, and many other examples. In what follows, for simplicity, we will consider universities and departments as an example, and relation between them will be - "university has department(s)".

One of the possible ways to visualize such relations is to add a hierarchy between entities, that is, in our example, the university acts as a parent node, and its departments act as child nodes. In such case, "university has department(s)" relation is hierarchical one. In our Knowledge Graph Browser application university can be visualized as a big node containing inside child nodes representing departments as shown at the picture below: //TODO add picture

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

### Child-parent or parent-child relations

These relations have a special place in our grouping of clusters extension of the Knowledge Graph Browser because they establish how hierarchical groups of nodes can be visualized. Expansion query can be triggered from a parent node (expand child nodes) as well as form a child node (expand parent node). In both cases it may use same property in SPARQL CONSTRUCT, for example *skos:broader*.

Layout constraint defining child-parent (resp. parent-child) relations is expressed as an instance of the *browser:ChildParentLayoutConstraint* (resp. *browser:ParentChildLayoutConstraint*). Classes playing a child role (resp. parent role) are assigned to the *browser:ChildParentLayoutConstraint* (resp. *browser:ParentChildLayoutConstraint*) class using the *browser:childNodeSelector* (resp. *browser:parentNodeSelector*) property. Expansion property in expansion query is selected using the *browser:hierarchyEdgeSelector*.

### Hierarchical groups to cluster

It can be useful to group together nodes that belong to the same hierarchical group (but within that group). 

The layout constraint that defines the hierarchical groups for clustering is expressed as an instance of the *browser:HierarchyGroupToClusterLayoutConstraint* class. The hierarchical group class is assigned using the *browser:clusteringSelector* property. 

**Warning** Each hierarchical group class must be assigned to a separate instance of the *browser:HierarchyGroupToClusterLayoutConstraint* class. The whole process of grouping nodes is written in //TODO (add a link).
 

### Classes to cluster together

It is possible to define which classes can be clustered and grouped together. By default, in our grouping algorithm, only nodes of the same visual and hierarchical classes are clustered and grouped together.

The layout constraint that defines classes to cluster and group together is expressed as an instance of the *browser:ClassesToClusterTogetherLayoutConstraint* class. Classes are assigned using the *browser:clusteringSelector* property.

**Warning** Use this layout constraint only if you want to create groups containing nodes of different classes. Put all these classes that you want to cluster and group together in one instance of the *browser:ClassesToCluster Together Layout Constraint* class.

### Implementation

See the implementation for [backend configuration]()

---

## Frontend script

Our extension of the original Knowledge Graph Browser is inspired by maps like google maps, maps.cz, etc. When you zoom in, at each zoom level, you see more and more details about the region you zoom in, and also otherwise, when you zoom out, some details disappear and more regions are shown on the map itself.

A similar principle is used in our extension, namely when you zoom out, 


### Extension of the GraphArea component

Original GrpahArea component is extended with a checkbox that let the user choose whether to do zoom

