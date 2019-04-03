# G2G Mapper

## Quick Start

### Installation

Set an alias to run docker container:

    $ alias g2g='docker run --rm -v $PWD:/work g2gml/g2g:0.3.1 g2g'

Check if it works:

    $ g2g --help

### Endpoint Mode

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

    $ g2g musician.g2g http://dbpedia.org/sparql

Check the output file:

    $ more output/musician/musician.pg

`musician.pg`

    "http://dbpedia.org/resource/Jin_Tielin"	->	"http://dbpedia.org/resource/Zu_Hai"	:influenced
    "http://dbpedia.org/resource/George_Lam"	->	"http://dbpedia.org/resource/Eason_Chan"	:influenced
    "http://dbpedia.org/resource/Aaron_Kwok"	->	"http://dbpedia.org/resource/Alien_Huang"	:influenced
    "http://dbpedia.org/resource/Samuel_Hui"	->	"http://dbpedia.org/resource/Albert_Au"	:influenced
    "http://dbpedia.org/resource/George_Lam"	->	"http://dbpedia.org/resource/Albert_Au"	:influenced

    ..

### Local File Mode

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

    $ g2g mini-05.g2g mini-05.ttl

Check the output file:

    $ more output/mini-05/mini-05.pg

`mini-05.pg`

    "http://example.org/person1"    "http://example.org/person2"    :follows        since:2017
    "http://example.org/person1"    :person
    "http://example.org/person2"    :person

## Usage

    Usage:

      ＄ g2g [options] <g2gml_file> <data_source>

    Options:

      -V, --version              output the version number
      -f, --format [format]      format of results <rq|pg|pgx|neo|dot|aws|all (default: pg)>
      -o, --output_dir [prefix]  directory where results are output (default: output/<input_prefix>)
      -h, --help                 output usage information

## Behaviour

 1. interpret G2GML and generates SPARQL queries to retrieve data
 2. issues SPARQL queries against public endpoints or given RDF data
 3. obtain the query results and save in PG format
 4. (optional) translate PG data into specific formats for graph databases

## Output Formats

### PG

Create a general PG file.

    $ g2g examples/musician.g2g http://ja.dbpedia.org/sparql 

### Neo4j

Create Neo4j style nodes/edges files.

    $ g2g -f neo examples/musician.g2g http://ja.dbpedia.org/sparql

Remove an existing Neo4j database files.

    $ rm -r ~/work/neo4j-community-3.3.1/data/databases/graph.db

Import data from nodes/edges files.

    $ $NEO4J_DIR/bin/neo4j-import \
      --into $NEO4J_DIR/data/databases/graph.db \
      --nodes output/musician.neo.nodes --relationships output/musician.neo.edges

Start Neo4j console and access its browser ( http://localhost:7474/browser/ ).

    $ $NEO4J_DIR/bin/neo4j console

### PGX

Create PGX style nodes/edges files.

    $ g2g -f pgx examples/musician.g2g http://ja.dbpedia.org/sparql 

Run PGX console and load the data.

    $ $PGX_DIR/bin/pgx
    pgx> G = session.readGraphWithProperties("output/musician.pgx.json")
