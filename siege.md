Using siege, and tuning up Apache to it: https://www.joedog.org/articles-tuning/

siege -c 500 -d 1 -i -f urls.txt

-c: concurrent users (not sessions)
-d: introduce a delay of x seconds. Recommended is "1" to benchmark performance (the default is 3). 
-i: internet mode (randomizes the URLs on the file) 
-f: file to get the urls from



HOW TO INTERPRET THE RESULTS: 

**Transactions** is the number of server hits. In the example, 385 transactions.

Elapsed time is the duration of the entire siege test. This is measured from the time the user invokes siege until the last simulated user completes its transactions. Shown above, the test took 76.02 seconds to complete.

Data transferred is the sum of data transferred to every siege simulated user. It includes the header information as well as content. Because it includes header information, the number reported by siege will be larger then the number reported by the server. In internet mode, which hits random URLs in a configuration file, this number is expected to vary from run to run.

Response time is the average time it took to respond to each simulated user’s requests.

Transaction rate is the average number of transactions the server was able to handle per second, in a nutshell: transactions divided by elapsed time.

Throughput is the average number of bytes transferred every second from the server to all the simulated users.

Concurrency is average number of simultaneous connections, a number which rises as server performance decreases.

Successful transactions is the number of times the server returned a code less then 400. Accordingly, redirects are considered successful transactions.



