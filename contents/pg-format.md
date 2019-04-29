# PG Format

## Overview

* There are many **graph databases** such as Neo4j, Oracle PGX, Amazon Neptune, ...
* Different graph databases use different **property graph definitions and formats**.
* We define the **property graph (PG)** data model and its serialization (PG and JSON-PG).
* We also provide its converter for various data formats.

## PG (Flat File)

* A PG file consists of lines that describe nodes and edges.
* Each line describes one node or one edge.

`example.pg`

    # NODES
    101  :person  name:Alice  country:"United States"
    102  :person  :student  name:Bob  country:Japan

    # EDGES
    101  -- 102  :same_school  :same_class  since:2012
    101  -> 102  :likes  since:2015

### Nodes

    <node_id>  :<label1>  :<label2>  ...  <key1>:<value1>  <key2>:<value2>  ...

* All elements are separated by space or tab.
* **Node IDs** have to be unique.
    * If there are multiple lines with the same Node ID, latter ones are ignored.
* Each line can contain arbitrarily many **labels**.
* Each line can contain arbitrarily many **properties**.

### Edges

    <src_node_id> [->|--] <dst_node_id>  :<label1>  :<label2>  ...  <key1>:<value1>  <key2>:<value2>  ...

* Basically, edge lines have the same format as node lines.
* However, the first three columns contain **source node ID**, **direction**, and **destination node ID**.
* An edge can be directed `->` or undirected `--`.
* **The combinations of node IDs** do NOT have to be unique. (= multiple edges are allowed.)
    * An edge line will be ignored if a non-defined node ID is used.

### Data type

Each element can have one of the following data types:

* Node ID: integer or string
* Label: string
* Property key: string
* Property value: integer, double, or string

The values in each data type can be written as follows:

* integer: Numbers which do NOT contain a period
* double: Numbers which contain a period
* string: Anything else
    * Should be double quoted if it contains a space, tab, or colon (`:`)
    * To escape double quotes, use `\"`

## JSON-PG

JSON format is useful when it is processed by web clients, while the PG (flat file) format above is convenient for users and file systems. 

This format basically follows the rules of the general JSON format and our PG format, however:

* **Nodes and edges** are listed under `nodes` and `edges` elements, respectively.
* **Edge direction** is defined with the boolean element `undirected`. By default it is `false` (= directed).
* **Labels** are listed under the `labels` element.
* **Properties** (= key-value pairs) are listed under the `properties` element.

`example.json`

    {
      "nodes":[
        {"id":101, "labels":["person"], "properties":{"name":"Alice", "country":"United States"}}
      , {"id":102, "labels":["person", "student"], "properties":{"name":"Bob", "country":"Japan"}}
      ],
      "edges":[
        {"from":101, "to":102, "undirected":true, "labels":["same_school", "same_class"], "properties":{"since":2012}}
      , {"from":101, "to":102, "labels":["likes"], "properties":{"since":2015}}
      ]
    }

## Comparison

PG

    # NODES
    101  :person  name:Alice  country:"United States"
    102  :person  :student  name:Bob  country:Japan

    # EDGES
    101  -- 102  :same_school  :same_class  since:2012
    101  -> 102  :likes  since:2015

JSON-PG

    {
      "nodes":[
        {"id":101, "labels":["person"], "properties":{"name":"Alice", "country":"United States"}}
      , {"id":102, "labels":["person", "student"], "properties":{"name":"Bob", "country":"Japan"}}
      ],
      "edges":[
        {"from":101, "to":102, "undirected":true, "labels":["same_school", "same_class"], "properties":{"since":2012}}
      , {"from":101, "to":102, "labels":["likes"], "properties":{"since":2015}}
      ]
    }

