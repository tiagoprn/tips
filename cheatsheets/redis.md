CLI COMMANDS:

$ redis-cli

Sample queries:

- Get all "databases":
    $ INFO keyspace
    PS: Redis has a fixed number of databases (16), starting from 0.

- Get all keys:
    $ SCAN 0
    PS: suposing db0 here

- Get all keys values:
    $ GET [key_name]

- Get the type of a key:
    $ TYPE [key_name]

- Running instance info:
    $ INFO

- DELETING DATA (USE WITH CAUTIOUS):

    - To drop the currently selected database:
        $ FLUSHDB

    - To drop all databases at once:
        $ FLUSHALL
