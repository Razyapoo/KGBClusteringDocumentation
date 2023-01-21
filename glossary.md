<h1 id="glossary">Glossary</h1>

In this section of the guide, you will be introduced to key concepts and terminology that are fundamental to understand how the extension works. These terms may differ from those commonly used in other context.

<h3 id="compound-node">Compound node</h3>

Compound nodes are used to represent hierarchical relationships. This way the parent node is represented as a container node that includes the child nodes inside.

Compound nodes can be used to group inner nodes together, creating a hierarchical structure where the parent node represents the group as a whole and the child nodes represent individual elements within the group.

<h3 id="supernode">Supernode</h3>

Supernode is used to represent a collapsed compound node. The list of collpased child nodes is available in the detail panel.   

<h3 id="superedge">Superedge</h3>

Superedge is used to represent a compressed set of edges. Each superedge represents a set of edges that are of the same type.   

<h3 id="group-compact-mode">Group compact mode</h3>

Group compact mode is a feature that allows to view the structure of a group and recursively explore its inner groups without cluttering the graph. 

The goal of this mode is to make it easier for the user to understand the structure of the group and navigate through it without being overwhelmed by unnecessary information.

> **Note**
> This mode is only available for group nodes. 

<h3 id="hierarchical-relationships-glossary">Hierarchical relationship</h3>

