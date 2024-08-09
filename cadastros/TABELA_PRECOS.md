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
| moeda         | Texto (3)  | Moeda                                   | currency_id     |     Não      |
| preço         |   Número   | Preço padrão do produto                 | price           |     Sim      |
| até 1         |   Número   | Primeira quantidade "Até X"             | until_1         |     Sim      |
| preço 1       |   Número   | Preço do produto da primeira quantidade | price_1         |     Sim      |
| até 2         |   Número   | Segunda quantidade "Até X"              | until_2         |     Sim      |
| preço 2       |   Número   | Preço do produto da segunda quantidade  | price_2         |     Sim      |
| até 3         |   Número   | Terceira quantidade "Até X"             | until_3         |     Sim      |
| preço 3       |   Número   | Preço do produto da terceira quantidade | price_3         |     Sim      |
| até 4         |   Número   | Quarta quantidade "Até X"               | until_4         |     Sim      |
| preço 4       |   Número   | Preço do produto da quarta quantidade   | price_4         |     Sim      |

## Representação em JSON

### Tabela de preços

Representação interna na aplicação:

```ts
export enum PriceTableType {
  Simple = 1,
  ByQuantity = 2,
}

export type PriceTable = {
  id: number;
  externalId: string;
  description: string;
  type: PriceTableType;
};
```

#### Payload de cadastro

`POST /price-tables`

```ts
export type PriceTablePostPayload = {
  externalId: string;
  description: string;
  type: PriceTableType;
};
```

```json
// Exemplo
{
  "externalId": "001",
  "description": "Venda parcelada",
  "type": "Simple"
}
```

`PATCH /price-table/{:id([0-9]+)}`

```ts
export type PriceTablePatchPayload = Partial<PriceTablePostPayload>;
```

```json
// Exemplo PATCH /price-table/124
{
  "description": "Vendas parcelada",
  "type": "ByQuantity"
}
```

### Itens da tabela de preços

Representação interna na aplicação:

```ts
export type PriceTableItem = {
  productId: number; // Id do produto
  price: number; // Preço
  unit: string; // Unidade
  currencyId: string; // ID da Moeda
  until1: number; // Primeira quantidade: até x
  price1: number; // Preço da primeira quantidade
  until2: number; // Segunda quantidade: até x
  price2: number; // Preço da segunda quantidade
  until3: number; // Terceira quantidade: até x
  price3: number; // Preço da terceira quantidade
  until4: number; // Quarta quantidade: até x
  price4: number; // Preço da quarta quantidade
};
```

#### Payload de cadastro

**IMPORTANTE:** Algumas validações mudam dependendo do tipo da tabela.

- Se o campo **currencyId** não for informado, devemos assumir o padrão **BRL**.
- Um produto pode estar varias vezes na mesma tabela, contanto que em unidades diferentes. Não podemos permitir um produto com unidades repetidas ou com unidades não cadastradas no sistema.
- Se a tabela for do tipo "simples": O campo **price** é obrigatório e deve ser maior que zero, caso a tabela for por quantidade esse campo não precisa ser validado.
- Se a tabela for do tipo "por quantidade": Os campos **until1** e **price1** são obrigatórios e devem ser maiores que zero, os demais campos **untilX** e **priceX** são opcionais, mas devemos observar as regras abaixo.
  - Os pares devem ser informados: Se o campo **until2** for preenchido, então **price2** também deve estar
  - Não se pode pular sequências: Exemplo, não podemos informar o **until4** e **price4**, sem informar os anteriores.

`POST /price-table/{:priceTableId([0-9]+)}/items`

```ts
export type PriceTableItemPostPayload = {
  productId: number;
  unit: string;
  currencyId?: string;
  price?: number;
  until1?: number;
  price1?: number;
  until2?: number;
  price2?: number;
  until3?: number;
  price3?: number;
  until4?: number;
  price4?: number;
};
```

```json
// Exemplo de item para uma tabela de preço simples
// POST /price-table/789/items
{
  "productId": 124,
  "price": 440, // R$ 4,40
  "unit": "UN",
  "currencyId": "BRL"
}
```

```json
// Exemplo de item para uma tabela de preço por quantidade
// POST /price-table/789/items
{
  "productId": 124,
  "unit": "UN",
  "currencyId": "BRL",
  "until1": 10,
  "price1": 397, // R$ 3,97
  "until2": 50,
  "price2": 365 // R$ 3,65
}
```

`PATCH /price-table/{:priceTableId([0-9]+)}/item/{:productId([0-9]+)}/unit/{:unit}`

Perceba que na rota, além do id do produto, também há a unidade.
Isso porque um produto pode estar na tabela em diferentes unidades, por isso precisamos saber para qual unidade está sendo realizado o patch.

```ts
export type PriceTableItemPatchPayload = {
  currencyId?: string;
  price?: number;
  until1?: number;
  price1?: number;
  until2?: number;
  price2?: number;
  until3?: number;
  price3?: number;
  until4?: number;
  price4?: number;
};
```

```json
// Exemplo de item para uma tabela de preço simples
// POST /price-table/789/item/124/unit/UN
{
  "productId": 124,
  "price": 440, // R$ 4,40
  "currencyId": "BRL"
}
```

```json
// Exemplo de item para uma tabela de preço por quantidade
// POST /price-table/789/item/124/unit/UN
{
  "productId": 124,
  "currencyId": "BRL",
  "until1": 10,
  "price1": 397, // R$ 3,97
  "until2": 50,
  "price2": 365 // R$ 3,65
}
```
