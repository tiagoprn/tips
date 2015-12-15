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

- ENABLING REDIS TO RESPOND OUTSIDE OF LOCALHOST:

	$ vim /etc/redis.conf
	Change the line: 
		bind 127.0.0.1
	To:
		bind 0.0.0.0
	That will make it listen on all network interfaces. 
	After changing, restart the redis daemon to the change to make effect:
		$ systemctl stop redis
		$ systemctl start redis

- DATA TYPES:
    - **STRING**: String, Integer or floating point (the "smallest" data type). Operations: GET, SET, DEL.
    - **LIST**: Ordered sequence (linked list) of strings. A string can be on the list more than once (is NOT unique). Operations: RPUSH, LRANGE, LINDEX, LPOP.
    - **SET**: Unordered sequence table of strings. All strings on a hash table are UNIQUE. Operations: SADD, SMEMBERS, SISMEMBER (quickly check if an item is in the set), SREM, SINTER, SUNION, SDIFF.
    - **HASH**: Similar to a document on a document store or a row on a relational database. Values that can be stored in hashes are the same then strings. If the value can be intepreted as a number, can be incremented or decremented. A hash is kinda like a "miniature" version of Redis itself. Operations: HSET, HGET, HGETALL HDEL, HINCRBY (increments a value in a hash).
    - **ZSET**: Analogous to HASHES, but its keys (AKA members) are unique, and the values (AKA scores) are limited to floating point numbers. Items can be accessed by the sorted order and values of the scores. Operations: ZADD, ZRANGE, ZRANGEBYSCORE, ZREM, ZINCRBY (increments the score of a member).
    **IMPORTANT: We can combine the data storage ability of HASHes with the sorting ability of ZSETs.**

