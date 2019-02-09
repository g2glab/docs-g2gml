# Quick Start

## G2G Mapper

### Run with Docker

Set an alias to run docker container:

    $ alias g2g='docker run --rm -v $PWD:/work g2gml/g2g:0.2.1 g2g'

Show help:

    $ g2g --help

### Endpoint Mode

Download example g2g file:

    $ wget https://raw.githubusercontent.com/g2gml/g2g/master/examples/musician/musician.g2g
    
Run (mapping against SPARQL endpoint):

    $ g2g musician.g2g http://ja.dbpedia.org/sparql

### Local File Mode

Download example turtle file:

    $ wget https://raw.githubusercontent.com/g2gml/g2g/master/examples/mini-05/mini-05.ttl
    
Download example g2g file:

    $ wget https://raw.githubusercontent.com/g2gml/g2g/master/examples/mini-05/mini-05.g2g
    
Run (mapping against RDF data file):

    $ g2g mini-01.g2g mini-01.ttl

## PG Converters

Create sample data:

    $ vi data.pg
    p1 :person name:John
    p2 :person name:Mary
    p1 p2 :follows since:2013

Run pg2pgx command for example:

    $ alias pg2pgx='docker run --rm -v $PWD:/shared g2gml/pg:0.2.1 pg2pgx'
    $ pg2pgx data.pg data
    "data.pgx.nodes" has been created.
    "data.pgx.edges" has been created.
    "data.pgx.json" has been created.

For converting other formats:

    $ pg2pgx <input_pg_file> <output_path_prefix>
    $ pg2neo <input_pg_file> <output_path_prefix>
    $ pg2aws <input_pg_file> <output_path_prefix>
    $ pg2dot <input_pg_file> <output_path_prefix>
