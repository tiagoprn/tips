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
