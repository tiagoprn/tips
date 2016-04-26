Mostrar apenas state, pid e cmd dos processos:
    $ ps -eo state,pid,cmd

Se o IOWAIT estiver alto, vão haver muitos processos com os state "S" ou "D". 
    S: Uninterruptible sleep (waiting for an event to complete)
    D: Uninterruptible sleep (usually I/O)


