## 5. Estoque: Alerta de produtos abaixo do mínimo

### 🛠 Problema real
O Protheus permite configurar estoques mínimos e máximos por produto (SB1/SB2), mas **não emite alertas automáticos** quando os níveis estão abaixo do ideal. Isso faz com que o setor de compras só perceba o problema **quando já está em falta**, o que gera prejuízo e urgência nas reposições.

### 📉 Impacto
- Falta de produtos para venda ou produção
- Clientes insatisfeitos e perda de oportunidades
- Compras emergenciais com custo maior
- Falta de planejamento no abastecimento

### 💡 Solução aplicada
Desenvolvimento de um **relatório automático**, que identifica todos os itens com saldo atual **abaixo do estoque mínimo**. A rotina pode ser programada para rodar diariamente, gerando uma planilha ou e-mail com os dados críticos para a área de compras.

> O relatório usa como base a tabela SB2 (saldos por depósito) comparando os campos `B2_QATU` (quantidade atual) e `B2_ESTMIN` (estoque mínimo).

### 🧾 Código exemplo (ADVPL)
```advpl
User Function AlertaEstMin()
    Local cArquivo := "C:\relatorios\Estoque_Minimo.csv"
    Local nHandle := MemoWrite(cArquivo, "Produto;Descrição;Estoque Atual;Estoque Mínimo" + CRLF)
    Local cLinha := ""

    dbSelectArea("SB2")
    dbSetOrder(1) // Índice por produto
    dbGoTop()

    While !Eof()
        If SB2->B2_QATU < SB2->B2_ESTMIN .And. SB2->B2_ESTMIN > 0
            cLinha := SB2->B2_COD + ";" +;
                      SB1->B1_DESC + ";" +;
                      Str(SB2->B2_QATU,10,2) + ";" +;
                      Str(SB2->B2_ESTMIN,10,2) + CRLF
            MemoWrite(cArquivo, cLinha, .T.)
        EndIf
        dbSkip()
    EndDo

    MsgInfo("Relatório de estoque mínimo gerado com sucesso.")
Return
```
O código pode ser adaptado para gerar um PDF, HTML ou até enviar por e-mail para o comprador responsável.

## 🧪 Cenários de teste - Controle de Estoque Mínimo

| Situação                           | Comportamento Esperado               | Status  |
|------------------------------------|--------------------------------------|---------|
| Estoque atual < estoque mínimo     | Incluído no relatório de alerta      | ✅      |
| Estoque atual ≥ estoque mínimo     | Oculto no relatório                  | 🚫      |
| Estoque mínimo = 0                 | Excluído da verificação              | ⚠️      |

🎯 Benefícios
Evita rupturas no estoque

Dá visibilidade diária ao time de compras

Permite ações proativas em vez de reativas

Garante continuidade da operação de vendas/produção

🏷️ Tags
#Protheus #Estoque #SB2 #EstoqueMinimo #Compras #ADVPL #Automação
