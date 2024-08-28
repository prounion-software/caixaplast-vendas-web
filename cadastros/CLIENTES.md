# CLIENTES

O cadastro de clientes, é bastante complexo, com diversos atributos e associações com outras entidades.
Assim como a entidade de produtos, a entidade de clientes é essencial para este projeto.

Assim como foi apresentado em [produtos](./PRODUTOS.md), primeiro iremos apresentar aqui as estruturas de dados e tabelas e depois a relação entre elas e a operação no cadastro.

## Estrutura de dados

### Tabela de tipos de clientes: **customer_types**

| Dado       |    Tipo     | Campo na Tabela  | Permite Nulo | Observação                      |
| ---------- | :---------: | ---------------- | :----------: | ------------------------------- |
| ID interno |   Número    | customer_type_id |     Não      | Chave primária, auto-incremento |
| ID no ERP  | Texto (10)  | external_id      |     Não      |                                 |
| Descrição  | Texto (100) | description      |     Não      |                                 |

### Tabela de endereços dos clientes: **customer_addresses**

| Dado          |    Tipo     | Campo na Tabela | Permite Nulo | Observação                      |
| ------------- | :---------: | --------------- | :----------: | ------------------------------- |
| ID interno    |   Número    | address_id      |     Não      | Chave primária, auto-incremento |
| ID do cliente |   Número    | customer_id     |     Não      |                                 |
| ID no ERP     | Texto (10)  | external_id     |     Sim      | Nem sempre vai estar preenchido |
| Logradouro    | Texto (100) | street_name     |     Não      |                                 |
| Complemento   | Texto (100) | additional_info |     Sim      |                                 |
| Número        | Texto (10)  | street_number   |     Sim      |                                 |
| Bairro        | Texto (100) | neighborhood    |     Sim      |                                 |
| CEP           | Texto (10)  | postal_code     |     Sim      |                                 |
| UF            |  Texto (2)  | state           |     Sim      |                                 |
| IBGE          |   Número    | ibge            |     Sim      | IBGE é um código da cidade      |
| Tipo          | Texto (20)  | type            |     Não      |                                 |

### Tabela de transportadoras: **carriers**

| Dado       |    Tipo     | Campo na Tabela | Permite Nulo | Observação                      |
| ---------- | :---------: | --------------- | :----------: | ------------------------------- |
| ID interno |   Número    | carrier_id      |     Não      | Chave primária, auto-incremento |
| ID no ERP  | Texto (10)  | external_id     |     Não      |                                 |
| Descrição  | Texto (100) | description     |     Não      |                                 |

### Tabela de tipos de cobrança: **payment_methods**

Aqui se refere ao meio de pagamento (cobrança) como cheque, cartão, boleto, pix...

| Dado       |    Tipo     | Campo na Tabela   | Permite Nulo | Observação                      |
| ---------- | :---------: | ----------------- | :----------: | ------------------------------- |
| ID interno |   Número    | payment_method_id |     Não      | Chave primária, auto-incremento |
| ID no ERP  | Texto (10)  | external_id       |     Não      |                                 |
| Descrição  | Texto (100) | description       |     Não      |                                 |

### Tabela de formas de pagamento: **payment_terms**

Aqui se refere a forma de pagamento como à vista, à prazo, no carnê...

| Dado       |    Tipo     | Campo na Tabela | Permite Nulo | Observação                      |
| ---------- | :---------: | --------------- | :----------: | ------------------------------- |
| ID interno |   Número    | payment_term_id |     Não      | Chave primária, auto-incremento |
| ID no ERP  | Texto (10)  | external_id     |     Não      |                                 |
| Descrição  | Texto (100) | description     |     Não      |                                 |

### Tabela de posição financeira: **customer_financial_positions**

Só deve haver um registro por cliente, portanto o código do cliente deve fazer parte da chave da tabela

| Dado               |    Tipo    | Campo na Tabela  | Permite Nulo | Observação               |
| ------------------ | :--------: | ---------------- | :----------: | ------------------------ |
| ID do cliente      |   Número   | customer_id      |     Não      | Chave primária da tabela |
| Limite de crédito  |   Número   | credit_limit     |     Não      | Em centavos              |
| Crédito disponível |   Número   | credit_available |     Não      | Em centavos              |
| Bloqueado          | Número (1) | blocked          |     Não      | 0 = False / 1 = True     |

### Tabela de rotas de entrega: **delivery_routes**

