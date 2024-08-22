# USUÁRIOS

⚠️ ⚠️ ⚠️ ⚠️

Esse processo será revisado no futuro para adicionar melhoras formas de autenticação como recuperação de senha e etc. Por hora, vamos no concentrar no funcionamento essencial.

⚠️ ⚠️ ⚠️ ⚠️

---

Teremos basicamente 2 papéis de usuários no sistema:

- Administradores
- Representantes

Perceba que não são "tipos", mas papéis, pois um administrador pode agir como um representante.

## Administradores

Administradores tem acesso as áreas de cadastro, como produtos, moedas, usuários... ou seja, são os usuários que gerenciam a aplicação.

Também pode realizar pedidos para os vendedores aos quais estão definidos como representantes.

## Representantes

São os usuários comuns do sistema, mas que estão associados como representantes de ao menos um vendedor.

Para entender melhor essa relação, imagine a tela de inclusão de pedidos. Quando o usuário for criar um novo pedido, ele deve selecionar qual é o vendedor relacionado ao pedido. Um representante pode ter vários vendedores disponíveis para escolher.

## Estrutura de dados

### Tabela de usuários: **users**

Nenhum desses campos deve permitir nulo.

| Dado          | Tipo        | Campo na Tabela | Observação                        |
| ------------- | ----------- | --------------- | --------------------------------- |
| ID do usuário | Numero      | user_id         | Chave primária                    |
| ID externo    | Texto (10)  | external_id     | Deve ser único na tabela          |
| Nome          | Texto (100) | name            |                                   |
| Email         | Texto (100) | email           | Deve ser único na tabela          |
| Administrador | Numero      | admin           | 0 = false, 1 = true (default = 0) |
| Senha         | Texto (100) | password        |                                   |

### Tabela associativa de representantes e vendedores: **agents_sellers**

Nenhum desses campos deve permitir nulo.

| Dado           | Tipo   | Campo na Tabela | Observação              |
| -------------- | ------ | --------------- | ----------------------- |
| ID do usuário  | Número | user_id         | FK tabela de usuários   |
| ID do vendedor | Número | seller_id       | FK tabela de vendedores |

### Estrutura de dados na aplicação

Essa estrutura de dados abaixo deve ser usada internamente na aplicação, perceba que não há propriedade de senha, pois ela é usada apenas na criação e na autenticação. Não deve ser "trafegada" pela aplicação.

A propriedade `sellers`, se refere aos [vendedores](./VENDEDORES.md).

```ts
export type User = {
  id: number;
  external_id: string;
  name: string;
  email: string;
  administrador: boolean;
  sellers: Seller[];
};
```

### Usuário

`POST /users`: Criação de um novo usuário

```ts
export type UserPostPayload = {
  external_id: string;
  name: string;
  email: string;
  password: string;
};
```

`PATCH /user/:id([0-9]+)`

```ts
export type UserPatchPayload = {
  external_id: string;
  name: string;
  email: string;
};
```

#### Mudança de senha

`PATCH /user/:id([0-9]+)/password`

Nesse contexto é importante que o usuário só possa alterar a própria senha, e não de outros usuários.

```ts
export type PasswordUpdatePayload = {
  oldPassword: string;
  newPassword: string;
};
```

#### Promovendo para administrador ou rebaixando

Apenas administradores podem realizar essa ação.

Para promover: `POST /user/:id([0-9]+)/admin`
Para rebaixar: `DELETE /user/:id([0-9]+)/admin`

#### Associoando um representante com um vendedor

A associação entre representante e vendedor é bastante simples também.
É claro que é necessário verificar se tanto usuário quanto o vendedor existem.

Para associar: `POST /user/:userId([0-9]+)/seller/:sellerId([0-9]+)`
Se esse representante já estiver associado com o vendedor, nenhuma ação será necessária, basta retornar 204 (no content).
Se a associação for criada, deve retornar 201 (created).

Para desassociar: `DELETE /user/:userId([0-9]+)/seller/:sellerId([0-9]+)`
Se esse representante não estiver associado com o vendedor, nenhuma ação será necessária.
O retorno dessa rota deve ser 204 (no content).

Apenas administradores devem ter acesso nessas ações.

---

## Autenticação

Neste momento, iremos criar um processo de autenticação por JWT simples, sem refresh-token.

### Login

`POST /auth/login`

```ts
export type LoginPayload = {
  email: string;
  password: string;
};
```

Devemos retornar o token JWT que será usado para validar a entrada de todas as rotas, com exceção é claro de `/auth/login`

### Projetos com exemplos de uso

Já usamos os processos de login como descrito acima em vários projetos, [aqui](https://github.com/prounion-software/app-esperanca-api/blob/main/src/api/modules/autenticacao/controller.ts) temos o exemplo de um controller que faz esse processo de autenticação. E [aqui](https://github.com/prounion-software/app-esperanca-api/blob/main/src/main/middleware/auth.ts) temos o middleware que realiza essa verificação.

Note o middleware adiciona a propriedade `userId` no Request do Express, é dessa forma que obtemos o usuário autenticado na aplicação.
Para que o typescript identifique essa propriedade, é necessário criar um arquivo de definição como [este](https://github.com/prounion-software/app-esperanca-api/blob/main/src/types.d.ts)
