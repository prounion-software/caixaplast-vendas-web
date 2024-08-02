# Padrões adotados no projeto

## Linguagem de domínio

Um dos temas mais controversos no desenvolvimento de software é se devemos adotar inglês ou a língua falada pelos utilizadores da ferramenta, no nosso caso português (pt-BR).

O problema em usar português no projeto é lutar contra a mistura de termos que acabam tornando o código menos legivel. Por exemplo, `CriarUsuarioUseCase`, é bem mais estranho do que `CreateUserUseCase`, ou `PedidoController` em relação a `OrderController`.

Mas usar termos em inglês também gera um outro problema, pois os utilizadores da ferramenta não usam inglês para se comunicar no dia-a-dia. Para tentar contornar isso, e o usuário falar em preço (`price`) e nós não entendermos errado como custo (`cost`), iremos criar um [dicionário](DICIONARIO_TERMOS.md) de termos.

Esse [dicionário](DICIONARIO_TERMOS.md) também será usado para a representação dos elementos na interface de usuário.

## Campos comuns na base de dados e Soft Delete

Todas as tabelas nesse sistema terão as seguintes colunas:

| Campo      | Tipo     | Descrição                       | Obrigatório |
| ---------- | -------- | ------------------------------- | ----------- |
| created_at | DateTime | Data de criação do registro     | Sim         |
| updated_at | DateTime | Data de atualização do registro | Sim         |
| deleted_at | DateTime | Data de remoção do registro     | Não         |

Na descrição das tabelas nessa documentação, esses campos serão omitidos para evitar repetição, mas devem sempre estar presentes em todas as tabelas de cada entidade.

**Soft Delete**

Soft Delete é uma técnica usada para marcar um registro como removido, mas não apagá-lo realmente da base de dados.

Dessa forma, quando um registro é "deletado" ele apenas marca o campo `deleted_at` com a data e hora atual do sistema, e a partir desse ponto o sistema não considera mais esse item.

Mas esse item pode sim ser listado pela aplicação em algumas situações:

- Acessado diretamente pelo id: `GET /product/1243`, mesmo se o produto de id 1243 estiver marcado como removido, os dados dele devem ser retornados. Uma vez que ele possa ser reativado se necessário.
- Em rotas de listagem com o parâmetro `withDeleted` igual a **true** ou **1**: `GET /products?withDeleted=1` ou `GET /products?withDeleted=true`

## API Rest

A definição de API Rest está amplamente difundido na internet e nas comunidades de software.

As diretrizes abaixos são para definição dos padrões adotados neste projeto.

### Nomenclatura das rotas

#### Rotas para listagens

Utilizar plural e métodos **GET**:

- `GET /products`
- `GET /customers`
- `GET /orders`

Todas as rotas de listagens devem retornar array, mesmo que vazios, não devem retornar 404, a mesmo que alguma entidade do seguimento da rota não exista:

- Listagem de produtos de um pedido existente e com produtos, `GET /orders/3346/items`, esperado um array dos itens do pedido
- Listagem de produtos de um pedido existente, mas sem produtos, `GET /orders/3566/items`, esperado um array vazio
- Listagem de produtos de um pedido **não** existente, `GET /orders/4409/items`, deve retornar 404 pois o pedido informado não existe.

**Paginação**

Para rotas de listagens de primeiro nível, todos os resultados devem ser paginados, caso não seja informados os valores padrão serão:

- Página (page): 1
- Resultados por página (perPage): 50

Os dados de paginação devem ser via **query string**, `GET /products?page=2&perPage=30`.

Mas para listagens que não são de primeiro nível, como `GET /order/234/items` não é necessário paginação, pois estamos listando todos os itens do pedido 234.

### Rotas de entidade

São basicamente todas as rotas de cadastro além da listagem, devem ser no singular, e com `id` número no seguimento que aponta para o id do cadastro da entidade:

- `GET /product/{id:([0-9]+)}`: `GET /product/2345`
- `GET /order/{id:([0-9]+)}`: `GET /order/34522`
- `GET /sale-agent/{id:([0-9]+)}`: `GET /sale-agent/445`

Caso o registro em questão não exista, deve ser retornado status 404.

**Outros métodos**

- Inclusão de novos registros: **POST** (deve retornar status 201 em caso de sucesso)
- Atualizar registros existentes, toda atualização pode ser parcial: **PATCH**
- Remover registros (soft delete): **DELETE**
