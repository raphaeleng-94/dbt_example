{% docs __overview__ %}

# Bem-vindo ao Projeto de Dados
# Overview do Projeto dbt_example

## 📋 Informações Gerais

- **Nome do Projeto**: dbt_example
- **Versão**: 1.0.0
- **Profile**: dbt_example
- **Data Warehouse**: BigQuery

## 🏗️ Arquitetura do Projeto

Este projeto segue a arquitetura padrão do dbt com as seguintes camadas:

### 1. **Staging** (Schema: `staging`)
- **Materialização**: Table
- **Tags**: `['staging']`
- **Propósito**: Limpeza e padronização inicial dos dados brutos

### 2. **Intermediate** (Schema: intermediate)
- **Materialização**: Ephemeral
- **Tags**: `['intermediate']`
- **Propósito**: Transformações intermediárias e lógica de negócio

### 3. **Marts** (Schema: `marts`)
- **Materialização**: Table
- **Tags**: `['marts']`
- **Propósito**: Modelos finais para consumo das aplicações

## 📊 Sources (Fontes de Dados)

### Northwind
- **Schema**: `dbt_dw_seeds`
- **Loader**: BigQuery
- **Descrição**: Dados brutos do sistema de vendas da empresa

#### Tabelas Disponíveis:
- **orders**: Tabela com os pedidos da empresa
  - **Colunas Principais**:
    - `order_id` (PK, unique, not_null)
    - `customer_id`
    - `employee_id`
    - `order_date`
    - `required_date`
    - `shipped_date`
    - `ship_via`
    - `freight`
    - `ship_name`
    - `ship_address`
    - `ship_city`
    - `ship_region`
    - `ship_postal_code`
    - `ship_country`

## 🌱 Seeds (Dados Estáticos)

### Schema: `seeds`

#### Arquivos Principais:
- **country_codes.csv**: Códigos de países
  - Colunas: `country_code`, `country_name`
  - Países: Brazil, United States, Germany

#### ERP Northwind (Schema: `erp_northwind`)
- **categories.csv**: Categorias de produtos (9 registros)
- **customers.csv**: Clientes (93 registros)
- **customer_customer_demo.csv**: Demografia de clientes (2 registros)
- **customer_demographics.csv**: Demografia de clientes (2 registros)
- **employees.csv**: Funcionários (11 registros)
- **employee_territories.csv**: Territórios dos funcionários (51 registros)
- **order_details.csv**: Detalhes dos pedidos (2.157 registros)
- **orders.csv**: Pedidos (832 registros)
- **products.csv**: Produtos (79 registros)
- **region.csv**: Regiões (6 registros)
- **shippers.csv**: Transportadoras (8 registros)
- **suppliers.csv**: Fornecedores (31 registros)
- **territories.csv**: Territórios (55 registros)
- **us_states.csv**: Estados dos EUA (53 registros)

## 🔄 Models

### Staging Models

#### Northwind
- **stg_orders** (`staging/northwind/stg_orders.sql`)
  - **Tags**: `['comercial']`
  - **Descrição**: Modelo de staging para pedidos
  - **Transformações**:
    - Adiciona campo `status_envio` baseado em `shipped_date`
    - Mapeia todos os campos da tabela orders
  - **Dependências**: `source('northwind', 'orders')`

#### Finance
- *Nenhum modelo implementado*

#### SAP
- *Nenhum modelo implementado*

### Intermediate Models

- **int_vendas** (`intermediate/int_vendas.sql`)
  - **Tags**: `['comercial']`
  - **Descrição**: Agregação de vendas por mês e ano
  - **Transformações**:
    - Extrai mês e ano da data do pedido
    - Soma total de frete por período
  - **Dependências**: `ref('stg_orders')`

### Marts Models

#### Comercial

##### Dimensions
- *Nenhuma dimensão implementada*

##### Facts
- **fct_vendas** (`marts/comercial/facts/fct_vendas.sql`)
  - **Tags**: `['comercial']`
  - **Descrição**: Fato de vendas da empresa
  - **Colunas**:
    - `mes`: Mês da venda (not_null, valores aceitos: jan-dez)
    - `ano`: Ano da venda
    - `total_frete`: Total de frete (not_null)
  - **Dependências**: `ref('int_vendas')`

## 🧪 Tests

### Data Tests Implementados
- **stg_orders**:
  - `order_id`: unique, not_null
- **fct_vendas**:
  - `mes`: not_null, accepted_values
  - `total_frete`: not_null

## 📁 Estrutura de Diretórios

```
dbt_example/
├── models/
│   ├── staging/
│   │   ├── finance/
│   │   ├── northwind/
│   │   │   ├── sources.yml
│   │   │   └── stg_orders.sql
│   │   └── sap/
│   ├── intermediate/
│   │   └── int_vendas.sql
│   └── marts/
│       └── comercial/
│           ├── dimensions/
│           └── facts/
│               ├── fct_vendas.sql
│               └── fct_vendas.yml
├── seeds/
│   ├── country_codes.csv
│   └── erp_northwind/
│       ├── categories.csv
│   │   └── [outros arquivos...]
├── macros/
├── tests/
├── snapshots/
├── analyses/
└── dbt_project.yml
```

## 🎯 Domínios de Negócio

### Comercial
- **Objetivo**: Análise de vendas e pedidos
- **Modelos**: stg_orders, int_vendas, fct_vendas
- **Tags**: `['comercial']`

## 📈 Pipeline de Dados

1. **Extração**: Dados brutos do Northwind via BigQuery
2. **Staging**: Limpeza e padronização em `stg_orders`
3. **Intermediate**: Agregação de vendas por período em `int_vendas`
4. **Marts**: Fato final de vendas em `fct_vendas`

## 🔧 Configurações

### Model Configurations
- **Staging**: Materializado como tabelas no schema `staging`
- **Intermediate**: Materializado como ephemeral
- **Marts**: Materializado como tabelas no schema `marts`

### Seed Configurations
- **Schema**: `seeds`
- **Tags**: `['seeds']`

## 🚀 Próximos Passos Sugeridos

1. **Implementar dimensões**:
   - `dim_customers`
   - `dim_employees`
   - `dim_products`
   - `dim_categories`

2. **Expandir fatos**:
   - `fct_order_details`
   - `fct_inventory`

3. **Adicionar modelos de staging**:
   - `stg_customers`
   - `stg_products`
   - `stg_employees`

4. **Implementar snapshots** para controle de mudanças

5. **Adicionar análises** para insights de negócio

6. **Criar macros** para reutilização de código

## 📝 Notas Técnicas

- O projeto utiliza BigQuery como data warehouse
- Implementa testes de qualidade de dados
- Segue as melhores práticas do dbt
- Estrutura modular e escalável

{% enddocs %}