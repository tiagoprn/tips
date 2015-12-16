Using siege, and tuning up Apache to it: https://www.joedog.org/articles-tuning/

    $ siege -v -c 500 -d 1 -t60S -i -f urls.txt -l

        -v: verbose mode
        -c: concurrent users (not sessions)
        -d: random delay between 1 and x seconds. Recommended is "1" to benchmark performance (the default is 3). 
        -t: runs for x [S]econds
        -i: internet mode (randomizes the URLs on the file) 
        -f: file to get the urls from
        -l: output to log file (outputs the results into a csv file. It adds a new line to the file for each new run.)

## HOW TO INTERPRET THE RESULTS: 

**Transactions** is the number of server hits. In the example, 385 transactions.

**Elapsed time** is the duration of the entire siege test. This is measured from the time the user invokes siege until the last simulated user completes its transactions. Shown above, the test took 76.02 seconds to complete.

**Data transferred** is the sum of data transferred to every siege simulated user. It includes the header information as well as content. Because it includes header information, the number reported by siege will be larger then the number reported by the server. In internet mode, which hits random URLs in a configuration file, this number is expected to vary from run to run.

**Response time** is the average time it took to respond to each simulated user’s requests.

**Transaction rate** is the average number of transactions the server was able to handle per second, in a nutshell: transactions divided by elapsed time.

**Throughput** is the average number of bytes transferred every second from the server to all the simulated users.

**Concurrency** is average number of simultaneous connections, a number which rises as server performance decreases.

**Successful transactions** is the number of times the server returned a code less then 400. Accordingly, redirects are considered successful transactions.

OBS.: If you need to measure a proxy performance (to redirect to another http server/port), you can create a file named "~/.siegerc" and configure the proxy there. You can also specify username and passwords on this file. E.g.: https://gist.github.com/stansmet/3067988

## HOW TO MAKE A SIMPLE TEST:
(will just generate 4k concurrent users)

    $ siege -v -c 4000 -i -f urls.txt -l

