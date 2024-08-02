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
| ID no ERP              | Texto (10)  | id_externo       |     Não      |
| Descrição              | Texto (100) | description      |     Nao      |

### Tabela de grupos de produtos: **product_groups**

| Dado        |    Tipo     | Campo na Tabela | Permite Nulo |
| ----------- | :---------: | --------------- | :----------: |
| ID do grupo |   Número    | group_id        |     Não      |
| ID no ERP   | Texto (10)  | external_id     |     Não      |
| Descrição   | Texto (100) | description     |     Não      |

### Tabela de subgrupos de produtos: **products_subgroups**

| Dado           |    Tipo     | Campo na Tabela | Permite Nulo |
| -------------- | :---------: | --------------- | :----------: |
| ID do subgrupo |   Número    | subgroup_id     |     Não      |
| ID do grupo    |   Número    | group_id        |     Não      |
| ID no ERP      | Texto (10)  | external_id     |     Não      |
| Descrição      | Texto (100) | description     |     Não      |

### Tabela de códigos de barras dos produtos: **products_bar_codes**

| Dado                |    Tipo    | Campo na Tabela | Permite Nulo |
| ------------------- | :--------: | --------------- | :----------: |
| ID do produto       |   Número   | product_id      |     Não      |
| Código de barras    | Texto (20) | bar_code        |     Não      |
| Unidade             | Texto (10) | unit            |     Não      |
| Quantidade unitária |   Número   | quantity        |     Não      |

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

```ts
export type ProductBarCodePatchPayload = Partial<ProductBarCodePostPayload>;
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

```ts
export type ProductMaterialType = {
  id: number; // id do tipo de material na aplicação
  externalId: string; // id do tipo de material no ERP
  description: string; // descrição do tipo do material
};
```

```ts
export type ProductGroup = {
  id: number; // id do grupo na aplicação
  externalId: string; // id do grupo no ERP
  description: string; // descrição do grupo
};
```

```ts
export type ProductSubGroup = {
  id: number; // id do sub-grupo na aplicação
  externalId: string; // id do sub-grupo no ERP
  groupId: number; // id interno do grupo na aplicação
  description: string; // descrição do grupo
};
```

```ts
export type ProductImagePostPayload = {
  externalId: string;
  url: string;
  sequence: number;
};
```

```ts
export type ProductImage = {
  id: number; // id da imagem na aplicação
  externalId: string; // id da imagem no ERP
  url: string; // segmento de URL da imagem
  sequence: number; // sequência da imagem na galeria
};
```

```ts
export type ProductBarCodePostPayload = {
  barCode: string; // código de barras
  unit: string; // Tipo de unidade PÇ (peça), PCT (pacote), BB (bombona), LT (lata) ...
  quantity: number; // quantidade unitário do produto nesta embalagem
};
```

```ts
export type ProductBarCode = {
  id: number; // id na aplicação
  barCode: string; // código de barras
  unit: string; // Tipo de unidade PÇ (peça), PCT (pacote), BB (bombona), LT (lata) ...
  quantity: number; // quantidade unitário do produto nesta embalagem
};
```
