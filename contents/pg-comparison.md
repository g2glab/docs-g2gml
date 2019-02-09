PG

    # NODES
    101  :person  name:"Barack Obama"  country:"United States"
    102  :person  name:"Shinzo Abe"  country:Japan

    # EDGES
    101  104  :admires  score:20.0  since:2015
    102  103  :collaborates  since:2010

JSON-PG

    {
      "nodes":[
        {"_id":101, "_label":"person", "name":"Barack Obama", "country":"United States"}
      , {"_id":101, "_label":"person", "name":"Shinzo Abe", "country":"United States"}
      ],
      "edges":[
        {"_from":101, "_to":104, "_label":"admires", "score":20.0, "since":2015}
      , {"_from":102, "_to":103, "_label":"collaborates", "since":2010}
      ]
    }

GraphSON

    {
      "nodes":[
        {"_id":101, "_label":"person", "name":"Barack Obama", "country":"United States"}
      , {"_id":101, "_label":"person", "name":"Shinzo Abe", "country":"United States"}
      ],
      "edges":[
        {"_from":101, "_to":104, "_label":"admires", "score":20.0, "since":2015}
      , {"_from":102, "_to":103, "_label":"collaborates", "since":2010}
      ]
    }


