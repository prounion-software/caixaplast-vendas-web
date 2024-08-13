# PRODUTOS

A origem do cadastro dos produtos é sempre do ERP, assim como os cadastros auxiliares como grupos e subgrupos, nessa aplicação não há como cadastrar nada disso diretamente.

Por tanto, não há necessidade de criar áreas de cadastro ou pesquisa na interface de usuário. Pois os dados de produtos e relacionados serão utilizados apenas na inclusão de pedidos e orçamentos. _(Por enquanto)_

## Estrutura de dados

### Tabela de produtos: **products**

| Dado                           |    Tipo     | Campo na Tabela        | Permite Nulo |
| ------------------------------ | :---------: | ---------------------- | :----------: |
| ID interno na aplicação        |   Número    | product_id             |     Não      |
| ID no ERP                      | Texto (10)  | external_id            |     Não      |
| Descrição do produto           | Texto (100) | description            |     Não      |
| Descrição completa             | Texto (500) | full_description       |     Sim      |
| Descrição reduzida             | Texto (500) | short_description      |     Sim      |
| Código de barras               | Texto (20)  | bar_code               |     Sim      |
| Unidade de controle            | Texto (10)  | stock_unit             |     Não      |
| Unidade de venda               | Texto (10)  | seles_unit             |     Não      |
| Altura em milímetros           |   Número    | height                 |     Sim      |
| Largura em milímetros          |   Número    | width                  |     Sim      |
| Comprimento em milímetros      |   Número    | length                 |     Sim      |
| Peso em gramas                 |   Número    | weight                 |     Sim      |
| Quantidade de peças por volume |   Número    | quantity_pieces_volume |     Sim      |
| ID do tipo de material         |   Número    | material_type_id       |     Sim      |
| ID do grupo                    |   Número    | group_id               |     Sim      |
| ID do subgrupo                 |   Número    | subgroup_id            |     Sim      |

### Tabela de imagens dos produtos: **product_images**

| Dado          |    Tipo     | Campo na Tabela | Permite Nulo |
| ------------- | :---------: | --------------- | :----------: |
| ID da imagem  |   Número    | image_id        |     Não      |
| ID no ERP     | Texto (10)  | external_id     |     Sim      |
| ID do produto |   Número    | product_id      |     Não      |
| URL           | Texto (500) | url             |     Não      |
| Sequência     |   Número    | sequence        |     Não      |

### Tabela de tipos de materiais: **product_material_types**

| Dado                   |    Tipo     | Campo na Tabela  | Permite Nulo |
| ---------------------- | :---------: | ---------------- | :----------: |
| ID do tipo de material |   Número    | material_type_id |     Não      |
| ID no ERP              | Texto (10)  | external_id      |     Não      |
| Descrição              | Texto (100) | description      |     Nao      |

### Tabela de grupos de produtos: **product_groups**

| Dado        |    Tipo     | Campo na Tabela | Permite Nulo |
| ----------- | :---------: | --------------- | :----------: |
| ID do grupo |   Número    | group_id        |     Não      |
| ID no ERP   | Texto (10)  | external_id     |     Não      |
| Descrição   | Texto (100) | description     |     Não      |

### Tabela de subgrupos de produtos: **product_subgroups**

| Dado           |    Tipo     | Campo na Tabela | Permite Nulo |
| -------------- | :---------: | --------------- | :----------: |
| ID do subgrupo |   Número    | subgroup_id     |     Não      |
| ID do grupo    |   Número    | group_id        |     Não      |
| ID no ERP      | Texto (10)  | external_id     |     Não      |
| Descrição      | Texto (100) | description     |     Não      |

### Tabela de códigos de barras dos produtos: **product_bar_codes**

| Dado                |    Tipo    | Campo na Tabela | Permite Nulo |
| ------------------- | :--------: | --------------- | :----------: |
| ID do produto       |   Número   | product_id      |     Não      |
| Código de barras    | Texto (20) | bar_code        |     Não      |
| Unidade             | Texto (10) | unit            |     Não      |
| Quantidade unitária |   Número   | quantity        |     Não      |

### Tabela de unidades de produtos: **product_units**

| Dado                |    Tipo    | Campo na Tabela    | Permite Nulo |
| ------------------- | :--------: | ------------------ | :----------: |
| ID da unidade       | Texto (10) | unit_id            |     Não      |
| Descrição           | Texto (50) | description        |     Não      |
| Descrição no plural | Texto (50) | plural_description |     Não      |

O cadastro do produto consiste na seguinte estrutura:

#### Produto

A estrutura abaixo é a representação geral de como essa entidade será trabalhada internamente na aplicação e como serão os retornos delas nas rotas de pesquisa. No entanto, veremos que para cadastro e atualizações os payloads serão diferentes.

