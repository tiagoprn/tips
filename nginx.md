How to configure the number of connections per second that can be handled by a server:

1) Get the number of cores the server has available:

```
$ grep processor /proc/cpuinfo | wc -l
> 1
```

Ideally, we must set 1 worker processor for each processor:

```
$ vim /etc/nginx/nginx.conf
    worker_processes 1;
```

2) You must know your system limitations. Almost any one of them you can't do anything to surpass, except for one (open files). To get those limitations:

```
$ ulimit -a

> core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 30
file size               (blocks, -f) unlimited
pending signals                 (-i) 23457
max locked memory       (kbytes, -l) unlimited
max memory size         (kbytes, -m) unlimited
open files                      (-n) 1024
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 99
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 23457
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited
```

Here we are. "open files" has the value of 1024. That is the number of simultaneous connections that can be served by Nginx. Important to have in mind that every browser usually opens up at least 2 connections per server, so that number can half. You must also keep in mind that this halfed number can be multiplied by the amount of cores. So:

```
$ vim /etc/nginx/nginx.conf
    worker_connections 1024;
```

CONSIDERATIONS:

    -A) In many cases the default here is 1024. If nginx hits the limit it will log the error (24: Too many open files) and return an http status code error to the client. Chances are nginx and your OS can handle a LOT more that 1024 "open files" (file descriptors). That value can be safely increased. You can do that setting a new value with ulimit.
        - For the current user session:
            ```
            $ ulimit -n 16384
            ```
        - Permanently, and for all users / sesssions::
            ```
            $ vim /etc/security/limits.conf
                soft nofile 16384 (value the kernel enforces)
                hard nofile 16384 (ceiling for the value above - a "maximum")
            ```

    -B) A nice formula to get an idea of the MAX number of connections is:
        ```
        max = worker_processes * worker_connections * (total_of_current_active_connections / average_request_time)
        ```
        If you are using nginx as a reverse proxy (e.g. using uwsgi), each request will always open up an additional connection to your backend. So, on that case, you must consider 1 connection as in fact being 2.


3) Configure the buffers. If anyone of them is too low, nginx will have to write to a temp file causing high I/O. There are mainly 4 directives to control that:

- client_body_buffer_size: the request body size (keep in mind POST requests here, and that they generally are form submissions).

- client_header_buffer_size: the request header size. Generally 1K is enough here.

- client_max_body_size: Maximum allowed size for a request. Keep in mind that, when exceeded, nginx will respond with http status code 413 (Request Entity Too Large).

- large_client_header_buffers: Maximum number and size of buffers on large client headers.

E.g.:

```
$ vim /etc/nginx/nginx.conf
    client_body_buffer_size 10K;
    client_header_buffer_size 1k;
    client_max_body_size 8m;
    large_client_header_buffers 2 1k;
```

4) Configure the timeouts.

The following directives are available:

- client_body_timeout, client_header_timeout: how much time a server will wait for a client body or client header to be sent after request. If that time expires and neither is sent, nginx will return http status code 408 (Request time out).

- keepalive_timeout: Nginx will close connections with the client after the time specified here.

- send_timeout: if the client takes nothing on the time interval between 2 readings, the connection will be shutdown.

E.g.:

```
$ vim /etc/nginx/nginx.conf
    client_body_timeout 12;
    client_header_timeout 12;
    keepalive_timeout 15;
    send_timeout 10;
```

5) If you have any other way to log every request made to server (E.g., Google Analytics), it may be good to turn off the access_log:

```
$ vim /etc/nginx/nginx.conf
    access_log off;
```

6) Avoid disk I/O at all costs (on your backend and on the server). Take into consideration that a low ram machine will need to swap to disk, and that swap is disk I/O.


7) Talking about Disk I/O, it is nice to cache the open file descriptors. Here are the available directives for that: http://wiki.nginx.org/HttpCoreModule#open_file_cache

8) Compress the data to be transfered through the network. Don't worry with the CPU penalty because Nginx is already optimized to deal with the required amount of CPU load. To do that:

```
$ vim /etc/nginx/nginx.conf
    gzip             on;
    gzip_min_length  1000;
    gzip_types       text/plain application/xml application/json;
    gzip_comp_level 5
```

9) Restart the nginx service (systemd / upstart / init.d) and do your measures again (you can use siege, ab or wrk/wrk2 for that).


---
references:
    - https://www.digitalocean.com/community/tutorials/how-to-optimize-nginx-configuration
    - http://blog.martinfjordvald.com/2011/04/optimizing-nginx-for-high-traffic-loads/
    - https://www.joedog.org/articles-tuning/ (that one shows step-by-step tuning of apache in a similar fashion assuming using "siege" to run the benchmarks).