Hierarchical relationships have a parent-child structure and are represented using [compound nodes](#compound-node). 

Figure 1 below shows an example with Animal classification:

<p align="center">
    <img src="img/child_parent_relation.png" alt="parent-child-relationship" title="Parent-child relationship" width="400"/><br/>
    <em>Figure 1. Parent-child relationship</em>
</p>

Here the node "Chordata" represents a compound node, containing inside "mammal" child node, which, in turn, is a compound node containing the "(72) Order" group. 

Each such multi-level hierarchy represents a [hierarchical group](#hierarchical-group-glossary).

> **Warning** \
> Hierarchical relationships are predefined by a technician in the visual configuration file. It is not possible for the user to define whether a relationship should be hierarchical within the user interface.

[Non-hierarchical](#non-hierarchical-relationships-glossary) relationships are also possible. 

<h3 id="non-hierarchical-relationships-glossary">Non-hierarchical relationship</h3>

> **Definition** \
> Non-hierarchical relationships refer to relationships that are not represented using parent-child structure. Instead, these relationships are typically represented by edges between nodes in a graph. 

<h3 id="hierarchical-class-glossary">Hierarchical class</h3>

> The hierarchical class is a visual class that defines which [hierarchical group](#hierarchical-groups-glossary) a node belongs to in hierarchical structure. A node can only be assigned to one hierarchical class.

This class is used to group nodes together in a hierarchical manner. If a node belongs to some hierarchical class, the class will be displayed along with the label of the node in the detail panel. See Figure 3 below for more details.

<p align="center">
    <img src="img/hierarchical_class.png" alt="hierarchical-class" title="Hierarchical class" width="350"/><br/>
    <em>Figure 3. Hierarchical class</em>
</p>

> **Note** \
> This class is common for all the nodes that are placed in the same [hierarchical group](#hierarchical-groups-glossary). \
> It is necessary for a node to be assigned to a hierarchical class in order for it to be included in a hierarchical group.

<h3 id="hierarchical-level-glossary">Hierarchical level</h3>

> **Definition** \
> The hierarchical level of a node indicates the depth of a hierarchy at which a node resides. For example, root node resides at level 0 and its children reside at level 1 and so on. 

This concept is similar to how the amount of detail displayed on a map  changes based on the zoom level. Our approach uses this same idea, where at the deepest level of the hierarchy, the graph displays all possible details, while at the highest level of the hierarchy, the graph only shows the representative nodes that make up the hierarchy. 

This allows for an easy way to navigate through large and complex networks, by showing different level of detail based on the hierarchical level of nodes.

<h3 id="current-hierarchical-level-glossary">Current hierarchical level</h3>

> **Definition** \
> The current hierarchical level refers to the specific [level of hierarchy](#hierarchical-level-glossary) that is currently being focused on. 

This level can also be described as the "zoom level" where the level of detail displayed in the graph changes as the user zoom in or zoom out. 
For example, when zooming out, the current hierarchical level decreases as child nodes collapse into their parents, resulting in a higher level of abstraction. Conversely, when zooming in, the current hierarchical level increases as child nodes with a deeper level in the hierarchy become visible, providing more detail.  

<h3 id="hierarchical-groups-glossary">Hierarchical group</h3>

> **Definition** \
> Hierarchical groups are clusters of nodes that are related to each other through [hierarchical relationships](#hierarchical-relationships-glossary).
 
Each node within a hierarchical group must be assigned to a specific [hierarchical class](#hierarchical-class-glossary), which represents that hierarchical group.

An example of a hierarchical group is shown in Figure 1 above.

> **Warning** \
> Hierarchical groups are predefined by a technician in the visual configuration file and cannot be defined by the user within the user interface. 

<h3 id="visual-group-glossary">Visual group</h3>

> **Definition** \
> A visual group is a type of compound node that is used to visually represent a cluster of similar nodes, regardless of the type of the relationships present between them.

For example, in Figure 4, "pseudo-parent" (grey) node is used as visual element to encapsulate and represent the nodes of a specific type. 

<p align="center">
    <img src="img/visual_group.png" alt="visual-group" title="Visual group" width="300"/><br/>
    <em>Figure 4. Visual group</em>
</p>

> **Warning** \
> Visual groups are predefined by a technician in the visual configuration file and cannot be defined by the user within the user interface. 

Each node within a visual group must be assigned to a specific visual class, which represents that visual group.

> **Note** \
> In the context of a hierarchical group, the parent node serves as the visual element encapsulating and representing the cluster of child nodes. This eliminates the need for a separate "pseudo-parent" node for a hierarchical group. 

An example of hierarchical group "regionVisualGroup" and visual group "animalHierarchicalGroup" is shown in Figure 5 below.

<p align="center">
    <img src="img/visual_groups.png" alt="visual-groups" title="Visual groups" width="600"/><br/>
    <em>Figure 5. Visual groups. Above is "animalHierarchicalGroup" visual group and to below is "regionVisualGroup" visual group</em>
</p>

Visual groups make the graph more manageable and intuitive for the user by grouping similar nodes together. They allow to easily move the cluster, avoiding the scattering of related nodes around the graph area.

<h3 id="cluster-glossary">Cluster</h3>

> **Definition** \
> A cluster is a collection of similar elements that are situated near one another. 

<h3 id="grouping-glossary">Grouping</h3>

> **Definition** \
> Grouping is the task of converting clusters into a single node.

<h3 id="checkbox-glossary">Checkbox</h3>

The "Scaling options" checkbox is located in the top right corner of the graph area and allows the user to select whether to group clusters globally, or locally, zoom in/out, or combine these options. See Figure 6 below for more detail.

<p align="center">
    <img src="img/scaling_options.png" alt="scaling-options" title="Scaling options" width="200"/><br/>
    <em>Figure 6. Scaling options</em>
</p>

<h3 id="zooming-glossary">Zooming</h3>

Map-style zooming, as it relates to the Grouping of Clusters extension, refers to the ability to adjust the magnification and level of detail displayed on the graph at the same time, allowing to focus on the specific data of interest and make the graph more manageable and intuitive.  

The level of detail is controlled through the use of [compound nodes](#compound-node), [supernodes](#supernode)/[superedges](#superedge), and [groups](#grouping-glossary). By utilizing these elements, certain areas of the graph can be selectively expanded or collapsed to reveal or hide specific details. 

There are two types of map-style zooming, namely global and local. Global zooming allows for zooming in or our on the entire graph, while local zooming allows for zooming only on specific selected nodes. Map-style zooming is achieved by combination of traditional zooming, which changes the magnification, with any variation of grouping of clusters zooming. 