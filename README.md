# Exercícios Práticos de PostgreSQL - IFRS

Repositório contendo a resolução de exercícios práticos de **PostgreSQL / SQL**, desenvolvidos durante o curso de Banco de Dados do **Instituto Federal de Educação, Ciência e Tecnologia do Rio Grande do Sul (IFRS)**.

Os exercícios abordam desde consultas fundamentais até técnicas avançadas de manipulação e análise de dados, incluindo junções (`JOIN`), subconsultas encadeadas (`SUBQUERIES`), agrupamentos com filtros (`GROUP BY` e `HAVING`), funções de agregação e operações de busca textual.

---

## 🗄️ Esquema do Banco de Dados

Todas as consultas foram construídas com base na estrutura de e-commerce descrita abaixo:

### Tabela `clientes`
| Coluna | Tipo de Dado | Descrição |
| :--- | :--- | :--- |
| `id` | `INTEGER` | Chave primária do cliente |
| `nome` | `VARCHAR` | Nome completo do cliente |
| `email` | `VARCHAR` | Endereço de e-mail |
| `cidade` | `VARCHAR` | Cidade de residência |

### Tabela `pedidos`
| Coluna | Tipo de Dado | Descrição |
| :--- | :--- | :--- |
| `id` | `INTEGER` | Chave primária do pedido |
| `cliente_id` | `INTEGER` | Chave estrangeira referenciando `clientes(id)` |
| `status` | `VARCHAR` | Status atual (`'Entregue'`, `'Em Processamento'`, `'Enviado'`, etc.) |
| `valor_total` | `NUMERIC(10,2)` | Valor total do pedido em reais |
| `descricao_pedido` | `TEXT` | Descrição simplificada dos itens |
| `data` | `DATE` | Data de realização do pedido |

---

## 💻 Exercícios Desenvolvidos

---

### **Módulo 1: Junções e Filtros de Ausência**

#### Exercício 1: Identificação de Clientes Sem Pedidos
**Enunciado:** Para uma campanha de reativação do setor de marketing, identifique os clientes que nunca realizaram nenhum pedido utilizando `LEFT JOIN` e a condição de valor nulo (`IS NULL`).

```sql
SELECT 
    cli.nome
FROM clientes AS cli
LEFT JOIN pedidos AS ped ON cli.id = ped.cliente_id
WHERE ped.id IS NULL;
```

---

#### Exercício 2: Exclusão com Subconsulta (`NOT IN`)
**Enunciado:** Busque o nome e a cidade de todos os clientes que não possuem nenhum pedido registrado com o status `"Entregue"`, utilizando subconsulta no `WHERE`.

```sql
SELECT 
    cli.nome, 
    cli.cidade
FROM clientes AS cli
WHERE cli.id NOT IN (
    SELECT ped.cliente_id
    FROM pedidos AS ped
    WHERE ped.status = 'Entregue'
);
```

---

### **Módulo 2: Agregação, Agrupamento e Média**

#### Exercício 3: Filtro de Ticket Médio (`HAVING` e `AVG`)
**Enunciado:** Mapeie os clientes com ticket médio alto. Retorne o nome do cliente e a média do valor dos seus pedidos (`media_pedidos`), exibindo apenas aqueles cuja média seja superior a `400.00`.

```sql
SELECT 
    cli.nome, 
    AVG(ped.valor_total) AS media_pedidos
FROM clientes AS cli
INNER JOIN pedidos AS ped ON cli.id = ped.cliente_id
GROUP BY cli.id, cli.nome
HAVING AVG(ped.valor_total) > 400.00;
```

---

#### Exercício 4: Comparação com Média Geral (Subconsulta Numérica)
**Enunciado:** Liste a descrição e o valor total de todos os pedidos cujo valor seja estritamente maior que a média geral de todos os pedidos cadastrados no sistema.

```sql
SELECT 
    ped.descricao_pedido, 
    ped.valor_total
FROM pedidos AS ped
WHERE ped.valor_total > (
    SELECT AVG(p2.valor_total)
    FROM pedidos AS p2
);
```

---

#### Exercício 5: Valores Extremos por Cidade (`MIN` e `MAX`)
**Enunciado:** Traga o nome da cidade, o menor valor de pedido (`menor_pedido`) e o maior valor de pedido (`maior_pedido`) registrados para clientes daquela cidade, ordenando o resultado alfabeticamente pela cidade.

```sql
SELECT 
    cli.cidade, 
    MIN(ped.valor_total) AS menor_pedido, 
    MAX(ped.valor_total) AS maior_pedido
FROM clientes AS cli
JOIN pedidos AS ped ON cli.id = ped.cliente_id
GROUP BY cli.cidade
ORDER BY cli.cidade ASC;
```

---

### **Módulo 3: Busca Textual e Filtragem Avançada**

#### Exercício 6: Busca Pattern Matching (`ILIKE` e `OR`)
**Enunciado:** Identifique clientes cujo e-mail termine com `'@gmail.com'` ou comece com a letra `'a'` (de forma *case-insensitive*).

```sql
SELECT 
    cli.nome, 
    cli.email
FROM clientes AS cli
WHERE cli.email ILIKE '%@gmail.com'
   OR cli.email ILIKE 'a%';
```

---

#### Exercício 7: Subconsulta para Múltiplos Status (`IN`)
**Enunciado:** Gere uma lista com nome, e-mail e cidade dos clientes que possuem pedidos com o status igual a `'Em Processamento'` ou `'Enviado'`, utilizando subconsulta no `WHERE` com o operador `IN`.

```sql
SELECT 
    cli.nome, 
    cli.email, 
    cli.cidade
FROM clientes AS cli
WHERE cli.id IN (
    SELECT ped.cliente_id
    FROM pedidos AS ped
    WHERE ped.status IN ('Em Processamento', 'Enviado')
);
```

---

## 🚀 Conceitos e Sintaxes Dominados

- **Junções de Tabelas:** `INNER JOIN`, `LEFT JOIN`
- **Filtros e Operadores:** `WHERE`, `HAVING`, `IN`, `NOT IN`, `BETWEEN`, `ILIKE`
- **Funções de Agregação:** `SUM()`, `AVG()`, `MIN()`, `MAX()`, `COUNT()`
- **Agrupamento e Ordenação:** `GROUP BY`, `ORDER BY (ASC/DESC)`
- **Subconsultas (Subqueries):** Aninhamento no `WHERE`, listas dinâmicas de IDs e filtros escalares.

---

*Desenvolvido como parte do programa acadêmico de Banco de Dados do IFRS.*