# CARTEIRA DE CLIENTES

Uma carteira de clientes nada mais é do que a relação de clientes associados a um vendedor.
Um vendedor pode ter infinitos clientes, mas cada cliente pertence a um vendedor.

Essa relação já é feita na tabela `customers` através do campo `seller_id`.
O processo descrito aqui, é apenas a listagem de clientes por vendedor que será usada no processo de criação do pedido.

Perceba que a estrutura de dados dos clientes nesse ponto é mais simplificada, por dois motivos principais:

- Não apresentar todos os dados ao usuário nesse momento;
- Economizar banda e processamento;

### Listagem dos clientes

`GET seller/:id([0-9]+)/customer-list`

Deve retornar um array com a estrutura abaixo, baseada no que já existe do cadastro de clientes.
Essa rota deve permitir paginação.

```ts
export type CustomerList = {
  id: number; // ID do cliente na aplicação
  externalId: string; // ID do cliente no ERP
  name: string; // Razão Social
  tradeName: string; // Fantasia
  cnpj: string; // CNPJ ou CPF
};
```

Nessa rota, também deve ser possível filtros de pesquisa via `query string` para qualquer campo da tabela `customers` que não seja uma referência de outra tabela. Por exemplo:

`GET seller/:id([0-9]+)/customer-list?name=paulo&cnpj=6768`

No exemplo acima, estamos listando apenas os clientes em que o nome tenha alguma parte com "paulo", como "João Paulo", "Paulo César".... E da mesma forma tenha "6768" em alguma parte do CNPJ.

A pesquisa deve ser `case insensitive`, ou seja, os termos "paulo", "Paulo", "PAULO" devem retornar os mesmos dados. A forma mais comum para isso é convertendo o texto para "upper" e usando o `LIKE` na instrução SQL, porém acredito que o PostgreSQL oferece suporte ao FULLTEXT SEARCH, pode ser interessante nesse contexto.
