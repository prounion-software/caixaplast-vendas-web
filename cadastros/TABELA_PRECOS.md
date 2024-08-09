# TABELA DE PREÇOS

Uma tabela de preços é onde são definidos os preços de um grupo de produtos, em todo cadastro de cliente há uma referência para uma tabela de preços. É com base nessa associação que serão apresentados os preços no momento da criação dos pedidos.

Há dois tipos de tabelas de preços: **Simples** e **Por Quantidade**

Na **tabela de preços simples**, o preço do produto é uma combinação de [produto](./PRODUTOS.md), unidade e [moeda](./MOEDAS.md), por exemplo:

| Produto    | Unidade | Moeda | Preço |
| ---------- | :-----: | :---: | ----- |
| Caixa 15 L |   UN    |  BRL  | 10,00 |
| Caixa 15 L |   PCT   |  BRL  | 53,00 |
| Caixa 15 L |   CT    |  BRL  | 60,00 |
| Caixa 30 L |   UN    |  USD  | 10,44 |
| Caixa 30 L |   PCT   |  USD  | 55,32 |

Já na tabela **por quantidade**, há mais um fator determinante para o preço do produto no pedido, que é a quantidade.

Nesse tipo de tabela pode haver até 4 preços diferentes por produto a depender da quantidade solicitada, por exemplo:

| Produto   | Unidade | Quantidade | Moeda | Preço |
| --------- | ------- | ---------- | :---: | ----- |
| Caixa 3 L | UN      | Até 10     |  BRL  | 1,43  |
| Caixa 3 L | UN      | Até 50     |  BRL  | 1,30  |
| Caixa 3 L | UN      | Até 100    |  BRL  | 1,25  |
| Caixa 3 L | UN      | Até 200    |  BRL  | 1,10  |
| Caixa 3 L | PCT     | Até 10     |  BRL  | 10,12 |
| Caixa 3 L | PCT     | Até 50     |  BRL  | 9,54  |
| Caixa 3 L | PCT     | Até 100    |  BRL  | 8,25  |
| Caixa 3 L | PCT     | Até 200    |  BRL  | 8,05  |

Seguindo as boas práticas atuais de mercado, nessa aplicação os preços na base de dados e na comunicação pela API os preços são tratados em centavos, ou seja:

- R$ 1,00 é tratado como 100
- R$ 1.435,66 é tratado como 143566

Isso permite que o armazenamento seja feito em **integer** ao invés de **float** ou **decimal**, só essa mudança economiza muito processamento.

> **Importante**
>
> Uma tabela de preços **pode ter menos itens do que o cadastro de produtos**, é possível que a equipe responsável monte uma tabela com poucos produtos apenas por uma questão de organização e negociação.
> Na inclusão do pedido, apenas os itens precificados na tabela do cliente deverão ser listados

A manutenção dos cadastros das tabelas de preços é realizado pelo ERP, portanto não precisamos criar uma área de manutenção na aplicação.

---

## Estrutura de dados

### Tabela de preços: **price_tables**

| Dado         |    Tipo     | Descrição                               | Campo na Tabela | Permite Nulo |
| ------------ | :---------: | --------------------------------------- | --------------- | :----------: |
| id da tabela |   Número    | ID interno na aplicação                 | price_table_id  |     Não      |
| id externo   | Texto (10)  | ID no ERP                               | external_id     |     Não      |
| descrição    | Texto (100) | Descrição da tabela                     | description     |     Não      |
| tipo         |    Enum     | Tipo da tabela (Simples/Por Quantidade) | type            |     Não      |

### Itens da tabela de preços: **price_table_itens**

| Dado          |    Tipo    | Descrição                               | Campo na Tabela | Permite Nulo |
| ------------- | :--------: | --------------------------------------- | --------------- | :----------: |
| id da tabela  |   Número   | ID interno da tabela                    | price_table_id  |     Não      |
| id do produto |   Número   | ID interno do produto                   | product_id      |     Não      |
| unidade       | Texto (10) | Unidade do produto                      | unit            |     Não      |
| preço         |   Número   | Preço padrão do produto                 | price           |     Não      |
| até 1         |   Número   | Primeira quantidade "Até X"             | until_1         |     Não      |
| preço 1       |   Número   | Preço do produto da primeira quantidade | price_1         |     Não      |
| até 2         |   Número   | Segunda quantidade "Até X"              | until_2         |     Sim      |
| preço 2       |   Número   | Preço do produto da segunda quantidade  | price_2         |     Sim      |
| até 3         |   Número   | Terceira quantidade "Até X"             | until_3         |     Sim      |
| preço 3       |   Número   | Preço do produto da terceira quantidade | price_3         |     Sim      |

## Representação em JSON

### Tabela de preços

#### Payload de cadastro

`// POST /price-tables`

```json
{
  "external_id": "",
  "description": "",
  "type": 0
}
```

#### Payload de retorno de cadastro ou leitura

`// GET /price-table/{id}`

```json
{
  "id": 0,
  "externalId": "",
  "description": "",
  "type": 0
}
```

### Itens da tabela de preços

### Payload de cadastro

`// POST /price-table/{id}/itens`

```json
{
  "productId": 0,
  "price": 0,
  "unit": "",
  "until1": 0,
  "price1": 0,
  "until2": 0,
  "price2": 0,
  "until3": 0,
  "price3": 0
}
```
