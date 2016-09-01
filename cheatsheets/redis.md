CLI COMMANDS:

$ redis-cli

Sample queries:

- Get all "databases":
    $ INFO keyspace
    PS: Redis has a fixed number of databases (16), starting from 0.

- Select a specific database:

    $ SELECT [number] 
    
    e.g.
    
    $ SELECT 1


- Get all keys on a database:
    $ KEYS * 

- Getting the remaining TTL of a key (in how much time it will expire): 

    $ TTL [key_name] 


- Setting / Getting keys:

    - STRING keys:
        - Set key value: 
            $ SET [key_name] [value]
                
            E.g.:
                $ SET teste "aaaa"

        - Get key value:
            $ GET [key_name]

            E.g.:
                $ GET teste

    - HASH keys:
        - Set key value: 
            $ HSET [key_name] [hash_name] [hash_value]
                
            E.g.:
                $ HSET "myhash" field01" "aaaa"

        - Get key value:
            $ HGET [key_name] [hash_name]

            E.g.:
                $ HGET "myhash" "field01"

        - Get all key hash names: 
            $ HKEYS [key_name]

            E.g.:
                $ HKEYS "myhash"

        - Get all key hash names/values: 
            $ HGETALL [key_name]
            
             E.g.:
                $ HGETALL "myhash"

   
- Incrementing / Decrementing STRING / HASH key counters:

    - STRING keys:
        $ INCR "var1"
        $ INCR "var1"
        $ INCR "var1"
        $ DECR "var1"

    - HASH keys:
        $ HINCRBY "myhash" "counter" 1
        $ HINCRBY "myhash" "counter" 1
        $ HINCRBY "myhash" "counter" 1
        $ HINCRBY "myhash" "counter" -1  # this one decreases the counter


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

