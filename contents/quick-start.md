# Quick Start

## Installation

Set an alias to run docker container:

    $ alias g2g='docker run --rm -v $PWD:/work g2gml/g2g:0.2.1 g2g'

Show help:

    $ g2g --help

## Endpoint Mode

Download example g2g file:

    $ wget https://raw.githubusercontent.com/g2gml/g2g/master/examples/musician/musician.g2g
    
`musician.g2g`

    PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
    PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
    ..
    # Node mappings
    (mus:Musician {vis_label:nam, born:dat, hometown:twn, pageLength:len})
        ?mus rdf:type foaf:Person, dbpedia-owl:MusicalArtist .
    ..
    # Edge mappings
    (mus1:Musician)-[:same_group {label:nam, hometown:twn, pageLength:len}]->(mus2:Musician)
        ?grp a schema:MusicGroup ;
    ..

Run (mapping against SPARQL endpoint):

    $ g2g musician.g2g http://ja.dbpedia.org/sparql

## Local File Mode

Download example turtle file:

    $ wget https://raw.githubusercontent.com/g2gml/g2g/master/examples/mini-05/mini-05.ttl
    
`mini-05.ttl`
    
    @prefix : <http://example.org/> .
    :person1 a :Person .
    :person2 a :Person .
    [] a :Follow ;
       :follower :person1 ;
       :followed :person2 ;
       :since 2017 .
    
Download example g2g file:

    $ wget https://raw.githubusercontent.com/g2gml/g2g/master/examples/mini-05/mini-05.g2g
    
`mini-05.g2g`

    PREFIX : <http://example.org/>
    
    (p:person)
        ?p a :Person .
    
    (p1:person)-[:follows {since:s}]->(p2:person)
        ?f :follower ?p1 ;
           :followed ?p2 ;
           :since ?s .

Run (mapping against RDF data file):

    $ g2g mini-01.g2g mini-01.ttl