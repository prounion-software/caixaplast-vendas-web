# System Design

## O que é System Desing

System Design, ou design de sistema, é como desenhar o "plano" de uma casa, mas no mundo do software. Quando você quer construir uma casa, você precisa pensar em quantos quartos terá, onde ficará a cozinha, como será o encanamento, entre outros detalhes. No software, é algo parecido: **antes de construir um sistema ou aplicativo, é preciso planejar como tudo vai funcionar.**

No contexto do desenvolvimento de software, system design envolve decidir como diferentes partes de um sistema de software vão interagir entre si. Isso inclui definir a estrutura dos componentes, como os dados serão armazenados e transmitidos, e como as diferentes partes do sistema vão se comunicar.

Alguns pontos principais que são considerados no system design incluem:

- **Arquitetura do Sistema**: Decidir se será uma aplicação web, mobile, desktop, e como essas diferentes partes vão se conectar. Por exemplo, uma aplicação web pode ter um frontend (parte visível para o usuário) e um backend (parte que processa dados e lógica).

- **Escalabilidade**: Planejar para que o sistema possa crescer e lidar com mais usuários ou dados no futuro, sem perder desempenho.

- **Segurança**: Garantir que os dados dos usuários estejam protegidos e que o sistema seja resistente a ataques.

- **Manutenção e Atualizações**: Facilitar futuras manutenções e melhorias no sistema.

- **Performance**: Garantir que o sistema seja rápido e eficiente para os usuários.

Em resumo, system design é o planejamento estratégico de como construir um sistema de software que seja funcional, eficiente, seguro e capaz de crescer com o tempo. É uma parte crucial para garantir que o produto final atenda às necessidades dos usuários e dos desenvolvedores.

## Camadas

O projeto é todo baseado em orientação a objetos e organizado em camadas, as camadas são inspiradas em design como Hexagonal e Clean Architecture, mas sem a rigidez que esses padrões exigem.

É importante destacar, que camadas não são estrutura de diretórios, teremos vários diretórios no projeto que possuem são conceitualmente diversas camadas.

**Exemplo no Modelo MVC**

É comum em aplicacões web MVC, termos uma estruta de diretórios da seguite forma:

```
  📂 src
  |-- 📂 controllers
      |-- 📃 user-controller.ts
      |-- 📃 order-controller.ts
      |-- 📃 product-controller.ts
  |-- 📂 models
      |-- 📃 user-model.ts
      |-- 📃 order-model.ts
      |-- 📃 product-model.ts
  |-- 📂 views
      |-- 📂 users
          |-- 📃 users-list-view.ts
          |-- 📃 user-edit-view.ts
          |-- 📃 user-add-view.ts
      |-- 📂 orders
          |-- 📃 orders-list-view.ts
          |-- 📃 order-edit-view.ts
          |-- 📃 order-add-view.ts
      |-- 📂 products
          |-- 📃 products-list-view.ts
          |-- 📃 product-edit-view.ts
          |-- 📃 product-add-view.ts
      |-- 📂 tests
          |--  📂 controllers
                |-- 📃 user-controller.test.ts
                |-- 📃 order-controller.test.ts
                |-- 📃 product-controller.test.ts
          |-- 📂 models
              |-- 📃 user-model.test.ts
              |-- 📃 order-model.test.ts
              |-- 📃 product-model.test.ts
```

Embora a estrutura acima funcione, ela é indica apenas para projetos pequenos, pois para atuar no domínio de `user`, o programador tem que percorrer vários diretórios diferentes. Há maneiras melhores de se trabalhar em camadas e manter uma estrutura de diretórios mais eficiente.

**Refazendo no Modelo MVC**

```
  📂 src
  |-- 📂 users
      |-- 📃 controller.ts
      |-- 📃 controller.test.ts
      |-- 📃 model.ts
      |-- 📃 model.test.ts
      |-- 📂 views
          |-- list.ts
          |-- edit.ts
          |-- add.ts
  |-- 📂 orders
      |-- 📃 controller.ts
      |-- 📃 controller.test.ts
      |-- 📃 model.ts
      |-- 📃 model.test.ts
      |-- 📂 views
          |-- list.ts
          |-- edit.ts
          |-- add.ts
  |-- 📂 products
      |-- 📃 controller.ts
      |-- 📃 controller.test.ts
      |-- 📃 model.ts
      |-- 📃 model.test.ts
      |-- 📂 views
          |-- list.ts
          |-- edit.ts
          |-- add.ts
```

Dessa forma, tudo que envolve o domínio `user` está dentro de uma só estrutura, inclusive os testes automatizados, mas mesmo assim há uma separação clara das camadas de model, view e controller.

**Importante**, esses exemplos foram usando o modelo MVC apenas para entendimento, usaremos outras arquiteturas nesses projeto, mas é importante entender que estrutura de diretórios não é o mesmo que as camadas da aplicação.

### Principais camadas nesse projeto

- **Main**: É onde o **start** da aplicação acontece, onde as configurações são carregas, classes são instanciadas e a API "fica de pé".

