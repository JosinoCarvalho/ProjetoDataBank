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








