# PG Format

## Background

* There are many **graph databases** such as Neo4j, Oracle PGX, Amazon Neptune, ...
* Different graph databases use different **property graph formats**.
* We define generic **property graph (PG)** format and converters for various formats.
* [[PG Comparison]] page describes the difference from other formats (GML, GraphSON, ..).

## Specifications

* A PG file consists of lines that describe nodes and edges.
* Each line describes one node or one edge.

Example:

```
# NODES
101  :person  name:"Barack Obama"  country:"United States"
102  :person  name:"Shinzo Abe"  country:Japan

# EDGES
101  104  :admires  score:20.0  since:2015
102  103  :collaborates  since:2010
```

### Node lines

```
<node_id>  :<label>  <prop1>:<value1>  <prop2>:<value2>  ...
```

* All elements are separated by space or tab.
* Node IDs have to be unique.
    * If there are multiple lines with the same Node ID, latter ones are ignored.
* Each line can contain arbitrarily many properties.

Example:

```
# NODES
101  :person  name:"Barack Obama"  country:"United States"
102  :person  name:"Shinzo Abe"  country:Japan
```

### Edge lines

```
<src_node_id>  <dst_node_id>  :<label>  <prop1>:<value1>  <prop2>:<value2>  ...
```

* Basically, the same format as node lines.
* However, the first two columns are a source node and a destination node.
* The combinations of Node IDs do NOT have to be unique. (multiple edges)
    * If even one of Node IDs is not defined in a node line, this edge line will be ignored.

Example:

```
# EDGES
101  104  :admires  score:20.0  since:2015
102  103  :collaborates  since:2010
```

### Datatype

Each element can be the following datatypes:

* Node ID: integer or string
* Label: string
* Prop name: string
* Prop value: integer, double, or string

The values in each datatype can be written as follows:

* integer: number which does NOT contain period
* double: number which contains period
* string: double quoted if contains space, tab, or colon (:)
    * To escape double quote, use \\"
