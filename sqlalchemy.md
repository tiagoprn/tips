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


