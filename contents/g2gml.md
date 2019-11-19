# G2GML

The Graph To Graph Mapping Language (G2GML) is a language for mapping **[RDF graphs](https://www.w3.org/TR/2014/REC-rdf11-concepts-20140225/)** to **property graphs**.

## Overview
* The mapping is described with a combination of **RDF graph patterns** and **property graph patterns**.
* RDF graph patterns are written in WHERE clause syntax of **SPARQL**, while the property graph patterns are written in MATCH clause syntax of **Cypher**.

`Structure of G2GML`

    <prefixes>
    
    <property graph patterns>           <-- Cypher MATCH clause syntax
        <semantic graph patterns>       <-- SPARQL WHERE clause syntax
    
    ...

## Basic Examples

* RDF resource > PG node
* RDF datatype property > PG node property
* RDF object property > PG edge

### RDF resource > PG node

![](https://user-images.githubusercontent.com/4862919/69172754-b7cf2b80-0afe-11ea-8c93-594a1b2efb66.png)

Input: `mini-01.ttl`

    @prefix : <http://example.org/> .
    :person1 a :Person .


Mapping: `mini-01.g2g`

    PREFIX : <http://example.org/>
    (p:Person)                          <-- PG node is defined
        ?p a :Person .

Output: `mini-01.pg`

    "http://example.org/person1"	 :"Person"


### RDF datatype property > PG node property

![](https://user-images.githubusercontent.com/4862919/69172807-d03f4600-0afe-11ea-8127-d5298b2c0ddf.png)

Input: `mini-02.ttl`

    @prefix : <http://example.org/> .
    :person1 a :Person .
    :person1 :age 30 .

Mapping: `mini-02.g2g`

    PREFIX : <http://example.org/>
    (p:Person {age:a})                 <-- PG node property is defined
        ?p a :Person .
        ?p :age ?a .

Output: `mini-02.pg`

    "http://example.org/person1"	 :"Person"	"age":30

### RDF object property > PG edge

![](https://user-images.githubusercontent.com/4862919/69172887-efd66e80-0afe-11ea-9621-9673831b658f.png)

Input: `mini-03.ttl`

    @prefix : <http://example.org/> .
    :person1 a :Person .
    :person2 a :Person .
    :person1 :follows :person2 .

Mapping: `mini-03.g2g`

    PREFIX : <http://example.org/>
    (p:Person)
        ?p a :Person .
    (p1:Person)-[:follows]->(p2:Person)       <-- PG edge is defined
        ?p1 :follows ?p2 .

Output: `mini-03.pg`

    "http://example.org/person1"	 :"Person"
    "http://example.org/person2"	 :"Person"
    "http://example.org/person1"	->	"http://example.org/person2"	:follows

### RDF resource > PG edge

![](https://user-images.githubusercontent.com/4862919/69172844-dfbe8f00-0afe-11ea-9176-7777a4043821.png)

Input: `mini-04.ttl`

    @prefix : <http://example.org/> .
    :person1 a :Person .
    :person2 a :Person .
    [] a :Follow ;
       :follower :person1 ;
       :followed :person2 .    

Mapping: `mini-04.g2g`

    PREFIX : <http://example.org/>
    (p:Person)
        ?p a :Person .
    (p1:Person)-[:follow]->(p2:Person)       <-- PG edge is defined
        ?f :follower ?p1 ;
           :followed ?p2 .

Output: `mini-04.pg`

    "http://example.org/person1"	 :"Person"
    "http://example.org/person2"	 :"Person"
    "http://example.org/person1"	"http://example.org/person2"	 :"follow"

### RDF datatype property > PG edge property

![](https://user-images.githubusercontent.com/4862919/69173333-d7b31f00-0aff-11ea-8858-da01e750c6af.png)

Input: `mini-05.ttl`

    @prefix : <http://example.org/> .
    :person1 a :Person .
    :person2 a :Person .
    [] a :Follow ;
       :follower :person1 ;
       :followed :person2 ;
       :since 2017 .

Mapping: `mini-05.g2g`

    PREFIX : <http://example.org/>
    (p:Person)
        ?p a :Person .
    (p1:Person)-[:follow {since:s}]->(p2:Person)  <-- PG edge is defined
        ?f :follower ?p1 ;
           :followed ?p2 ;
           :since ?s .

Output: `mini-05.pg`

    "http://example.org/person1"	 :"Person"
    "http://example.org/person2"	 :"Person"
    "http://example.org/person1"	"http://example.org/person2"	 :"follow"	"since":2017

## Actual Example

`musician.g2g`

    PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
    PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
    PREFIX schema: <http://schema.org/>
    PREFIX dbpedia-owl: <http://dbpedia.org/ontology/>
    PREFIX dbpedia-prop: <http://dbpedia.org/property/>
    PREFIX foaf: <http://xmlns.com/foaf/0.1/>

    # Node mappings
    (mus:Musician {vis_label:nam, born:dat, hometown:twn, pageLength:len})
        ?mus rdf:type foaf:Person, dbpedia-owl:MusicalArtist .
        ?mus rdfs:label ?nam .
        FILTER(lang(?nam) = "en") .
        OPTIONAL { ?mus dbpedia-prop:born ?dat }
        OPTIONAL { ?mus dbpedia-owl:hometown / rdfs:label ?twn. FILTER(lang(?twn) = "en"). }
        OPTIONAL { ?mus dbpedia-owl:wikiPageLength ?len }

    # Edge mappings
    (mus1:Musician)-[:same_group {label:nam, hometown:twn, pageLength:len}]->(mus2:Musician)
        ?grp a schema:MusicGroup.
        { ?grp dbpedia-owl:bandMember ?mus1 , ?mus2. } UNION
        { ?grp dbpedia-owl:formerBandMember ?mus1 , ?mus2. }
        FILTER(?mus1 != ?mus2)
        OPTIONAL { ?grp rdfs:label ?nam. FILTER(lang(?nam) = "en")}
        OPTIONAL { ?grp dbpedia-owl:hometown / rdfs:label ?twn. FILTER(lang(?twn) = "en"). }
        OPTIONAL { ?grp dbpedia-owl:wikiPageLength ?len }

    (mus1:Musician)-[:influenced]->(mus2:Musician)
        ?mus1 dbpedia-owl:influenced ?mus2 .