```ts
export type Product = {
  id: number; // código interno do produto na aplicação
  externalId: string; // código do produto no ERP
  description: string; // descrição do produto
  fullDescription?: string; // descrição completa do produto
  shortDescription?: string; // descrição reduzida do produto
  barCodePrincipal: string; // código de barras principal
  units: {
    stock: string; // unidade de controle interno
    sales: string; // unidade de venda
  };
  dimensions?: ProductDimension;
  materialType?: ProductMaterialType;
  group?: ProductGroup;
  subGroup?: ProductSubGroup;
  images: ProductImage[];
  barCodes: ProductBarCode[];
};
```

##### Payload de cadastro

Um produto pode ser cadastrado com todos seus relacionamentos preenchidos ou não, podendo ter o cadastro completado posteriormente com outras rotas.

Além disso, o relacionamento com outras entidades, no cadastro e atualização se darão pelo id dessas entidades.

**PRODUTOS**

`POST /products`

```ts
export type ProductPostPayload = {
  externalId: string; // código do produto no ERP
  description: string; // descrição do produto
  fullDescription?: string; // descrição completa do produto
  shortDescription?: string; // descrição reduzida do produto
  barCodePrincipal: string; // código de barras principal
  units: {
    stock: string; // unidade de controle interno de estoque
    sales: string; // unidade de venda
  };
  dimensions?: ProductDimension;
  materialTypeId?: number;
  groupId?: number;
  subGroupId?: number;
  images?: ProductImagePostPayload[];
  barCodes?: ProductBarCodePostPayload[];
};
```

```json
// exemplo de payload completo
{
  "externalId": "12345",
  "description": "Fanta Laranja 2L",
  "fullDescription": "Refrigerante Fanta Sabor Laranja PET 2 Litros",
  "shortDescription": "Refri. Fanta Laranja 2L",
  "barCodePrincipal": "7892348967503",
  "units": {
    "stock": "FD", // fardo
    "sales": "GRF" //  garrafa
  },
  "dimensions": {
    "height": 29,
    "width": 15,
    "length": 15,
    "weight": 2000,
    "quantityPiecesVolume": 1
  },
  "materialTypeId": 1,
  "groupId": 1,
  "subGroupId": 2,
  "images": [
    {
      "externalId": "12345-1",
      "url": "https://exemple.com/img-1.png",
      "sequence": 1
    },
    {
      "externalId": "12345-2",
      "url": "https://exemple.com/img-2.png",
      "sequence": 2
    }
  ],
  "barCodes": [
    {
      "barCode": "789998778998",
      "unit": "GRF",
      "quantity": 1
    },
    {
      "barCode": "789998778888",
      "unit": "FD",
      "quantity": 6
    }
  ]
}

// exemplo de payload incompleto

{
  "externalId": "12345",
  "description": "Fanta Laranja 2L",
  "barCodePrincipal": "7892348967503",
  "units": {
    "stock": "FD", // fardo
    "sales": "GRF" //  garrafa
  }
}
```

`PATCH /product/{:id([0-9]+)}`

O processo de patch é praticamente a mesma coisa que o post, porém todos os campos são opcionais no payload, o que não foi informado não será atualizado.

```ts
export type ProductPatchPayload = Partial<ProductPostPayload>;
```

```ts
export type ProductDimension = {
  height: number; // altura do produto
  width: number; // largura do produto
  length: number; // comprimento do produto
  weight: number; // peso do produto
  quantityPiecesVolume: number; // quantidade de peças por volume
};
```

---

**TIPOS DE MATERIAL**

Estrutura interna da aplicação.

```ts
export type ProductMaterialType = {
  id: number; // id do tipo de material na aplicação
  externalId: string; // id do tipo de material no ERP
  description: string; // descrição do tipo do material
};
```

#### Payload de cadastro

`POST products/material-types`

```ts
export type ProductMaterialTypePostPayload = {
  externalId: string; // id do tipo de material no ERP
  description: string; // descrição do tipo do material
};
```

```json
// exemplo
{
  "externalId": "12",
  "description": "Chapa de Aço de 1mm"
}
```

`PATCH products/material-type/{:id([0-9]+)}`

O processo de patch é praticamente a mesma coisa que o post, porém todos os campos são opcionais no payload, o que não foi informado não será atualizado.

```ts
export type ProductMaterialTypePatchPayload =
  Partial<ProductMaterialTypePostPayload>;
```

---

**GRUPO DE PRODUTOS**

Estrutura interna da aplicação.

```ts
export type ProductGroup = {
  id: number; // id do grupo na aplicação
  externalId: string; // id do grupo no ERP
  description: string; // descrição do grupo
};
```

#### Payload de cadastro

`POST /products/groups`

```ts
export type ProductGroupPostPayload = {
  externalId: string; // id do grupo no ERP
  description: string; // descrição do grupo
};
```

```json
// exemplo
{
  "externalId": "g47",
  "description": "PRODUTOS DE LIMPEZA"
}
```

`PATCH products/group/{:id([0-9]+)}`

O processo de patch é praticamente a mesma coisa que o post, porém todos os campos são opcionais no payload, o que não foi informado não será atualizado.

```ts
export type ProductGroupPatchPayload = Partial<ProductGroupPostPayload>;
```

