# Desafio de Estágio Prana - Relatório Financeiro (Módulo A)

Este documento detalha as decisões técnicas e o processo de desenvolvimento do relatório financeiro no Power BI, desde o tratamento dos dados até a construção do painel executivo, incluindo o link para acesso e evidências visuais.

**Link para o Relatório Publicado:** [https://app.powerbi.com/groups/me/reports/e5f4eb26-83ee-4b95-a079-131f70c35c38?ctid=104fa4b8-852f-40bd-ae90-7a5b30db9ae5&pbi_source=linkShare](https://app.powerbi.com/groups/me/reports/e5f4eb26-83ee-4b95-a079-131f70c35c38?ctid=104fa4b8-852f-40bd-ae90-7a5b30db9ae5&pbi_source=linkShare)

### 1. Tratamento de Dados (Power Query)

O processo de tratamento de dados foi a etapa mais crítica para garantir a confiabilidade da análise. As seguintes decisões foram tomadas:

* **Estrutura do Arquivo:** A fonte de dados original continha 4 linhas de cabeçalho e milhares de colunas vazias. A primeira ação foi remover as linhas iniciais, promover a linha correta como cabeçalho e, em seguida, selecionar apenas as 8 colunas relevantes, removendo todas as outras para otimizar o modelo.

* **Classificação ENTRADA/SAÍDA:** Para os registros com a classificação ENTRADA/SAÍDA faltante, criei uma regra em que todas as transações com DRE de "Receita" ou "Receitas nao operacionais" foram classificadas como ENTRADA e as demais como SAÍDA.

    **Print:** <img src="./dados consertados.jpeg" alt="Dados" />

### 2. Modelo de Dados

O modelo final é composto por uma única tabela (`Dados`) que passou por um processo de transformação intenso no Power Query. Foram criadas colunas calculadas para derivar uma data funcional (`Data_Final`) e a classificação de `TIPO LANÇAMENTO`. O resultado é um modelo limpo, enxuto e performático, pronto para as análises DAX.

### 3. Medidas DAX

Para a análise, criei três medidas principais para os KPIs do relatório:

* **Receita:** Calcula a soma total da coluna `QUANTIA` apenas para as transações classificadas como "ENTRADA".
    ````dax
    Receita = CALCULATE( SUM('Dados'[QUANTIA]), 'Dados'[TIPO LANÇAMENTO] = "ENTRADA" )
    ````


* **CPV (Custo do Produto Vendido):** Calcula a soma da `QUANTIA` para as transações da DRE tipo "CPV". O resultado é multiplicado por -1 para ser exibido como um valor positivo.
    ````dax
    CPV = CALCULATE( SUM('Dados'[QUANTIA]), 'Dados'[DRE] = "CPV" ) * -1
    ````
    

* **Margem Bruta:** Uma medida simples que subtrai o `CPV` da `Receita` para encontrar a margem de contribuição.
    ````dax
    Margem Bruta = [Receita] - [CPV]
    ````

### 4. Construção do Relatório Executivo

O painel visual foi construído em uma única página para fornecer uma visão rápida e executiva, contendo quatro elementos principais:

1.  **KPIs Principais:** Utilizei três visuais de **Cartão** no topo do relatório para exibir as medidas `Receita`, `CPV` e `Margem Bruta`, oferecendo um resumo imediato da saúde financeira.


2.  **Tendência Mensal:** Adicionei um **Gráfico de Linhas** para visualizar a medida `Receita` ao longo do tempo, usando a coluna `Data_Final` no eixo X para mostrar a evolução mensal.


3.  **Análise por Categoria:** Inseri um **Gráfico de Barras** para detalhar a `Receita` por `CATEGORIA`, permitindo identificar rapidamente quais categorias de produtos são mais relevantes.



4.  **Filtro Interativo:** Implementei uma **Segmentação de Dados** com a coluna `Data_Final`, dando ao usuário a flexibilidade de analisar qualquer período desejado.

    **Print:** <img src="./modelo visual.jpeg" alt="Modelo Visual" />

### 5. Configuração da Atualização Automática

Como a fonte de dados utilizada foi um arquivo Excel local, instalei e configurei o On-premises Data Gateway (personal mode) no meu computador. No Power BI Service, o conjunto de dados foi mapeado para usar este gateway, e as credenciais do Windows foram fornecidas. Por fim, a atualização agendada foi ativada para ocorrer diariamente.

**Print(agendamento):** <img src="./agendamento de atualizacao.jpeg" />

**Print(conclusão):** <img src="./atualizacao concluida.jpeg" />
