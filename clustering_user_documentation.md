### Table of Contents

- [Motivation](#motivation)
- [Glossary](#glossary)
- [Glossary](#how-to-use-the-extension)

---
<h2 id="motivation">Motivation</h2>

We all know how zoom in/out works on mapping platforms such as [google maps](https://maps.google.com), maps.cz, etc. Zoom is used to increase or decrease the zoom level at a specific point and show more or less detail on a map.

Our extension of the original Knowledge Graph browser is inspired by such mapping platforms.

<h2 id="glossary">Glossary</h2>

In this part of the guide, you will learn the necessary terms that will help you understand the basic principle of how our extension works.

<h3 id="hierarchical-relationships">Hierarchical relationships</h3>

In our approach, we introduce the concept of hierarchical relationships. 

Typically, nodes in a graph are related to each other, for example, a company has employees, university has scientists, scientist has awards, scientist writes scientific papers, university has departments, and many other examples. In what follows, for simplicity, we will consider universities and departments as an example, and relation between them will be - "university has department(s)".

One possible way to visualize such relation is to create an edge between nodes. But there is also another way, namely adding a hierarchy between the nodes, that is, in our example, the university acts as a parent node, and its departments act as child nodes. In such case, "university has department(s)" relation is hierarchical one, i.e. representing a *"parent-child"* relationship.

See the picture below for an example:

<p align="center">
    <img src="img/child_parent_relation.png" alt="parent-child-relationship" title="Parent-child relationship" width="600"/><br/>
    <em>Figure 1. Parent-child relationship</em>
</p>

Here the node *"Fakulty"* is the parent node of the node *"Matematicko-fyzikální fakulta"*, which, in turn, is the parent of the internal nodes that are light-blue and have titles inside the node.

<h3 id="hierarchical-groups">Hierarchical groups</h3>

> A hierarchical group is a cluster of nodes that are related to each other by parent-child relationships. 

Each node in a hierarchical group must have a hierarchical class that represents that hierarchical group.

An example of one such hierarchical group is shown in Figure 1.

The hierarchical group is predefined by the technician in the visual configuration.

<h3 id="visual-groups">Visual groups</h3>

> A visual group is a cluster of nodes located in the same area on a graph.

The visual group is predefined by the technician in the visual configuration.

Each node in a visual group must have a visual class that represents that hierarchical group. It can be the same class as the hierarchical class.

<p align="center">
    <img src="img/visual_groups.png" alt="visual-groups" title="Visual groups" width="600"/><br/>
    <em>Figure 2. Visual groups</em>
</p>

<h3 id="hierarchical-level">Hierarchical level</h3>

> The hierarchical level indicates the depth of the hierarchy at which the node resides.

<h2 id="how-to-use-the-extension">How to use the extension?</h2>

This guide will explain and teach you how the *"clustering and grouping"* extension works.

The Knowledge Graph Browser currently supports only one configuration that allows this extension to be used.

<h3 id="configuration-selection">Configuration selection</h3>

<p align="center">
    <img src="img/configuration_selection.png" alt="configuration-selection" title="Configuration selection" width="600"/><br/>
    <em>Figure 3. Configuration selection</em>
</p>




## Useful links

[1] https://www.geeksforgeeks.org/google-maps-zoom/