---

**SUB GRUPOS DE PRODUTOS**

Estrutura interna da aplicação.

```ts
export type ProductSubGroup = {
  id: number; // id do sub-grupo na aplicação
  externalId: string; // id do sub-grupo no ERP
  description: string; // descrição do grupo
};
```

#### Payload de cadastro

Todo subgrupo pertence a um grupo, atente-se para a segmentação da URL.

`POST /products/group/{:groupId([0-9]+)}/sub-groups`

```ts
export type ProductSubGroupPostPayload = {
  externalId: string; // id do sub-grupo no ERP
  description: string; // descrição do grupo
};
```

```json
// exemplo
{
  "externalId": "sg99",
  "description": "DETERGENTE"
}
```

`PATCH /products/group/{:groupId([0-9]+)}/sub-group/{:id([0-9]+)}`

O processo de patch é praticamente a mesma coisa que o post, porém todos os campos são opcionais no payload, o que não foi informado não será atualizado.

```ts
export type ProductSubGroupPatchPayload = Partial<ProductSubGroupPostPayload>;
```

---

**UNIDADES DE PRODUTOS**

Os IDs das unidades de produtos, não são geradas automaticamente pela aplicação como os demais cadastros e não tem um id externo **(external id)**, esse id será fornecido no cadastro.

As unidades serão referenciadas nas [tabelas de preços](./TABELA_PRECOS.md), por esse ID. Como **UN**, **PCT**, **GRF**...

Estrutura interna na aplicação:

```ts
export type ProductUnit = {
  id: string; // id da unidade (UN, PCT, GRF, BB...)
  description: string; // descrição (unidade, pacote, garrafa, bombona...)
  pluralDescription: string; // plural (unidades, pacotes, garrafas, bombonas...)
};
```

#### Payload de cadastro

`POST /products/units`

```ts
export type ProductUnitPostPayload = {
  id: string;
  description: string;
  pluralDescription: string;
};
```

```json
// Exemplo
{
  "id": "PC",
  "description": "Peça",
  "pluralDescription": "peças"
}
```

`PATCH /products/units/{:id}`

```ts
export type ProductUnitPatchPayload = {
  description?: string;
  pluralDescription?: string;
};
```

```json
// Exemplo: PATCH /products/units/PCT
{
  "description": "Pacote",
  "pluralDescription": "Pacotes"
}
```

##### Validações importantes

- O id deve ser sempre em maiúsculo, caso o usuário mande como "un", devemos armazenar como "UN".

- O id da unidade é único, não podem haver 2 ou mais cadastrados com o mesmo ID, se alguém tentar cadastrar um registro com um ID já cadastrado, devemos retornar status **409 (conflict)**;

---

**IMAGEM DOS PRODUTOS**

Por enquanto não temos a inteção de armazenar as imagens internamente na aplicação, por isso recebemos uma URL pública para essa imagem.

Estrutura interna na aplicação:

```ts
export type ProductImage = {
  id: number; // id da imagem na aplicação
  externalId: string; // id da imagem no ERP
  url: string; // segmento de URL da imagem
  sequence: number; // sequência da imagem na galeria
};
```

#### Payload de cadastro

Toda imagem pertence a um produto, atente-se para a segmentação da URL.

`POST /product/{:productId([0-9]+)}/images`

```ts
export type ProductImagePostPayload = {
  externalId: string;
  url: string;
  sequence: number;
};
```

```json
// exemplo
{
  "externalId": "img-1",
  "url": "https://images.exemplo.com/img.png",
  "sequence": 1
}
```

`PATCH /product/{:productId([0-9]+)}/image/{:id([0-9]+)}`

O processo de patch é praticamente a mesma coisa que o post, porém todos os campos são opcionais no payload, o que não foi informado não será atualizado.

```ts
export type ProductImagePatchPayload = Partial<ProductImagePostPayload>;
```

---

**CÓDIGOS DE BARRAS**

Estrutura interna na aplicação

```ts
export type ProductBarCode = {
  id: number; // id na aplicação
  barCode: string; // código de barras
  unit: string; // Tipo de unidade PÇ (peça), PCT (pacote), BB (bombona), LT (lata) ...
  quantity: number; // quantidade unitário do produto nesta embalagem
};
```

#### Payload de cadastro

Todo código de barras pertence a um produto, atente-se para a segmentação da URL.

`POST /product/{:productId([0-9]+)}/bar-codes`

```ts
export type ProductBarCodePostPayload = {
  barCode: string; // código de barras
  unit: string; // Tipo de unidade PÇ (peça), PCT (pacote), BB (bombona)...
  quantity: number; // quantidade unitário do produto nesta embalagem
};
```

`PATCH /product/{:productId([0-9]+)}/bar-code/{:id([0-9]+)}`

O processo de patch é praticamente a mesma coisa que o post, porém todos os campos são opcionais no payload, o que não foi informado não será atualizado.

```ts
export type ProductBarCodePatchPayload = Partial<ProductBarCodePostPayload>;
```
