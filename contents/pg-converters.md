# PG Converters

## Installation

* Docker installation requires **Docker**
* Local installation requires **Git** and **Node**

### Docker Installation

    $ alias pg2pgx='docker run --rm -v $PWD:/shared g2gml/pg:0.2.1 pg2pgx'
    $ pg2pgx --version
    0.2.1

### Local Installation

    $ git clone -b v0.2.1 https://github.com/g2gml/pg.git
    $ cd pg
    $ npm install
    $ npm link
    $ pg2pgx --version
    0.2.1

## Example

Create sample data:

    $ vi data.pg
    
`data.pg`
    
    p1 :person name:John
    p2 :person name:Mary
    p1 p2 :likes since:2013

Run pg2pgx command for example:

    $ pg2pgx data.pg data
    "data.pgx.nodes" has been created.
    "data.pgx.edges" has been created.
    "data.pgx.json" has been created.

## Commands

    $ pg2pgx <input_pg_file> <output_path_prefix>
    $ pg2neo <input_pg_file> <output_path_prefix>
    $ pg2aws <input_pg_file> <output_path_prefix>
    $ pg2dot <input_pg_file> <output_path_prefix>
