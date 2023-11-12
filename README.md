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