| Dado       |    Tipo     | Campo na Tabela | Permite Nulo | Observação                      |
| ---------- | :---------: | --------------- | :----------: | ------------------------------- |
| ID interno |   Número    | route_id        |     Não      | Chave primária, auto-incremento |
| ID no ERP  | Texto (10)  | external_id     |     Não      |                                 |
| Descrição  | Texto (100) | description     |     Não      |                                 |

### Tabela de contatos do cliente: **customer_contacts**

| Dado               |    Tipo     | Campo na Tabela | Permite Nulo | Observação                      |
| ------------------ | :---------: | --------------- | :----------: | ------------------------------- |
| ID interno         |   Número    | contact_id      |     Não      | Chave primária, auto-incremento |
| ID do cliente      |   Número    | customer_id     |     Não      |                                 |
| ID no ERP          | Texto (10)  | external_id     |     Sim      |                                 |
| Nome               | Texto (100) | name            |     Não      |                                 |
| Setor/Departamento | Texto (100) | department      |     Sim      |                                 |
| Telefone           | Texto (20)  | phone_number    |     Sim      |                                 |
| E-mail             | Texto (100) | email           |     Sim      |                                 |

### Tabela de clientes: **customers**

É possível notar que alguns campos estão em português, pois são termos que não tem conversão direta para o inglês.
Tentar converter poderia tirar do contexto.

| Dado               |    Tipo     | Campo na Tabela   | Permite Nulo | Observação                      |
| ------------------ | :---------: | ----------------- | :----------: | ------------------------------- |
| ID interno         |   Número    | customer_id       |     Não      | Chave primária, auto-incremento |
| ID no ERP          | Texto (10)  | external_id       |     Não      |                                 |
| Tipo de pessoa     |  Texto (1)  | person_type       |     Não      | J = Jurídica / F = Física       |
| Razão Social       | Texto (100) | name              |     Não      |                                 |
| Fantasia           | Texto (100) | trade_name        |     Não      |                                 |
| CNPJ/CPF           | Texto (100) | cnpj              |     Não      |                                 |
| IE/RG              | Texto (20)  | ie                |     Sim      |                                 |
| Tipo de cliente    |   Número    | customer_type_id  |     Sim      | Ref. customer_types             |
| E-mail             | Texto (100) | email             |     Sim      |                                 |
| Observações        | Texto (500) | additional_info   |     Sim      |                                 |
| Suframa            | Texto (20)  | suframa           |     Sim      |                                 |
| Transportadora     |   Número    | carrier_id        |     Sim      | Ref. carriers                   |
| Info. entrega      | Texto (100) | delivery_info     |     Sim      |                                 |
| Tabela de preços   |   Número    | price_table_id    |     Sim      | Ref. price_tables               |
| Vendedor           |   Número    | seller_id         |     Sim      | Ref. sellers                    |
| Representante      |   Número    | agent_seller_id   |     Sim      | Ref. users                      |
| Meio de pagamento  |   Número    | payment_method_id |     Sim      | Ref. payment_methods            |
| Forma de pagamento |   Número    | payment_term_id   |     Sim      | Ref. payment_terms              |
| Rota de entrega    |   Número    | delivery_route_id |     Sim      | Ref. delivery_routes            |
| Subst. Tributária  |   Número    | subst_tribut      |     Sim      | 0 = False / 1 = True            |
| Consumidor Final   |   Número    | consumidor_final  |     Sim      | 0 = False / 1 = True            |
| Optante Simples    |   Número    | simples_nacional  |     Sim      | 0 = False / 1 = True            |
| Órgão público      |   Número    | orgao_publico     |     Sim      | 0 = False / 1 = True            |

## Estrutura de dados na aplicação

A maior parte dos cadastros é a mesma forma do que foi feito até aqui na aplicação.

### Transportadoras

`POST /carriers`

```ts
export type CarrierPostPayload = {
  externalId: string;
  description: string;
};
```

`PATCH /carrier/:id([0-9]+)`

```ts
export type CarrierPatchPayload = Partial<CarrierPostPayload>;
```

### Tipos de cobrança

`POST /payment-methods`

```ts
export type PaymentMethodPostPayload = {
  externalId: string;
  description: string;
};
```

`PATCH /payment-method/:id([0-9]+)`

```ts
export type PaymentMethodPatchPayload = Partial<PaymentMethodPostPayload>;
```

### Formas de pagamento

`POST /payment-terms`

```ts
export type PaymentTermPostPayload = {
  externalId: string;
  description: string;
};
```

`PATCH /payment-method/:id([0-9]+)`

```ts
export type PaymentTermPatchPayload = Partial<PaymentTermPostPayload>;
```

### Rotas de entrega

`POST /delivery-routes`