- **Infra**: Contém os recursos necessários para a aplicação funcionar e que não estão diretamente relacionados ao domínio da aplicação, como por exemplo:

- **Shared**: Contém classes e funções que não estão diretamente relacionadas com um modelo, e que podem ser usadas em diversos locais da aplicação como validadores e conversores;

- **Http**: Camada responsável de traduzir a camada **Application** para o ambiente web (http), é onde serão definidas as rotas da API, formatação em JSON e etc. É nesse camada que encontraremos o uso de Express JS ou outra ferramenta que cumpra o mesmo trabalho.

- **Application**: É o core da aplicação, ou seja, tudo que estiver abaixo dessa camada não muda de acordo com o ambiente da aplicação, ou seja, se essa aplicação deixar de ser web e um dia passar a ser uma aplicação CLI (linha de commando), essa camada não deve ser alterada, o que muda é o entorno dela.

- **Entity**: É a camada mais baixa de toda a aplicação, pertence a camada **Application**, e contém as definição das entidades como product, user, table price e etc. Contém as verdades absolutas do domínio como o **preço não pode ser negativo**, ou **a descrição do produto não pode ficar em branco**.

- **Use Case**: Atua diretamente com a camada **Entity**, também faz parte da **Application**, responsável por orquestrar a execução dos processos do domínio validando a entrada de dados, por exemplo, ao criar um novo usuário, verificar se já não existe um usuário com o mesmo nome ou e-mail, se houver retorar um erro a quem tentou realizar a ação, caso contrário (se tudo estiver certo), invocar a camada **Repository** para persistir os dados.

- **Repository**: Responsável por recuperar e manipular os dados no banco de dados, também pertence ao **Application**

#### Exemplos

Os exemplos abaixo são para ilustrar, porém estão bem próximos do que a aplicação se tornará no final.

**Entity**

```ts
// src/app/product/product.ts

export class Product {
  constructor(public id: string, public name: string, public price: number) {
    if (price <= 0) {
      throw new Error("Price must be greater than zero.");
    }
  }
}
```

**Use Case**

```ts
// src/app/product/use-cases/create.ts
import { Product } from "../product";
import { ProductRepository } from "../repository/repository";

interface CreateProductRequest {
  id: string;
  name: string;
  price: number;
}

export class CreateProduct {
  constructor(private productRepository: ProductRepository) {}

  execute(request: CreateProductRequest): Product {
    const { id, name, price } = request;

    // Criar uma nova instância de Product
    const product = new Product(id, name, price);

    // Salvar o produto no repositório
    this.productRepository.save(product);

    return product;
  }
}
```

**Repository**

Note que estabelecemos apenas um contrato (interface), de forma que possamos substituir a implementação facilitando muito os testes automatizados.

```ts
// src/app/product/repository/repository.ts
import { Product } from "./product";

export interface ProductRepository {
  save(product: Product): void;
  findById(id: string): Product | null;
}
```

Um exemplo, usando o conceito de InMemoryDatabase:

```ts
// src/app/product/repository/in-memory.ts
import { Product } from "../../product";
import { ProductRepository } from "./repository.ts";

export class InMemoryProductRepository implements ProductRepository {
  private products: Product[] = [];

  save(product: Product): void {
    this.products.push(product);
  }

  findById(id: string): Product | null {
    return this.products.find((product) => product.id === id) || null;
  }
}
```

**Http**

```ts
// src/app/product/http/controller.ts
import { CreateProduct } from "../use-cases/create-product";
import { ProductRepository } from "../repositories/repository.ts";

export class ProductController {
  constructor(private createProductUseCase: CreateProduct) {}

  createProduct(req: { id: string; name: string; price: number }) {
    try {
      const product = this.createProductUseCase.execute(req);
      console.log("Product created:", product);
    } catch (error) {
      console.error("Error creating product:", error.message);
    }
  }
}
```

```ts
// src/app/product/http/routes.ts
import { Request, Response, Router } from "express";

export function productRoutes(
  router: Router,
  repository: ProductRepository
): void {
  const createUseCase = new CreateProduct(repository);
  const controller = new ProductController(createUseCase);

  router.post("/product", async (req: Request, res: Response) => {
    await controller.createProduct(req.body);
  });
}
```

E assim, finalmente na **main** do projeto teremos a invocação desse e de outros módulos.

**Main**

```ts
// src/main.ts

import express, { Express } from "express";
import { productRoutes } from "./app/product/http/routes.ts";

const api = express();
const router = Router();
api.use("/api", router);

const productRepository = new InMemoryProductRepository();

productRoutes(router, productRepository);

api.listen(8080, () => logger.info(`Running on port 8080`));
```

A estrutura de diretórios desse exemplo, fica da seguinte forma:

```
📂 src
  |-- 📂 app
      |-- 📂 product
          |-- 📂 repository
              |-- repository.ts
              |-- in-memory.ts
          |-- 📂 use-cases
              |-- create.ts
          |-- 📂 http
              |-- controller.ts
              |-- routes.ts
          |-- product.ts
  |-- main.ts
```
