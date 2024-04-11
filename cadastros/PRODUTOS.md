# PRODUTOS

A origem do cadastro dos produtos é sempre do ERP, assim como os cadastros auxiliares como grupos e subgrupos, nessa aplicação não há como cadastrar nada disso diretamente.

Por tanto, não há necessidade de criar áreas de cadastro ou pesquisa na interface de usuário. Pois os dados de produtos e relacionados serão utilizados apenas na inclusão de pedidos e orçamentos. _(Por enquanto)_

## Estrutura de dados

### Tabela de produtos: **produtos**

| Dado                           |    Tipo     | Campo na Tabela    | Permite Nulo |
| ------------------------------ | :---------: | ------------------ | :----------: |
| ID interno na aplicação        |   Número    | produto_id         |     Não      |
| ID no ERP                      | Texto (10)  | id_externo         |     Não      |
| Descrição do produto           | Texto (100) | descricao          |     Não      |
| Descrição completa             | Texto (500) | descricao_completa |     Sim      |
| Descrição reduzida             | Texto (500) | descricao_reduzida |     Sim      |
| Código de barras               | Texto (20)  | codigo_barras      |     Sim      |
| Unidade de controle            | Texto (10)  | unidade_controle   |     Não      |
| Unidade de venda               | Texto (10)  | unidade_venda      |     Não      |
| Altura em milímetros           |   Número    | altura             |     Sim      |
| Largura em milímetros          |   Número    | largura            |     Sim      |
| Comprimento em milímetros      |   Número    | comprimento        |     Sim      |
| Quantidade de peças por volume |   Número    | qtd_pecas_volume   |     Sim      |
| ID do tipo de material         |   Número    | tipo_material_id   |     Sim      |
| ID do grupo                    |   Número    | grupo_id           |     Sim      |
| ID do subgrupo                 |   Número    | subgrupo_id        |     Sim      |

### Tabela de imagens dos produtos: **produtos_imagens**

| Dado          |    Tipo     | Campo na Tabela | Permite Nulo |
| ------------- | :---------: | --------------- | :----------: |
| ID da imagem  |   Número    | imagem_id       |     Não      |
| ID no ERP     | Texto (10)  | id_externo      |     Sim      |
| ID do produto |   Número    | produto_id      |     Não      |
| URL           | Texto (500) | url             |     Não      |
| Sequência     |   Número    | sequencia       |     Não      |

### Tabela de tipos de materiais: **tipos_materiais**

| Dado                   |    Tipo     | Campo na Tabela  | Permite Nulo |
| ---------------------- | :---------: | ---------------- | :----------: |
| ID do tipo de material |   Número    | tipo_material_id |     Não      |
| ID no ERP              | Texto (10)  | id_externo       |     Não      |
| Descrição              | Texto (100) | descricao        |     Nao      |

### Tabela de grupos de produtos: **grupos_produtos**

| Dado        |    Tipo     | Campo na Tabela | Permite Nulo |
| ----------- | :---------: | --------------- | :----------: |
| ID do grupo |   Número    | grupo_id        |     Não      |
| ID no ERP   | Texto (10)  | id_externo      |     Não      |
| Descrição   | Texto (100) | descricao       |     Não      |

### Tabela de subgrupos de produtos: **subgrupos_produtos**

| Dado           |    Tipo     | Campo na Tabela | Permite Nulo |
| -------------- | :---------: | --------------- | :----------: |
| ID do subgrupo |   Número    | subgrupo_id     |     Não      |
| ID do grupo    |   Número    | grupo_id        |     Não      |
| ID no ERP      | Texto (10)  | id_externo      |     Não      |
| Descrição      | Texto (100) | descricao       |     Não      |

### Tabela de códigos de barras dos produtos: **produtos_codigos_barras**

| Dado                |    Tipo    | Campo na Tabela | Permite Nulo |
| ------------------- | :--------: | --------------- | :----------: |
| ID do produto       |   Número   | produto_id      |     Não      |
| Código de barras    | Texto (20) | codigo_barras   |     Não      |
| Unidade             | Texto (10) | unidade         |     Não      |
| Quantidade unitária |   Número   | quantidade      |     Não      |

O cadastro do produto consiste na seguinte estrutura:

```ts
export type Produto = {
  id: number; // código interno do produto na aplicação
  idExterno: string; // código do produto no ERP
  descricao: string; // descrição do produto
  descricaoCompleta?: string; // descrição completa do produto
  descricaoReduzida?: string; // descrição reduzida do produto
  codigoBarrasPrincipal: string; // código de barras principal
  unidades: {
    controleInterno: string; // unidade de controle interno
    venda: string; // unidade de venda
  };
  dimensoes?: ProdutoDimensao;
  tipoMaterial?: ProdutoTipoMaterial;
  grupo?: ProdutoGrupo;
  subGrupo?: ProdutoSubGrupo;
  imagens: ProdutoImagem[];
  codigosBarra: ProdutoCodigoBarra[];
};

export type ProdutoDimensao = {
  altura: number; // altura do produto
  largura: number; // largura do produto
  comprimento: number; // comprimento do produto
  peso: number; // peso do produto
  qtdPecasVolume: number; // quantidade de peças por volume
};

export type ProdutoTipoMaterial = {
  id: number; // id do tipo de material na aplicação
  idExterno: string; // id do tipo de material no ERP
  descricao: string; // descrição do tipo do material
};

export type ProdutoGrupo = {
  id: number; // id do grupo na aplicação
  idExterno: string; // id do grupo no ERP
  descricao: string; // descrição do grupo
};

export type ProdutoSubGrupo = {
  id: number; // id do sub-grupo na aplicação
  idExterno: string; // id do sub-grupo no ERP
  grupoId: number; // id interno do grupo na aplicação
  descricao: string; // descrição do grupo
};

export type ProdutoImagem = {
  id: number; // id da imagem na aplicação
  idExterno: string; // id da imagem no ERP
  url: string; // segmento de URL da imagem
  sequencia: number; // sequência da imagem na galeria
};

export type ProdutoCodigoBarra = {
  id: number; // id na aplicação
  codigoBarras: string; // código de barras
  unidade: string;
  qtdUnitaria: number; // quantidade unitário do produto nesta embalagem
};
```
