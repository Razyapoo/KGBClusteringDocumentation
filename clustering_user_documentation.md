### Table of Contents

- [Motivation](#motivation)
- [Get started](#GetStarted)

---
<h2 id="motivation">Motivation</h2>

We all know how zoom in/out works on mapping platforms such as [google maps](https://maps.google.com), maps.cz, etc. Zoom is used to increase or decrease the zoom level at a specific point and show more or less detail on a map.

Our extension of the original Knowledge Graph browser is inspired by such mapping platforms.

<h2 id="get-started">Get started</h2>

This guide will explain and teach you how the clustering and grouping extension works.

<h3 id="hierarchical-relationships">Hierarchical relationships</h3>

In our approach, we introduce the concept of hierarchical relationships. 

Typically, nodes in a graph are related to each other, for example, a company has employees, university has scientists, scientist has awards, scientist writes scientific papers, university has departments, and many other examples. In what follows, for simplicity, we will consider universities and departments as an example, and relation between them will be - "university has department(s)".

One possible way to visualize such relationships is to create an edge between nodes. But there is another way, namely adding a hierarchy between the nodes, that is, in our example, the university acts as a parent node, and its departments act as child nodes. In such case, "university has department(s)" relation is hierarchical one, i.e. representing a *"parent-child"* relationship.

See the picture below for an example:

<p align="center">
    <img src="img/child_parent_relation.png" alt="parent-child-relationship" title="Parent-child relationship" width="600"/><br/>
    <em>Parent-child relationship</em>
</p>

Here the node *"Fakulty"* plays the role of the parent node, namely the node *"Matematicko-fyzikální fakulta"*, which, in turn, is the parent of the internal nodes that are light-blue and have titles inside the node.

<h3 id="hierarchical-groups">Hierarchical groups</h3>

> A hierarchical group is a cluster of nodes that are related to each other by parent-child relationships. 

In the picture above we have show 

Hierarchical group is predefined in the visual configuration by the technician.





## Useful links

[1] https://www.geeksforgeeks.org/google-maps-zoom/

