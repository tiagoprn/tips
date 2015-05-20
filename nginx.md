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

2) Get the number of simultaneous connections that can be served by Nginx. Important to have in mind that every browser usually opens up at least 2 connections per server, so that number can half. So, to get the number:

```
$ ulimit -n
> 1024
```

The value got there is a good starting number. Keep in mind that this number can be multiplied by the amount of cores. So:

```
$ vim /etc/nginx/nginx.conf
    worker_connections 1024;

```

NOTE: that limitation can be mitigated with tuning of the "keepalive_timeout" directive, that will be explained below at [4].

====================================== TODO: search for if and then how you can raise ulimit value.

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

6) Restart the nginx service (systemd / upstart / init.d) and do your measures again (you can use siege, ab or wrk/wrk2 for that).


---
references:
    - https://www.digitalocean.com/community/tutorials/how-to-optimize-nginx-configuration
    - http://blog.martinfjordvald.com/2011/04/optimizing-nginx-for-high-traffic-loads/
    - https://www.joedog.org/articles-tuning/ (that one shows step-by-step tuning of apache in a similar fashion assuming using "siege" to run the benchmarks).