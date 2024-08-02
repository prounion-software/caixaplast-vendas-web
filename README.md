# Sistema de Vendas on-line da CaixaPlast

![banner](./banner.jpg)

Desenvolvimento de um sistema web para simplificar o processo de pedidos e orçamentos para os representantes e vendedores da [CaixaPlast](https://caixaplast.com.br/).

## Contexto do Projeto

Antes da implementação deste sistema, os pedidos feitos pelos representantes da **CaixaPlast** seguiam um processo manual:

- O representante visitava o cliente.
- Anotava os produtos e suas quantidades.
- Entrava em contato com a equipe de vendas da **CaixaPlast** por telefone, e-mail ou mensagem.
- A equipe de vendas registrava o pedido no **[Sistema Proc](https://sistemaproc.com.br/)**.
- O pedido passava por um fluxo de aprovação, preparação, fabricação, faturamento, etc.

### Objetivos da solução

O principal objetivo é oferecer aos representantes e vendedores da **CaixaPlast** uma plataforma web intuitiva para realizar pedidos e solicitar orçamentos de forma eficiente.

Além disso, a solução irá fornecer uma API REST segura e bem documentada para integrar-se ao sistema ERP, facilitando a manutenção dos cadastros de clientes, produtos, tabelas de preços, pedidos e orçamentos.

Outras funcionalidades importantes incluem:

- Acompanhamento em tempo real dos pedidos e orçamentos (tracking).
- Possibilidade de pré-cadastro de clientes, com análise posterior pelo ERP para completar o cadastro.

Este sistema irá simplificar e otimizar significativamente o processo de vendas da **CaixaPlast**, oferecendo uma experiência moderna e ágil para seus representantes e vendedores.

## Arquitetura

Este projeto segue os padrões mais comuns de desenvolvimento de aplicações web no modelo SaaS.

Para uma visão geral sobre a arquitetura, veja o [System Design](arquitetura/SYSTEM_DESIGN.md) e os [padrões adotados](arquitetura/PADROES.md) do projeto.

## Os pontos chave do projeto são:

### Cadastros

- [Produtos](cadastros/PRODUTOS.md)
- Representantes
- [Tabelas de preços](cadastros/TABELA_PRECOS.md)
- Carteiras de clientes
- Pré-cadastro de clientes

### Pedidos

- [Pedidos](pedidos/PEDIDOS.md)
- Orçamentos
- Tracking de pedidos
- Duplicação de pedidos

### Fluxo de utilização pelo usuário

- Inclusão de pedido ou orçamento
- Conversão de orçamento em pedido
- Acompanhamento (Tracking)
- Edição de pedido ainda não aprovado

### Notificações e comunicação (E-mails)

- Alerta de novo pedido

### Integração

- API Rest
- Filas

### Projetos relacionados

- [API](https://github.com/prounion-software/vendas-web-api)
