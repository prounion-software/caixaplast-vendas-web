# VENDEDORES

O conceito de vendedores nesse sistema é importado do [Sistema PROC](https://sistemaproc.com.br), e pode parecer um pouco diferente da maioria dos sistemas.

É comum em sistemas de vendas, o vendedor ser o próprio usuário logado na ferramenta ao realizar um pedido. Aqui um vendedor não necessariamente é um usuário também.

Mais adiante os relacionamentos entre usuário e vendedor ficarão mais claros.

## Estrutura de dados

O cadastro de vendedores é algo bastante simples.

### Tabela de vendedores: **sellers**

| Dado                       | Tipo          | Campo na Tabela         | Permite Nulo | Observação            |
| -------------------------- | ------------- | ----------------------- | :----------: | --------------------- |
| Id do vendedor             | Numero        | seller_id               |     Não      | Chave primária        |
| Id externo                 | Texto(10)     | external_id             |     Não      |                       |
| Nome                       | Texto(50)     | name                    |     Não      |                       |
| Percentual desconto máximo | Numero(12, 6) | max_discount_percentage |     Sim      | Valor padrão 0 (zero) |
| E-mail                     | Texto(100)    | email                   |     Sim      |                       |

## Representação em objetos e JSON

Representação interna na aplicação:

```ts
export type Seller = {
  id: number;
  externalId: string;
  name: string;
  maxDiscountPercentage?: number;
  email?: string;
};
```

### Payload de cadastro

`POST /sellers`

```ts
export type SellerPostPayload = {
  name: string;
  externalId: string;
  maxDiscountPercentage?: number;
  email?: string;
};
```

```json
// Exemplo
{
  "name": "Gerson Ferreira",
  "externalId": "VD10",
  "maxDiscountPercentage": 5,
  "email": "gerson.ferreira@gmail.com"
}
```

`PATCH /seller/{:id([0-9]+)}`

```ts
export type SellerPatchPayload = Partial<SellerPostPayload>;
```