```ts
export type DeliveryRoutePostPayload = {
  externalId: string;
  description: string;
};
```

```ts
export type DeliveryRoutePatchPayload = Partial<DeliveryRoutePostPayload>;
```

### Tipos de clientes

`POST /customers/customers-types`

```ts
export type CustomerTypePostPayload = {
  externalId: string;
  description: string;
};
```

`PATCH /customers/customers-type/:id([0-9]+)`

```ts
export type CustomerTypePatchPayload = Partial<CustomerTypePostPayload>;
```

### Clientes

`POST /customers`

```ts
export type CustomerPostPayload = {
  externalId: string;
  personType: string; // As opções são apenas F ou J
  name: string; // Razão Social
  tradeName: string; // Fantasia
  cnpj: string; // CNPJ ou CPF
  ie?: string; // IE ou RG
  email?: string;
  additionalInfo?: string;
  suframa?: string;
  deliveryInfo?: string;
  substTributaria?: boolean; // Padrão = false
  consumidorFinal?: boolean; // Padrão = false
  optanteSimples?: boolean; // Padrão = false
  orgaoPublico?: boolean; // Padrão = false
  customerTypeId?: number; // ID do tipo de cliente
  carrierId?: number; // ID da transportadora
  priceTableId?: number; // ID da tabela de preços
  sellerId?: number; // ID do vendedor
  agentSellerId?: number; // ID do representante
  paymentMethodId?: number; // ID da cobrança
  paymentTermId?: number; // ID da forma de pagamento
  deliveryRouteId?: number; // ID da transportadora
};
```

`PATCH /customer/:id([0-9]+)`

```ts
export type CustomerPatchPayload = Partial<CustomerPostPayload>;
```

`GET /customers`

Como é uma entidade com muitos relacionamentos, e muitos deles são usados apenas no momento do pedido. Na listagem não precisamos de todos os campos, teremos uma versão reduzida desses dados.

```ts
export type CustomerListItem = {
  id: number;
  externalId: string;
  name: string; // Razão Social
  tradeName: string; // Fantasia
  cnpj: string; // CNPJ ou CPF
};
```

`GET /customer/:id([0-9]+)`

Quando o acesso é direto ao cliente, aí retornamos as informações comples, pois certamente trata-se de um pedido ou manutenção de cadastro.

```ts
export type Customer = {
  id: number;
  externalId: string;
  personType: string;
  name: string;
  tradeName: string;
  cnpj: string;
  ie: string;
  email: string;
  additionalInfo: string;
  suframa: string;
  deliveryInfo: string;
  substTributaria: boolean;
  consumidorFinal: boolean;
  optanteSimples: boolean;
  orgaoPublico: boolean;
  contacts: CustomerContact[];
  addresses: CustomerAddress[];
  financialPosition: CustomerFinancialPosition | null;
  customerType: CustomerType | null;
  carrier: Carrier | null;
  priceTable: PriceTable | null;
  seller: Seller | null;
  agentSeller: User | null;
  paymentMethod: PaymentMethod | null;
  paymentTerm: PaymentTerm | null;
  deliveryRoute: DeliveryRoute | null;
};
```

### Endereços do cliente

`POST /customer/:customerId([0-9]+)/addresses`

```ts
export type CustomerAddressPostPayload = {
  externalId?: string;
  type: string; // As opções são: business, delivery ou financial
  streetName: string;
  streetNumber?: string;
  additionalInfo?: string;
  neighborhood?: string;
  postalCode?: string;
  state?: string;
  ibge?: number;
};
```

`PATCH /customer/:customerId([0-9]+)/address/:addressId([0-9]+)`

```ts
export type CustomerAddressPatchPayload = Partial<CustomerAddressPostPayload>;
```

### Posição financeira do cliente

Essa rota é diferente das demais, só tem o `patch`.
Se não houver o registro na tabela, o registro é criado. Se houver, ele é atualizado.

`PATCH /customer/:customerId([0-9]+)/financial-position`

```ts
export type CustomerFinancialPositionPostPayload = {
  creditLimit: number;
  creditAvailable: number;
  blocked: boolean;
};
```

### Contatos do cliente

`POST /customer/:customerId([0-9]+)/contacts`

```ts
export type CustomerContactPostPayload = {
  externalId?: string;
  name: string;
  departament?: string;
  phoneNumber?: string;
  email?: string;
};
```

`PATCH /customer/:customerId([0-9]+)/contact/:contactId([0-9]+s)`

```ts
export type CustomerContactPatchPayload = Partial<CustomerContactPostPayload>;
```
