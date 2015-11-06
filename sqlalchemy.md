- FILTER BY NULL:

    faturas_all = Fatura.query.filter(                                                                                                                                                                  
        Fatura.cliente_id==cliente.id,                                                                                                                                                                  
        Fatura.data_quitacao == None).order_by(                                                                                                                                                         
        Fatura.periodo_referencia_ano.desc(),                                                                                                                                                       
        Fatura.periodo_referencia_mes.desc())                                                                                                                                                       


- FILTER BY NOT NULL:

    faturas_all = Fatura.query.filter(                                                                                                                                                                  
        Fatura.cliente_id==cliente.id,                                                                                                                                                                  
        Fatura.data_quitacao != None).order_by(                                                                                                                                                         
        Fatura.periodo_referencia_ano.desc(),                                                                                                                                                       
        Fatura.periodo_referencia_mes.desc()) 

- Filter by "ContaCorrente.cliente.codigo" (Filtering by Relationship Attribute):

    ContaCorrente.query.join(ContaCorrente.cliente).filter(Cliente.codigo == '100')

- Filter by "ContaCorrente.cliente.apelido" (Filtering by Relationship Attribute):

    ContaCorrente.query.join(ContaCorrente.cliente).filter(Cliente.apelido.ilike("%da%"))
