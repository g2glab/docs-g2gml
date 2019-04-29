# G2G Mapper

## Quick Start

### Installation

Set an alias to run docker container:

    $ alias g2g='docker run --rm -v $PWD:/work g2gml/g2g:x.x.x g2g'

Check if it works:

    $ g2g --help

### Endpoint Mode

Download example g2g file:

    $ wget https://raw.githubusercontent.com/g2gml/g2g/master/examples/musician/musician.g2g
    
`musician.g2g`

    PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
    PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
    ...
    
    # Node mappings
    (mus:Musician {vis_label:nam, born:dat, hometown:twn, pageLength:len})
        ?mus rdf:type foaf:Person, dbpedia-owl:MusicalArtist .
    ...
    
    # Edge mappings
    (mus1:Musician)-[:same_group {label:nam, hometown:twn, pageLength:len}]->(mus2:Musician)
        ?grp a schema:MusicGroup ;
    ...

Run (mapping against SPARQL endpoint):

    $ g2g musician.g2g http://dbpedia.org/sparql

Check the output file:

    $ more output/musician/musician.pg

`musician.pg`

    "http://dbpedia.org/resource/Martin_Glover"     :Musician       vis_label:"Martin Glover"
    "http://dbpedia.org/resource/Per_Wiberg"        :Musician       vis_label:"Per Wiberg"  hometown:Stockholm
    "http://dbpedia.org/resource/Tex_Perkins"       :Musician       vis_label:"Tex Perkins"
    "http://dbpedia.org/resource/Michelle_DaRosa"   :Musician       vis_label:"Michelle DaRosa"
    "http://dbpedia.org/resource/Raúl_Sánchez_(musician)"   :Musician       vis_label:"Raúl Sánchez (musician)"     hometown:"Valencia, Spain"
    ...
    "http://dbpedia.org/resource/Jin_Tielin"	->	"http://dbpedia.org/resource/Zu_Hai"	:influenced
    "http://dbpedia.org/resource/George_Lam"	->	"http://dbpedia.org/resource/Eason_Chan"	:influenced
    "http://dbpedia.org/resource/Aaron_Kwok"	->	"http://dbpedia.org/resource/Alien_Huang"	:influenced
    "http://dbpedia.org/resource/Samuel_Hui"	->	"http://dbpedia.org/resource/Albert_Au"	:influenced
    "http://dbpedia.org/resource/George_Lam"	->	"http://dbpedia.org/resource/Albert_Au"	:influenced
    ...
    "http://dbpedia.org/resource/Ville_Valo"        ->      "http://dbpedia.org/resource/Linde_Lindström"   :same_group     label:"HIM (Finnish band)"      hometown:Helsinki
    "http://dbpedia.org/resource/Jerry_Donahue"     ->      "http://dbpedia.org/resource/Sally_Barker"      :same_group     label:Fotheringay
    "http://dbpedia.org/resource/Line_Horntveth"    ->      "http://dbpedia.org/resource/Øystein_Moen"      :same_group     label:"Jaga Jazzist"    hometown:Norway
    "http://dbpedia.org/resource/Adam_von_Buhler"   ->      "http://dbpedia.org/resource/Kasson_Crooker"    :same_group     label:"Splashdown (band)"       hometown:"United States"
    "http://dbpedia.org/resource/Jeong_Jinwoon"     ->      "http://dbpedia.org/resource/Lee_Chang-min_(singer)"    :same_group     label:"2AM (band)"      hometown:"South Korea"
    ...

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

    "http://example.org/person1"	:person
    "http://example.org/person2"	:person
    "http://example.org/person1"	->	"http://example.org/person2"	:follows	since:2017


## Usage

    Usage:

      ＄ g2g [options] <g2gml_file> <data_source>

    Options:

      -V, --version              shows the version number
      -f, --format [format]      format of results <rq|pg|pgx|neo|dot|aws|all (default: pg)>
      -o, --output_dir [prefix]  output directory (default: output/<input_prefix>)
      -h, --help                 output usage information

## Behaviour

 1. Interprets G2GML and generates SPARQL queries to retrieve data
 2. Issues SPARQL queries against public endpoints or given RDF data
 3. Obtains the query results and saves it in PG format
 4. (optional) Translates PG data into specific formats for graph databases

## Output Formats

### PG

* Use `-f pg` or no `-f` option (default)
* Number of output files: 1 (sample.pg)

### JSON-PG

* Use `-f json`
* Number of output files: 1 (sample.json)

### Neo4j

* Use `-f neo`
* Number of output files: 2 (sample.neo.nodes, sample.neo.edges)

### Oracle Labs PGX

* Use `-f pgx`
* Number of output files: 3 (sample.pgx.nodes (opv), sample.pgx.edges (ope), sample.pgx.json (config))

### Amazon Neptune

* Use `-f aws`
* Number of output files: 2 (sample.aws.nodes, sample.aws.edges)

### Graphvis

* Use `-f dot`
* Number of output files: 1 (sample.dot)
