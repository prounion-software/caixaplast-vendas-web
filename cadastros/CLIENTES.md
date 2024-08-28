# CLIENTES

O cadastro de clientes, é bastante complexo, com diversos atributos e associações com outras entidades.
Assim como a entidade de produtos, a entidade de clientes é essencial para este projeto.

Assim como foi apresentado em [produtos](./PRODUTOS.md), primeiro iremos apresentar aqui as estruturas de dados e tabelas e depois a relação entre elas e a operação no cadastro.

## Estrutura de dados

### Tabela de tipos de clientes: **custumer_types**

| Dado       |    Tipo     | Campo na Tabela  | Permite Nulo | Observação                      |
| ---------- | :---------: | ---------------- | :----------: | ------------------------------- |
| ID interno |   Número    | customer_type_id |     Não      | Chave primária, auto-incremento |
| ID no ERP  | Texto (10)  | external_id      |     Não      |                                 |
| Descrição  | Texto (100) | description      |     Não      |                                 |

### Tabela de endereços dos clientes: **custumer_addresses**

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

### Tabela de tipos de cobrança: **payment_terms**

Aqui se refere a forma de pagamento como à vista, à prazo, no carnê...

| Dado       |    Tipo     | Campo na Tabela | Permite Nulo | Observação                      |
| ---------- | :---------: | --------------- | :----------: | ------------------------------- |
| ID interno |   Número    | payment_term_id |     Não      | Chave primária, auto-incremento |
| ID no ERP  | Texto (10)  | external_id     |     Não      |                                 |
| Descrição  | Texto (100) | description     |     Não      |                                 |

### Tabela de posição financeira: **customer_financial_positions**

| Dado               |    Tipo    | Campo na Tabela  | Permite Nulo | Observação           |
| ------------------ | :--------: | ---------------- | :----------: | -------------------- |
| ID do cliente      |   Número   | customer_id      |     Não      |                      |
| Limite de crédito  |   Número   | credit_limit     |     Não      | Em centavos          |
| Crédito disponível |   Número   | credit_available |     Não      | Em centavos          |
| Bloqueado          | Número (1) | blocked          |     Não      | 0 = False / 1 = True |

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

| Dado               |    Tipo     | Campo na Tabela   | Permite Nulo | Observação                      |
| ------------------ | :---------: | ----------------- | :----------: | ------------------------------- |
| ID interno         |   Número    | customer_id       |     Não      | Chave primária, auto-incremento |
| ID no ERP          | Texto (10)  | external_id       |     Não      |                                 |
| Tipo de pessoa     |  Texto (1)  | person_type       |     Não      | J = Jurídica / F = Física       |
| Razão Social       | Texto (100) | name              |     Não      |                                 |
| Fantasia           | Texto (100) | trade_name        |     Não      |                                 |
| CNPJ/CPF           | Texto (100) | cnpj              |     Não      |                                 |
| IE/RG              | Texto (20)  | ie                |     Sim      |                                 |
| Tipo de cliente    |   Número    | customer_type_id  |     Sim      | Ref. custumer_types             |
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
