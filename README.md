<div align="center">
  <h1>Projeto: Setor Bancário - Comportamento Dos Clientes </h1>
</div>

![image](https://github.com/JosinoCarvalho/ProjetoDataBank/assets/111013250/b4fb6231-f809-4091-8ffc-0fc3851fcab4)


## Introdução

No cenário dinâmico do setor bancário contemporâneo, a importância dos insights baseados em dados é incontestável, desempenhando um papel crucial na compreensão do comportamento dos clientes e na otimização das estratégias operacionais. Este estudo de caso mergulha nas complexidades intrincadas do sistema "Banco de Dados", destacando a dinâmica intricada dos clientes e das atividades de transação. Através da análise minuciosa de clientes exclusivos entre diferentes regiões, da investigação de padrões de transações e da identificação de tendências no crescimento do saldo dos clientes, também desvendamos descobertas inovadoras. Com o objetivo de aprimorar a eficiência operacional e intensificar o envolvimento dos clientes, propomos recomendações estratégicas fundamentadas e pertinentes.

## Diagrama de Relacionamento entre Entidades
![josinomoreira](https://github.com/JosinoCarvalho/ProjetoDataBank/assets/111013250/25ac285b-0359-4106-bc51-5d5ebc93161f)

## Parte A. Explorar padrões de comportamento e tendências dos clientes para melhorar estratégias e serviços

### 1. Quantos clientes únicos existem no sistema de banco de dados?
```sql
SELECT COUNT(DISTINCT node_id) as Nodes
FROM data_bank.customer_nodes;
```

- A função `COUNT(DISTINCT node_id)` é usada para calcular a contagem de valores únicos na coluna "node_id" da tabela "customer_nodes". Isto significa que os valores duplicados não são contados; cada valor exclusivo é contado apenas uma vez.

| Nodes |
|-------|
|   5   |

### 2. Qual é o número de clientes por região?
```sql
SELECT Region_Name, COUNT(DISTINCT node_id) as Nodes
FROM data_bank.customer_nodes
JOIN data_bank.regions
ON customer_nodes.region_id = regions.region_id
GROUP BY Region_Name;
```

- `COUNT(DISTINCT node_id) as Nodes`: This column calculates the count of unique values in the "node_id" column of the "customer_nodes" table. Each unique value is counted only once, and the result column is labeled as "Nodes".

- The `JOIN data_bank.regions ON customer_nodes.region_id = regions.region_id` clause performs an inner join between the "customer_nodes" table and the "regions" table.
- The `GROUP BY Region_Name` clause groups the data by the values in the "Region_Name" column.

| region_name | nodes |
|-------------|-------|
|   Africa    |   5   |
|   America   |   5   |
|    Asia     |   5   |
|  Australia  |   5   |
|   Europe    |   5   |

### 3. Quantos clientes estão alocados em cada região?
```sql
SELECT Region_Name, COUNT(DISTINCT Customer_Id) as UniqueCustomers
FROM data_bank.customer_nodes
JOIN data_bank.regions
ON customer_nodes.region_id = regions.region_id
GROUP BY Region_Name
ORDER BY Region_Name;
```

- `COUNT(DISTINCT Customer_Id) as UniqueCustomers`: Esta coluna calcula a contagem de valores únicos na coluna "Customer_Id" da tabela "customer_nodes". Cada ID de cliente exclusivo é contado apenas uma vez e a coluna de resultados é rotulada como "UniqueCustomers".
- A cláusula `JOIN data_bank.regions ON customer_nodes.region_id =regions.region_id` realiza uma junção interna entre a tabela "customer_nodes" e a tabela "regions".
- A cláusula `GROUP BY Region_Name` agrupa os dados pelos valores na coluna "Region_Name".

No geral, esta consulta recupera a contagem de clientes únicos para cada região juntando as tabelas "customer_nodes" e "regions" na coluna comum "region_id", agrupando os resultados por região e ordenando os resultados por nome de região.
Esta consulta calcula o número de clientes alocados para cada região.

| region_name | unique_customers |
|-------------|------------------|
|   Africa    |       102        |
|   America   |       105        |
|    Asia     |       95         |
|  Australia  |       110        |
|   Europe    |       88         |

### 4. Quantos dias em média os clientes são realocados para um nó diferente?
```sql
SELECT AVG(end_date::date - start_date::date)
FROM data_bank.customer_nodes
WHERE end_date < DATE '9999-01-01';
```

- A instrução `SELECT AVG(end_date::date - start_date::date)` calcula a duração média entre as colunas "end_date" e "start_date" para todas as linhas na tabela "customer_nodes". A notação `::date` é usada para converter explicitamente as colunas para o tipo de dados de data.
- A cláusula `WHERE end_date < DATE '9999-01-01'` filtra as linhas da tabela "customer_nodes" para incluir apenas aquelas onde a "end_date" é anterior a 1º de janeiro de 9999.

|   avg    |
|----------|
| 14.6340  |

### 5. Qual é a mediana, os percentis 80 e 95 para esta mesma métrica de dias de realocação para cada região?
```sql
SELECT regions.region_name as Region,
  percentile_cont(0.50) within group (ORDER BY end_date::date - start_date::date) as Median,
  percentile_cont(0.80) within group (ORDER BY end_date::date - start_date::date) as "80th Percentile",
  percentile_cont(0.95) within group (ORDER BY end_date::date - start_date::date) as "95th Percentile"
FROM data_bank.customer_nodes
JOIN data_bank.regions
ON regions.region_id = customer_nodes.region_id
WHERE end_date < DATE '9999-01-01'
GROUP BY regions.region_name;
```

- O `percentile_cont(0.xx) dentro do grupo (ORDER BY end_date::date - start_date::date) as Median` calcula a duração mediana entre "end_date" e "start_date" para linhas em cada região. A função `percentile_cont` calcula um valor percentil especificado dentro de um grupo de valores ordenados pela duração calculada.

- A cláusula `JOIN data_bank.regions ON regiões.region_id = customer_nodes.region_id` executa uma junção interna entre as tabelas "customer_nodes" e "regions" com base em valores correspondentes nas colunas "region_id".
- A cláusula `WHERE end_date < DATE '9999-01-01'` filtra as linhas para incluir apenas aquelas em que a "end_date" é anterior a 1º de janeiro de 9999.
- A cláusula `GROUP BY regiões.region_name` agrupa os resultados por nomes de regiões exclusivos.


|  region  | median | 80th Percentile | 95th Percentile |
|----------|--------|-----------------|-----------------|
|  Africa  |   15   |       24        |       28        |
| America  |   15   |       23        |       28        |
|   Asia   |   15   |       23        |       28        |
| Australia|   15   |       23        |       28        |
|  Europe  |   15   |       24        |       28        |

## Parte B. Transações do cliente

### 1. Qual é a contagem exclusiva e o valor total para cada tipo de transação?
```sql
SELECT txn_type as Transaction_Type, 
  COUNT(txn_type) as Count_Of_Transactions,  
  SUM(txn_amount) as Total_Transaction_Amount
FROM data_bank.customer_transactions
GROUP BY txn_type;
```

- O `COUNT(txn_type) as Count_Of_Transactions` calcula a contagem de ocorrências de cada valor único na coluna "txn_type". A coluna de resultados é rotulada como "Count_Of_Transactions".

- O `SUM(txn_amount) as Total_Transaction_Amount` calcula a soma dos valores na coluna "txn_amount" para cada "txn_type" exclusivo. A coluna de resultados é rotulada como "Total_Transaction_Amount".

- A cláusula `GROUP BY txn_type` agrupa os resultados por valores únicos na coluna "txn_type". Isto significa que os cálculos subsequentes serão realizados separadamente para cada tipo de transação distinto.

| tipo_transação | contagem_de_transações | total_transaction_amount |
|------------------|-----------------------|------ -------------------|
| comprar | 1617 | 806537 |
| depósito | 2671 | 1359168 |
| retirada | 1580 | 793003 |

### 2. Qual é a média total histórica de contagens e valores de depósitos para todos os clientes?
```sql
WITH CTE AS (
  SELECT customer_ID,
    COUNT(customer_id) as Count_Of_Transactions,  
    AVG(txn_amount) as AVG_Transaction_Amount
  FROM data_bank.customer_transactions
  WHERE Txn_type = 'deposit'
  GROUP BY customer_ID
)
SELECT ROUND(AVG(Count_Of_Transactions), 2) as AVG_Deposits, 
  ROUND(AVG(AVG_Transaction_Amount), 2) as AVG_Deposit_Amounts
FROM CTE;
```

- A consulta usa uma Common Table Expression (CTE) chamada "CTE" para definir um conjunto de resultados temporário:
   - O `COUNT(customer_id) as Count_Of_Transactions` calcula a contagem de ocorrências de cada "customer_id" exclusivo onde o "Txn_type" é 'depósito'. A coluna de resultados é rotulada como "Count_Of_Transactions".
   - O `AVG(txn_amount) as AVG_Transaction_Amount` calcula o valor médio de "txn_amount" para cada "customer_id" exclusivo onde o "Txn_type" é 'depósito'. A coluna de resultados é rotulada como "AVG_Transaction_Amount".
   - A cláusula `WHERE Txn_type = 'deposit'` filtra as linhas para incluir apenas aquelas onde o "Txn_type" é 'deposit'.
   - A cláusula `GROUP BY customer_ID` agrupa os resultados por "customer_id" exclusivo. Isto significa que os cálculos subsequentes serão realizados separadamente para cada ID de cliente distinto.

- A consulta principal utiliza o CTE “CTE” para agregar os resultados do CTE:
   - A instrução `SELECT ROUND(AVG(Count_Of_Transactions), 2) as AVG_Deposits` calcula a contagem média de transações para todos os clientes e arredonda o resultado para duas casas decimais. A coluna de resultados é rotulada como "AVG_Deposits".
   - O `ROUND(AVG(AVG_Transaction_Amount), 2) as AVG_Deposit_Amounts` calcula a média dos valores médios das transações para todos os clientes e arredonda o resultado para duas casas decimais. A coluna de resultados é rotulada como "AVG_Deposit_Amounts".

Esta consulta calcula as contagens e valores médios de depósitos para todos os clientes.
| avg_depósitos | avg_deposit_amounts |
|--------------|---------------------|
| 5,34 | 508,61 |

### 3. Para cada mês - quantos clientes do Data Bank fazem mais de 1 depósito e 1 compra ou 1 saque em um único mês?
```sql
WITH CTE AS (
  SELECT customer_id, 
    EXTRACT('month' FROM txn_date) as Month,
    CASE WHEN txn_type = 'deposit' THEN 1 ELSE 0 END as deposit,
    CASE WHEN txn_type = 'purchase' THEN 1 ELSE 0 END as purchase,
    CASE WHEN txn_type = 'withdrawal' THEN 1 ELSE 0 END as withdrawal
  FROM data_bank.customer_transactions
),
CTE2 AS (
  SELECT month, customer_id, 
    SUM(deposit) as dep, 
    SUM(purchase) as pur, 
    SUM(withdrawal) as withd
  FROM CTE
  GROUP BY Month, Customer_ID
  ORDER BY Month, Customer_ID
)
SELECT month, COUNT(Month)
FROM CTE2
WHERE dep > 1 AND (pur = 1 OR withd = 1)
GROUP BY Month;
```

- CTE1 (“CTE”):
     - O `EXTRACT('month' FROM txn_date) as Month` extrai o componente do mês da coluna "txn_date" e o rotula como "Month".
     - As instruções `CASE` subsequentes criam indicadores binários baseados na coluna "txn_type". Para cada tipo de transação ('depósito', 'compra', 'saque'), é atribuído o valor 1 se a condição for atendida, caso contrário, é atribuído 0.

- CTE2 (“CTE2”):
     - A instrução `SELECT mês, customer_id` seleciona as colunas "mês" e "customer_id".
     - Os `SUM(depósito) como dep`, `SUM(compra) como pur` e `SUM(retirada) como retirada` calculam as somas dos indicadores binários para cada tipo de transação ('depósito', 'compra', ' retirada') para cada combinação única de mês e ID do cliente.
     - A cláusula `GROUP BY Month, Customer_ID` agrupa os resultados por mês e ID do cliente.

- A consulta principal:
   - A instrução `SELECT COUNT(Month)` calcula a contagem de meses únicos onde as condições são atendidas.
   - A cláusula `WHERE dep > 1 AND (pur = 1 OR withd = 1)` filtra os resultados para incluir apenas linhas onde a contagem de transações de 'depósito' é maior que 1 e transações de 'compra' ou 'retirada' estão presentes .
   - A cláusula `GROUP BY Month` agrupa os resultados por mês.

| month | count |
| ----- | ----- |
| 1     | 115   |
| 2     | 108   |
| 3     | 113   |
| 4     | 50    |

### 4. Qual é o saldo final de cada cliente no final do mês?
```sql
WITH CTE AS (
  SELECT 
    customer_id, 
    EXTRACT('month' FROM txn_date) AS Month, 
    CASE
      WHEN txn_type IN ('purchase', 'withdrawal') THEN -txn_amount
      ELSE txn_amount
    END AS adjusted_amount
  FROM data_bank.customer_transactions
)
SELECT 
  cust.customer_id, 
  months.Month, 
  COALESCE(SUM(cte.adjusted_amount), 0) 
    + CASE WHEN LAG(SUM(cte.adjusted_amount), 1) OVER (PARTITION BY cust.customer_id ORDER BY months.Month) IS NULL 
           THEN 0 
           ELSE LAG(SUM(cte.adjusted_amount), 1) OVER (PARTITION BY cust.customer_id ORDER BY months.Month) 
      END AS Ending_Balance
FROM 
  (SELECT DISTINCT customer_id FROM data_bank.customer_transactions) AS cust
CROSS JOIN 
  (SELECT DISTINCT EXTRACT('month' FROM txn_date) AS Month FROM data_bank.customer_transactions) AS months
LEFT JOIN 
  CTE AS cte 
ON 
  cust.customer_id = cte.customer_id AND months.Month = cte.Month
GROUP BY 
  cust.customer_id, months.Month
ORDER BY 
  cust.customer_id, months.Month;
```

- A consulta usa uma Common Table Expression (CTE) chamada "CTE" para pré-processar os dados:
   - O `EXTRACT('month' FROM txn_date) AS Month` extrai o componente do mês da coluna "txn_date" e o rotula como "Month".
   - A instrução `CASE` ajusta o valor da transação com base no "txn_type". Se o tipo de transação for ‘compra’ ou ‘saque’, o valor da transação é negado; caso contrário, será mantido como está.

- A consulta principal:
   - A função `COALESCE` é utilizada para tratar casos em que não há dados de transação disponíveis para um cliente em um determinado mês. Ele soma os valores ajustados das transações e inclui o saldo final do mês anterior (usando a função de janela `LAG`) para calcular o saldo final do mês atual.
   - A cláusula `GROUP BY` agrupa os resultados por "customer_id" e "Month".
   - A cláusula `ORDER BY` ordena os resultados por "customer_id" e "Month".

| customer_id | month | ending_balance |
|-------------|-------|----------------|
|      1      |   1   |      312       |
|      1      |   2   |      312       |
|      1      |   3   |     -952       |
|      1      |   4   |     -952       |
|      2      |   1   |      549       |
|      2      |   2   |      549       |
|      2      |   3   |       61       |
|      2      |   4   |       61       |
|      3      |   1   |      144       |
|      3      |   2   |     -821       |









