# G2GML

G2GML is a RDF Graph to Property Graph Mapping Language.

## Overview

* G2GML defines mapping from RDF graphs to property graphs.
* Mapping is described with a set of pairs of **RDF graph patterns** and **property graph patterns**.
* RDF graph patterns are written in WHERE clause syntax of **SPARQL**, while the property graph patterns are written in MATCH clause syntax of **Cypher**.

`sample.g2g`

    <prefix>
    
    <property graph patterns>           <-- Cypher MATCH clause syntax
        <semantic graph patterns>       <-- SPARQL WHERE clause syntax
    
    ...

## Minimal Examples

* RDF resource > PG node
* RDF datatype property > PG node property
* RDF object property > PG edge

### RDF resource > PG node

`mini-01.ttl`

    @prefix : <http://example.org/> .
    :person1 a :Person .


`mini-01.g2g`

    PREFIX : <http://example.org/>
    (p:Person)                          <-- PG node is defined
        ?p a :Person .


`mini-01.pg`

    "http://example.org/person1"	 :"Person"


### RDF datatype property > PG node property

`mini-02.ttl`

    @prefix : <http://example.org/> .
    :person1 a :Person .
    :person1 :age 30 .

`mini-02.g2g`

    PREFIX : <http://example.org/>
    (p:Person {age:a})                 <-- PG node property is defined
        ?p a :Person .
        ?p :age ?a .

`mini-02.pg`

    "http://example.org/person1"	 :"Person"	"age":30

### RDF object property > PG edge

`mini-03.ttl`

    @prefix : <http://example.org/> .
    :person1 a :Person .
    :person2 a :Person .
    :person1 :follows :person2 .

`mini-03.g2g`

    PREFIX : <http://example.org/>
    (p:Person)
        ?p a :Person .
    (p1:Person)-[:follows]->(p2:Person)       <-- PG edge is defined
        ?p1 :follows ?p2 .

`mini-03.pg`

    "http://example.org/person1"	 :"Person"
    "http://example.org/person2"	 :"Person"
    "http://example.org/person1"	->	"http://example.org/person2"	:follows

## Actual Examples

`musician.g2g`

    PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
    PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
    PREFIX prop-ja: <http://ja.dbpedia.org/property/>
    PREFIX schema: <http://schema.org/>
    PREFIX dbpedia-owl: <http://dbpedia.org/ontology/>
    PREFIX foaf: <http://xmlns.com/foaf/0.1/>

    # Node mappings
    
    (mus:Musician {vis_label:nam, born:dat, hometown:twn, pageLength:len})
        ?mus rdf:type foaf:Person, dbpedia-owl:MusicalArtist .
        ?mus rdfs:label ?nam .
        FILTER(lang(?nam) = "en") .
        OPTIONAL { ?mus prop-ja:born ?dat }
        OPTIONAL { ?mus dbpedia-owl:hometown / rdfs:label ?twn }
        OPTIONAL { ?mus dbpedia-owl:wikiPageLength ?len }

    # Edge mappings
    
    (mus1:Musician)-[:same_group {label:nam, hometown:twn, pageLength:len}]-(mus2:Musician)
        ?grp a schema:MusicGroup ;
             dbpedia-owl:bandMember ?mus1 , ?mus2 .
        FILTER(?mus1 != ?mus2)
        OPTIONAL { ?grp rdfs:label ?nam. FILTER(lang(?nam) = "en")}
        OPTIONAL { ?grp dbpedia-owl:hometown / rdfs:label ?twn }
        OPTIONAL { ?grp dbpedia-owl:wikiPageLength ?len }

    (mus1:Musician)-[:influenced]-(mus2:Musician)
        ?mus1 dbpedia-owl:influenced ?mus2 .
