# MOEDAS

O sistema de vendas é multi moeda, isso significa que produtos podem ser comercializados em outras moedas além do real (BRL/R$), como dólar americano (USD/US$), euro (EUR/€)...

Diferentemente de outros cadastros, nessas entidades os IDs não serão gerados automaticamente e não haverá um **id externo**, pois os códigos de moedas seguem um padrão internacional.

## Câmbio / Variação / Cotação

A moeda padrão do sistema sempre será o real (BRL) e a cotação dela sempre será 1 para 1, ou seja, R$ 1,00 sempre valerá R$ 1,00.

Para outras moedas teremos a definição da cotação da moeda, operacionalmente isso pode variar diariamente.

### Visão geral das moedas e a cotação perante o real (BRL)

|      Moeda      | Código | Símbolo Monetário | Cotação | Em reais |
| :-------------: | :----: | :---------------: | :-----: | :------: |
| Real Brasileiro |  BRL   |        R$         |    1    | R$ 1,00  |
| Dólar Americano |  USD   |        US$        |   5,5   | R$ 5,50  |
|      Euro       |  EUR   |         €         |  6,01   | R$ 6,01  |
|   Yuan Chinês   |  CNY   |         ¥         |  0,77   | R$ 0,77  |
|  Iene Japonês   |  JPY   |         ¥         |  0,038  | R$ 0,038 |

Dessa forma, se um produto em uma tabela de preços estiver definido com custo de 2,3 dólares americanos (USD), devemos multiplicar 2,3 por 5,5 para chegar o valor em reais (BRL): **R$ 12,65**

## Por que um produto estaria como dólar na tabela de preços?

Um dos motivos é quando o produto vendido é importado, e todo o seu custo é baseado em dólar, então também será precificado em dólar. Mas o operador do sistema (vendedor/representante), visualizará em reais, pois é a moeda usada com o cliente.

Ou seja, para o operador do sistema tudo é sempre real (BRL), as moedas e cotações são usadas internamente do sistemas para os cálculos dos preços que serão apresentados.

## Estrutura de dados

### Tabela de moedas: **currencies**

Neste tabela, todos os campos são obrigatórios.

| Dado                |     Tipo      | Campo na Tabela    | Observação            |
| ------------------- | :-----------: | ------------------ | --------------------- |
| Código              |   Texto (3)   | currency_id        | Chave primária        |
| Descrição           |  Texto (50)   | description        |                       |
| Descrição no plural |  Texto (50)   | plural_description |                       |
| Símbolo monetário   |  Texto (10)   | symbol             |                       |
| Cotação             | Number(12, 3) | quote              | Sempre maior que zero |

### Tabela de histórico de cotações: **currency_quote_history**

Um histórico de cotação, armazena os valores das moeda ao longo do tempo, é como um log das alterações, sempre que uma moeda for cadastrada ou ter a cotação alterada registramos essa ação no histórico.

| Dado                     |     Tipo      | Campo na Tabela |
| ------------------------ | :-----------: | --------------- |
| Código da moeda          |   Texto (3)   | currency_id     |
| Data e hora da alteração |   DateTime    | date            |
| Cotação antiga           | Number(12, 3) | old_quote       |
| Nova cotação             | Number(12, 3) | new_quote       |

No caso de novos cadastros, nesse histórico os campos **old_quote** e **new_quote** vão receber o mesmo valor que está sendo cadastrado como **quote** pelo processo de inclusão do registro.

E nas alterações, o campo **old_quote** receberá o valor antes da alteração e o campo **new_quote** receberá o novo valor de cotação.

### Dados na aplicação

#### Moeda

Estrutura interna na aplicação

```ts
export type Currency = {
  id: string;
  description: string;
  pluralDescription: string;
  symbol: string;
  quote: number;
};
```

**Payload de cadastro**

`POST /currencies`

```ts
export type CurrencyPostPayload = {
  id: string;
  description: string;
  pluralDescription: string;
  symbol: string;
  quote: number;
};
```

```json
// Exemplo
{
  "id": "USD",
  "description": "Dólar Americano",
  "pluralDescription": "Dólares",
  "symbol": "US$",
  "quote": 5.5
}
```

`PATCH /currencies/{:id}`

```ts
export type CurrencyPatchPayload = {
  description?: string;
  pluralDescription?: string;
  symbol?: string;
  quote?: number;
};
```

```json
// Exemplo: PATCH /currencies/USD
{
  "description": "Dólar",
  "symbol": "US$",
  "quote": 5.3
}
```

#### Validações importantes

- O id da moeda é único, não podem haver 2 ou mais cadastrados com o mesmo ID, se alguém tentar cadastrar um registro com um ID já cadastrado, devemos retornar status **409 (conflict)**;

- O valor da cotação sempre deve ser maior que zero.
