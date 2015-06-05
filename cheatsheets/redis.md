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

- DUMPING:

    First, create a dump on server A.

    On redis-cli
    127.0.0.1:6379> SAVE
    OK
    This will ensure /var/lib/redis/dump.rdb is completely up-to-date. (This file is periodically written by Redis anyway.)

    Next, copy it to server B:

    A$ scp /var/lib/redis/dump.rdb myuser@B:/tmp/dump.rdb
    Stop the Redis server on B, copy dump.rdb (ensuring permissions are the same as before), then start.

    B$ sudo service redis-server stop
    B$ sudo cp /tmp/dump.rdb /var/lib/redis/dump.rdb
    B$ sudo chown redis: /var/lib/redis/dump.rdb
    B$ sudo service redis-server start

