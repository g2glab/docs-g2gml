# PG Converters

## Docker Installation

Requirements:

* Docker

Install:

    $ alias pg2pgx='docker run --rm -v $PWD:/shared g2gml/pg:0.2.1 pg2pgx'
    $ pg2pgx --version
    0.2.1

## Local Installation

Requirements:

* Git
* Node

Install:

    $ git clone -b v0.2.1 https://github.com/g2gml/pg.git
    $ cd pg
    $ npm install
    $ npm link
    $ pg2pgx --version
    0.2.1
