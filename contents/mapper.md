# G2G Mapper

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

## Example

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
