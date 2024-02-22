# PRODUTOS

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
