<div align="center">
  <h1>Case Study 4: Data Bank</h1>
</div>

![image](https://github.com/JosinoCarvalho/ProjetoDataBank/assets/111013250/9b20fd13-6e47-4629-8bfc-630df15e073a)


### 1. Quantos clientes únicos existem no sistema de banco de dados?
SELECT COUNT(DISTINCT node_id) as Nodes
FROM data_bank.customer_nodes;

- A função `COUNT(DISTINCT node_id)` é usada para calcular a contagem de valores únicos na coluna "node_id" da tabela "customer_nodes". Isto significa que os valores duplicados não são contados; cada valor exclusivo é contado apenas uma vez.
| Nodes |
|-------|
|   5   |

### 2. Qual é o número de clientes por região?
SELECT Region_Name, COUNT(DISTINCT node_id) as Nodes
FROM data_bank.customer_nodes
JOIN data_bank.regions
ON customer_nodes.region_id = regions.region_id
GROUP BY Region_Name;

- `COUNT(DISTINCT node_id) as Nodes`: Esta coluna calcula a contagem de valores únicos na coluna "node_id" da tabela "customer_nodes". Cada valor exclusivo é contado apenas uma vez e a coluna de resultados é rotulada como "Nós".
- A cláusula `JOIN data_bank.regions ON customer_nodes.region_id =regions.region_id` realiza uma junção interna entre a tabela "customer_nodes" e a tabela "regions".
- A cláusula `GROUP BY Region_Name` agrupa os dados pelos valores na coluna "Region_Name".
| region_name | nodes |
|-------------|-------|
|   Africa    |   5   |
|   America   |   5   |
|    Asia     |   5   |
|  Australia  |   5   |
|   Europe    |   5   |

### 3. Quantos clientes estão alocados em cada região?
SELECT Region_Name, COUNT(DISTINCT Customer_Id) as UniqueCustomers
FROM data_bank.customer_nodes
JOIN data_bank.regions
ON customer_nodes.region_id = regions.region_id
GROUP BY Region_Name
ORDER BY Region_Name;

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
SELECT AVG(end_date::date - start_date::date)
FROM data_bank.customer_nodes
WHERE end_date < DATE '9999-01-01';

- A instrução `SELECT AVG(end_date::date - start_date::date)` calcula a duração média entre as colunas "end_date" e "start_date" para todas as linhas na tabela "customer_nodes". A notação `::date` é usada para converter explicitamente as colunas para o tipo de dados de data.
- A cláusula `WHERE end_date < DATE '9999-01-01'` filtra as linhas da tabela "customer_nodes" para incluir apenas aquelas onde a "end_date" é anterior a 1º de janeiro de 9999.

|   avg    |
|----------|
| 14.6340  |

### 5. Qual é a mediana, os percentis 80 e 95 para esta mesma métrica de dias de realocação para cada região?
SELECT regions.region_name as Region,
  percentile_cont(0.50) within group (ORDER BY end_date::date - start_date::date) as Median,
  percentile_cont(0.80) within group (ORDER BY end_date::date - start_date::date) as "80th Percentile",
  percentile_cont(0.95) within group (ORDER BY end_date::date - start_date::date) as "95th Percentile"
FROM data_bank.customer_nodes
JOIN data_bank.regions
ON regions.region_id = customer_nodes.region_id
