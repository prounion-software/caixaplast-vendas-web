# TABELA DE PREÇOS

Uma tabela de preços é onde são definidos os preços de um grupo de produtos, em todo cliente há uma referência para uma tabela de preços, na inclusão de pedidos os preços listados com base nessa tabela.

Há dois tipos de tabelas de preços: **Simples** e **Por Quantidade**

Na **tabela de preços simples**, o preço do produto é uma combinação de produto e unidade, por exemplo:

| Produto    | Unidade | Preço    |
| ---------- | :-----: | -------- |
| Caixa 15 L |   UN    | R$ 10,00 |
| Caixa 15 L |   PCT   | R$ 53,00 |
| Caixa 15 L |   CT    | R$ 60,00 |
| Caixa 30 L |   UN    | R$ 10,44 |
| Caixa 30 L |   PCT   | R$ 55,32 |

Já na tabela **por quantidade**, há mais um fator determinante para o preço do produto no pedido, que é a quantidade.

Nesse tipo de tabela pode haver até 3 preços diferentes por produto a depender da quantidade solicitada, por exemplo:

| Produto   | Unidade | Quantidade | Preço    |
| --------- | ------- | ---------- | -------- |
| Caixa 3 L | UN      | Até 10     | R$ 1,43  |
| Caixa 3 L | UN      | Até 50     | R$ 1,30  |
| Caixa 3 L | UN      | Até 100    | R$ 1,25  |
| Caixa 3 L | PCT     | Até 10     | R$ 10,12 |
| Caixa 3 L | PCT     | Até 50     | R$ 9,54  |
| Caixa 3 L | PCT     | Até 100    | R$ 8,25  |

Seguindo as boas práticas atuais de mercado, nessa aplicação os preços na base de dados e na comunicação pela API os preços são tratados em centavos, ou seja:

- R$ 1,00 é tratado como 100
- R$ 1.435,66 é tratado como 143566

Isso permite que o armazenamento seja feito em **integer** ao invés de **float** ou **decimal**, só essa mudança economiza muito processamento.

> **Importante**
>
> Uma tabela de preços pode ter menos itens do que o cadastro de produtos, é possível que a equipe responsável monte uma tabela com poucos produtos apenas por uma questão de organização e negociação.
> Na inclusão do pedido, apenas os itens precificados na tabela do cliente deverão ser listados

---

## Estrutura de dados

### Tabela de preços

| Dado         |  Tipo  | Descrição                               | Campo na Tabela | Permite Nulo |
| ------------ | :----: | --------------------------------------- | --------------- | :----------: |
| id da tabela | Número | ID interno na aplicação                 | tabela_preco_id |     Não      |
| id externo   | Texto  | ID no ERP                               | id_externo      |     Não      |
| descrição    | Texto  | Descrição da tabela                     | descricao       |     Não      |
| tipo         |  Enum  | Tipo da tabela (Simples/Por Quantidade) | tipo            |     Não      |

### Itens da tabela de preços

| Dado          |  Tipo  | Descrição                               | Campo na Tabela | Permite Nulo |
| ------------- | :----: | --------------------------------------- | --------------- | :----------: |
| id da tabela  | Número | ID interno da tabela                    | tabela_preco_id |     Não      |
| id do produto | Número | ID interno do produto                   | produto_id      |     Não      |
| unidade       | Texto  | Unidade do produto                      | unidade         |     Não      |
| preço         | Número | Preço padrão do produto                 | preco           |     Não      |
| até 1         | Número | Primeira quantidade "Até X"             | ate_1           |     Não      |
| preço 1       | Número | Preço do produto da primeira quantidade | preco_1         |     Não      |
| até 2         | Número | Segunda quantidade "Até X"              | ate_2           |     Sim      |
| preço 2       | Número | Preço do produto da segunda quantidade  | preco_2         |     Sim      |
| até 3         | Número | Terceira quantidade "Até X"             | ate_3           |     Sim      |
| preço 3       | Número | Preço do produto da terceira quantidade | preco_3         |     Sim      |

## Representação em JSON

### Tabela de preços

#### Payload de cadastro

`// POST /tabelas-preco`

```json
{
  "idExterno": "",
  "descricao": "",
  "tipo": 0
}
```

#### Payload de retorno de cadastro ou leitura

`// GET /tabelas-preco/{id}`

```json
{
  "id": 0,
  "idExterno": "",
  "descricao": "",
  "tipo": 0
}
```

### Itens da tabela de preços

### Payload de cadastro

`// POST /tabela-preco/{id}/itens`

```json
{
  "idProduto": 0,
  "preco": 0,
  "ate1": 0,
  "preco1": 0,
  "ate2": 0,
  "preco2": 0,
  "ate3": 0,
  "preco3": 0
}
```
