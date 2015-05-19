CLI COMMANDS:

$ redis-cli

Sample queries:

- Get all "databases":
    $ info keyspace
    PS: If you didn't namespace them, they will be db0, db1, etc...

- Get all keys:
    $ scan 0
    PS: suposing db0 here

- Get all keys values:
    $ GET [key_name]

- Get the type of a key:
    $ TYPE [key_name]
