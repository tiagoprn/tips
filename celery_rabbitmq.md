- PARA VERIFICAR QUAIS TASKS AGENDADAS ESTÃO EM EXECUÇÃO:
    $ celery inspect active

    - Mais informações que podem ser pegas com o celery inspect:

        $ celery inspect [query]

        Os valores para "query" podem ser:

            - active
                show active tasks (being processed)

            - active_queues
                show queues being consumed from

            - clock
                get value of logical clock (useful to get how much time has passed since "time_start" of an active task)

            - registered
                show registered tasks

            - reserved
                dump reserved tasks (waiting to be processed)

            - revoked
                dump of revoked task ids

            - scheduled
                dump scheduled tasks (eta/countdown/retry)

            - stats
                dump worker statistics


- OBTENDO STATUS DO RABBITMQ (PARA DETALHES LOW LEVEL DA EXECUÇÃO):

    (Tip: Adding the -q option to rabbitmqctl makes the output easier to parse)

    - Finding the number of tasks in a queue:
        $ sudo rabbitmqctl list_queues name messages messages_ready messages_unacknowledged
            - Parameters:
                - messages:  sum of ready and unacknowledged messages.
                - messages_ready: number of messages ready for delivery (sent but not received).
                - messages_unacknowledged: number of messages that has been received by a worker but not acknowledged yet (meaning it is in progress, or has been reserved).

    - Finding the number of workers currently consuming from a queue:
        $ sudo rabbitmqctl list_queues name consumers

    - Finding the amount of memory allocated to a queue:
        $ sudo rabbitmqctl list_queues name memory

- RABBITMQ API:

    OVERVIEW:
        $ curl -i -u guest:guest  http://172.16.1.232:15672/api/overview

    ACTIVE CONNECTIONS:
        $ curl -i -u guest:guest  http://172.16.1.232:15672/api/overview

    ALL QUEUES:
        $ curl -i -u guest:guest  http://172.16.1.232:15672/api/queues

- RABBITMQ OVERVIEW:

    ### Queued messages:
        Ready
            Is the number of messages that are available to be delivered to the workers.

        Unacknowledged
            Is the number of messages for which the server is waiting for acknowledgement (the worker received the message and is working on it, but didn't send an acknowledge yet).

        Total
            Is the sum of Ready and Unacknowledged messages.

    ### Message rates:
        Publish
            This is the rate how many messages are incomming to the RabbitMQ server.

        Deliver
            This is the rate at which messages requiring acknowledgement are being delivered in response to basic.consume.

        Acknowledge
            Rate at which messages are being acknowledged by the worker/consumer.

        Redelivered
            Rate at which messages with the 'redelivered' flag set are being delivered. For example if you don't get a acknowledge message for a delivered message, you will deliver this message again.


- rabbitmqadmin: Admin CLI interface for rabbitmq

- INSTALLATION:

	1) Download from the host where it was installed: 
		On the web interface, go to "http://localhost:15672/cli/".
		$ wget [the_url_pointed_above]

	2) Copy it as a binary and change the permissions:
		$ chmod 755 rabbitmqadmin
		$ cp -farv rabbitmqadmin /usr/bin

- DELETE ALL QUEUES:
	rabbitmqadmin list queues name | awk '{print $2}' | xargs -I qn rabbitmqadmin delete queue name=qn

- If RabbitMQ fails to start, and you won't need to restore its persistent data, you can safely remove its mnesia database. 
	E.g., on CentOS 7:
		$ rm -fr /var/lib/rabbitmq/mnesia


