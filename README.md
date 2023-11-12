<div align="center">
  <h1>Projeto: Setor Bancário - Comportamento Dos Clientes </h1>
</div>


![image](https://github.com/JosinoCarvalho/ProjetoDataBank/assets/111013250/73af6dc8-c5a1-4675-afd2-cfac8e685aa4)


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




