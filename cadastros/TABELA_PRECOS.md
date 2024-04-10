# TABELA DE PREÇOS

Uma tabela de preços é onde são definidos os preços de um grupo de produtos, em todo cliente há uma referência para uma tabela de preços, na inclusão de pedidos os preços listados com base nessa tabela.

Uma tabela pode ter até 3 preços diferentes por produto a depender da quantidade solicitada, por exemplo:

| Quantidade | Preço   |
| ---------- | ------- |
| Até 10     | R$ 1,43 |
| Até 50     | R$ 1,30 |
| Até 100    | R$ 1,25 |

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

| Dado         |  Tipo  | Descrição               | Campo na Tabela | Permite Nulo |
| ------------ | :----: | ----------------------- | --------------- | :----------: |
| id da tabela | Número | ID interno na aplicação | tabela_preco_id |     Não      |
| id externo   | Texto  | ID no ERP               | id_externo      |     Não      |
| descrição    | Texto  | Descrição da tabela     | descricao       |     Não      |

### Itens da tabela de preços

| Dado          |  Tipo  | Descrição                               | Campo na Tabela | Permite Nulo |
| ------------- | :----: | --------------------------------------- | --------------- | :----------: |
| id do produto | Número | ID interno do produto                   | produto_id      |     Não      |
| id da tabela  | Número | ID interno da tabela                    | tabela_preco_id |     Não      |
| até 1         | Número | Primeira quantidade "Até X"             | ate_1           |     Não      |
| preço 1       | Número | Preço do produto da primeira quantidade | preco_1         |     Não      |
| até 2         | Número | Segunda quantidade "Até X"              | ate_2           |     Sim      |
| preço 2       | Número | Preço do produto da segunda quantidade  | preco_2         |     Sim      |
| até 3         | Número | Terceira quantidade "Até X"             | ate_3           |     Sim      |
| preço 3       | Número | Preço do produto da terceira quantidade | preco_3         |     Sim      |

## Representação em JSON

### Tabela de preços

#### Payload de cadastro

```json
// POST /tabelas-preco
{
  "idExterno": "", // id no ERP
  "descricao": "" // descrição da tabela
}
```

#### Payload de retorno de cadastro ou leitura

```json
// GET /tabelas-preco/{id}
{
  "id": 0, // id interno da aplicação
  "idExterno": "", // id no ERP
  "descricao": "" // descrição da tabela
}
```

### Itens da tabela de preços

### Payload de cadastro

```json
// POST /tabela-preco/{id}/itens
{
  "idProduto": 0, // id interno do produto na aplicação
  "ate1": 0, // primeira quantidade (até X)
  "preco1": 0, // preço do produto da primeira quantidade em centavos
  "ate2": 0, // segunta quantidade (até x) (opcional)
  "preco2": 0, // preço do produto da segunda quantidade em centavos (opcional)
  "ate3": 0, // terceira quantidade (até x) (opcional)
  "preco3": 0 // preço do produto da terceira quantidade em centavos (opcional)
}
```
