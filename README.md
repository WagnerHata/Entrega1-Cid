# Entrega 1 — Modelo Conceitual (DER)
### Modelagem de Banco de Dados para Gestão de Vendas Varejo e Atacado

## Metadados

- Wagner - 47584629
- Augusto - 48832588
- Felipe - 47868384
- Matheus - 47854928

---

## 1. Caracterização da Organização


**Nome e natureza da organização:** RIVIAR PRESENTES — empresa comercial de pequeno porte com atuação no comércio varejista e atacadista em ambiente presencial.

**Contexto e porte:** Organização com fins lucrativos. A operação conta com 2 funcionários utilizando o sistema simultaneamente durante turnos de 12 horas. O volume médio varia de 60 a 100 vendas por dia (~1.800 a 3.000 transações/mês), com projeção de crescimento de 30% ao ano.

**Problemas e necessidades identificados:** A organização enfrenta dificuldades no acompanhamento centralizado do histórico de transações, controle de preços no atendimento presencial e rastreamento de alterações e acréscimos em pedidos em aberto. Há descentralização das informações de vendas, risco de inconsistência no histórico de preços praticados quando há reajuste no catálogo, e ausência de relatórios automatizados de consolidação diária. A falta de um sistema integrado dificulta a obtenção ágil de métricas de desempenho diárias, como o Ticket Médio.

**Objetivo do projeto:** Projetar e modelar a camada conceitual de um banco de dados relacional para centralizar o cadastro de clientes, estruturar o catálogo de produtos com precificação dupla (atacado/varejo), permitir a gestão flexível de pedidos e gerar insumos para relatórios gerenciais diários. Esta Entrega 1 limita-se ao levantamento de requisitos de negócio, mapeamento de processos, construção do Dicionário de Dados Conceitual e elaboração do DER para a operação comercial atual.

**Justificativa da escolha:** A organização possui volume expressivo de transações e regras operacionais ricas (vendas balcão sem identificação de cliente, adição flexível de itens no mesmo pedido, precificação diferenciada por volume), sendo um cenário ideal para modelagem de banco de dados relacional.

**Evidências da organização:**
- Imagem da visita: `img/local.jpeg`
- Instagram da empresa: https://www.instagram.com/riviarpresentes/
- Endereço 1: R. Sete de Setembro, 555 - Parque Suzano, Suzano - SP, 08673-020
- Endereço 2: Av. Ver. Narciso Yague Guimarães, 1001 - Jardim Armenia, Mogi das Cruzes - SP, 08780-000
- Forma de contato: (11) 98295-4874 ; contato@riviarpresentes.com.br ; Vitória e Ricardo.

---

## 2. Processos de Negócio


**Principais processos mapeados:**

1. **Atendimento e Prospecção:** Identificação do perfil do cliente (presencial), apresentação do catálogo e negociação de valores (atacado/varejo).
2. **Elaboração e Flexibilização do Pedido:** Abertura do orçamento/pedido sob um código numérico. O cliente pode retornar e acrescentar/alterar itens no mesmo pedido sem a necessidade de gerar um novo registro.
3. **Despacho e Logística:** Preparação do produto e envio. Caso haja necessidade de envio via Correios, o endereço completo é exigido e vinculado ao cadastro.
4. **Atualização de Catálogo e Maquininhas:** Alteração de preços do produto na página e terminais de venda, mantendo o valor histórico congelado nas vendas antigas.


---

## 3. Requisitos do Sistema


### 3.1 Requisitos Funcionais

- **RF01 - Cadastro de Clientes:** Permitir o registro de clientes com Nome, Telefone, E-mail, Documento (CPF/RG) e Endereço Completo para entregas.
- **RF02 - Venda Balcão (Venda Solta):** Permitir o registro de vendas/pedidos sem obrigatoriedade de vincular um cliente cadastrado.
- **RF03 - Precificação Dupla no Catálogo:** Permitir o cadastro de produtos com Código de Estoque (SKU), Código de Catálogo e valores diferenciados para Atacado e Varejo.
- **RF04 - Alteração de Pedidos em Aberto:** Permitir acrescentar, editar ou remover itens de um pedido previamente aberto utilizando seu identificador original.
- **RF05 - Preservação de Histórico de Preço:** Gravar o preço praticado do item no momento da venda, garantindo que reajustes futuros no catálogo não alterem o valor histórico das vendas passadas.
- **RF06 - Correção Lógica de Dados:** Garantir que exclusões acidentais sejam tratadas via sinalização/correção de registros, sem perda indesejada do histórico.
- **RF07 - Relatório Diário de Desempenho:** Emitir relatórios diários do faturamento consolidado, contagem de vendas e cálculo automático do Ticket Médio.

### 3.2 Requisitos Não Funcionais

- **RNF01 - Concorrência e Disponibilidade:** O sistema deve suportar no mínimo 2 usuários simultâneos operando ativamente durante 12 horas diárias.
- **RNF02 - Desempenho:** Processar consultas e registros em tempo inferior a 2 segundos para o volume de até 100 vendas/dia, suportando crescimento projetado de 30%.
- **RNF03 - Segurança e LGPD:** O acesso aos dados pessoais confidenciais dos clientes e relatórios financeiros deve ser restrito exclusivamente aos perfis de Diretor e Gerente.

---

## 4. Regras de Negócio

**Regras operacionais:**

1. **Opcionalidade do Cliente:** Toda venda deve pertencer a 0 ou 1 Cliente (venda solta/balcão é permitida).
2. **Identificação Única do Pedido:** Cada pedido é identificado por um código sequencial único e pela data/hora do registro.
3. **Congelamento do Valor Unitário:** Ao incluir um produto no pedido, o valor praticado no momento (Atacado ou Varejo) deve ser copiado para o item do pedido.
4. **Nível de Acesso:** Apenas os cargos de Diretor e Gerente possuem permissão para visualizar e alterar dados sigilosos e relatórios de faturamento.

**Restrições organizacionais:**

1. **LGPD (Lei Geral de Proteção de Dados):** Como legislação federal, exige que dados pessoais dos clientes (documento, telefone, e-mail, endereço) sejam tratados com controle de acesso e finalidade definida. Essa restrição legal molda diretamente o modelo: obriga a separação entre dados operacionais (ex.: histórico de compras) e dados pessoais sensíveis, e justifica a regra de acesso restrito a Diretor/Gerente (RNF03) já no nível conceitual, não apenas como característica técnica do sistema.
2. **Operação exclusivamente presencial, com equipe reduzida:** A organização opera com apenas 2 funcionários por turno de 12 horas, sem canal de vendas online. Essa limitação de porte restringe o escopo do modelo — não há necessidade, por exemplo, de suportar múltiplos canais de venda ou grandes volumes de concorrência —, e é o motivo pelo qual o requisito RNF01 fixa apenas 2 usuários simultâneos como referência mínima.

---

## 5. Dicionário de Dados Conceitual (Preliminar)


### Entidade: CLIENTE

| Atributo | Descrição | Regra de negócio associada |
|---|---|---|
| id_cliente | Identificador único do cliente | Chave Primária. Gerado automaticamente. |
| nome | Nome do cliente / identificador | Obrigatório. |
| documento | CPF ou CNPJ do cliente | Opcional no atendimento rápido; único se preenchido. |
| telefone | Telefone / WhatsApp | Obrigatório para contato/vendas virtuais. |
| email | E-mail do cliente | Opcional. |
| endereco_completo | Logradouro, número, CEP, cidade | Obrigatório apenas quando há entrega. |

### Entidade: FUNCIONARIO

| Atributo | Descrição | Regra de negócio associada |
|---|---|---|
| id_funcionario | Identificador único do funcionário | Chave Primária. Gerado pelo sistema. |
| nome | Nome completo do funcionário | Obrigatório. |
| cpf | Documento do funcionário | Obrigatório e único. |
| cargo | Função (Vendedor, Caixa, Gerente) | Indicação da permissão e atribuição. |
| comissao_percentual | Percentual de comissão sobre vendas | Valor decimal padrão do vendedor (opcional). |

### Entidade: FORNECEDOR

| Atributo | Descrição | Regra de negócio associada |
|---|---|---|
| id_fornecedor | Identificador do fornecedor | Chave Primária. |
| razao_social | Razão Social ou Nome Fantasia | Obrigatório. |
| cnpj | CNPJ da empresa fornecedora | Obrigatório e único. |
| telefone_contato | Canal direto de atendimento | Obrigatório. |

### Entidade: CATEGORIA

| Atributo | Descrição | Regra de negócio associada |
|---|---|---|
| id_categoria | Identificador da categoria | Chave Primária. |
| nome_categoria | Nome do agrupamento (ex: Bebidas, Roupas) | Obrigatório e único. |

### Entidade: PRODUTO

| Atributo | Descrição | Regra de negócio associada |
|---|---|---|
| id_produto | Identificador único do produto | Chave Primária. Gerado pelo sistema. |
| codigo_estoque | Código interno de estoque (SKU) | Obrigatório e único. |
| nome_produto | Nome / descrição do produto | Obrigatório. |
| preco_varejo | Preço de venda unitário no varejo | Valor decimal positivo obrigatório. |
| preco_atacado | Preço de venda unitário no atacado | Valor decimal positivo obrigatório. |
| quantidade_estoque | Saldo físico atual em estoque | Atualizado por entradas e vendas. |
| id_categoria | Referência à categoria do produto | Obrigatório. |

### Entidade: PEDIDO

| Atributo | Descrição | Regra de negócio associada |
|---|---|---|
| id_pedido | Código identificador do pedido | Chave Primária. Único. |
| data_hora | Data e horário da venda | Preenchimento automático no registro. |
| status | Situação (Orçamento, Aberto, Concluído, Cancelado) | Permite alteração enquanto estiver Aberto. |
| valor_total | Somatório total do pedido | Calculado automaticamente a partir dos itens. |
| id_cliente | Referência ao cliente | Opcional (permite NULL para venda solta). |
| id_funcionario | Funcionário responsável pelo atendimento | Obrigatório para identificação de autoria/comissão. |

### Entidade: ITEM_PEDIDO

*(entidade associativa que resolve o relacionamento N:M entre PEDIDO e PRODUTO)*

| Atributo | Descrição | Regra de negócio associada |
|---|---|---|
| id_item_pedido | Identificador único do item dentro do pedido | Chave Primária. Gerado automaticamente. |
| id_pedido | Referência ao pedido | Chave Estrangeira. Obrigatório. |
| id_produto | Referência ao produto | Chave Estrangeira. Obrigatório. |
| quantidade | Quantidade do produto no pedido | Obrigatório. Valor inteiro positivo. |
| preco_praticado | Preço unitário (atacado ou varejo) vigente no momento da venda | Obrigatório. Copiado do PRODUTO no momento do registro (Regra: Congelamento do Valor Unitário / RF05). |
| subtotal | Valor do item (quantidade × preco_praticado) | Calculado automaticamente. |

### Entidade: PAGAMENTO

| Atributo | Descrição | Regra de negócio associada |
|---|---|---|
| id_pagamento | Identificador único do pagamento | Chave Primária. Gerado automaticamente. |
| id_pedido | Referência ao pedido pago | Chave Estrangeira. Obrigatório. |
| forma_pagamento | Meio utilizado (dinheiro, cartão, PIX, etc.) | Obrigatório. |
| valor_pago | Valor recebido nessa transação de pagamento | Obrigatório. Valor decimal positivo. |
| data_hora_pagamento | Data e horário do pagamento | Preenchimento automático. |
| status_pagamento | Situação do pagamento (Pago, Parcial, Pendente) | Permite representar pagamento fracionado do mesmo pedido. |

### Entidade: MOVIMENTACAO_ESTOQUE

| Atributo | Descrição | Regra de negócio associada |
|---|---|---|
| id_movimentacao | Identificador único da movimentação | Chave Primária. Gerado automaticamente. |
| id_produto | Referência ao produto movimentado | Chave Estrangeira. Obrigatório. |
| id_fornecedor | Referência ao fornecedor de origem (quando aplicável) | Chave Estrangeira. Opcional (permite NULL para ajustes internos). |
| tipo_movimentacao | Natureza da movimentação (Entrada, Saída, Ajuste) | Obrigatório. |
| quantidade | Quantidade movimentada | Obrigatório. |
| data_hora | Data e horário da movimentação | Preenchimento automático. |

### Entidade: ENDERECO

| Atributo | Descrição | Regra de negócio associada |
|---|---|---|
| id_endereco | Identificador único do endereço | Chave Primária. Gerado automaticamente. |
| logradouro | Rua, Avenida, Alameda, etc. | Obrigatório. |
| numero | Número do imóvel | Obrigatório (usar "S/N" para imóveis sem número). |
| complemento | Apartamento, bloco, ponto de referência | Opcional. |
| bairro | Bairro ou distrito | Obrigatório. |
| cidade | Nome do município | Obrigatório. |
| estado | Sigla da unidade federativa (UF, ex: SP, RJ) | Obrigatório (2 caracteres). |
| cep | Código de Endereçamento Postal | Obrigatório para envios/entregas. |
| principal | Indica se é o endereço padrão do cliente | Booleano (Sim/Não). Ajuda a preencher vendas automaticamente. |
| id_cliente | Referência ao cliente proprietário | Chave Estrangeira. Obrigatório para vincular ao cliente. |

---

## 6. Modelagem Conceitual (Entidades, Atributos, Relacionamentos)


**Entidades reconhecidas:**

- **CLIENTE** — representa as pessoas ou organizações que realizam compras. O documento é opcional porque o sistema precisa permitir atendimentos rápidos ou vendas sem cadastro completo, mantendo a possibilidade de identificação formal quando necessário.
- **FUNCIONARIO** — representa os colaboradores. A associação de cada pedido a um funcionário garante rastreabilidade e permite calcular comissão via `comissao_percentual`.
- **FORNECEDOR** — representa as empresas responsáveis pelo fornecimento dos produtos, identificadas por CNPJ único, permitindo relacioná-las às entradas de estoque.
- **CATEGORIA** — criada para classificar os produtos e evitar repetição da informação de classificação em cada produto.
- **PRODUTO** — representa os itens comercializados, com dois preços (`preco_varejo` e `preco_atacado`) para refletir a precificação dupla do negócio.
- **PEDIDO** — representa uma operação comercial registrada, concentrando data/hora, status, cliente e funcionário responsável.
- **ITEM_PEDIDO** — entidade associativa que resolve o relacionamento N:M entre PEDIDO e PRODUTO, armazenando quantidade, preço praticado e subtotal — informações que pertencem à participação do produto numa venda específica, não ao produto em si.
- **PAGAMENTO** — representa cada transação de recebimento vinculada a um pedido, permitindo múltiplas formas ou parcelas para a mesma compra.
- **MOVIMENTACAO_ESTOQUE** — representa entradas, saídas e ajustes de estoque ao longo do tempo, mantendo histórico em vez de apenas o saldo atual.
- **ENDERECO** — separada de CLIENTE para permitir que um mesmo cliente possua mais de um endereço (residencial, comercial, entrega).

**Atributos e classificações:** detalhados na Seção 5 (Dicionário de Dados Conceitual), incluindo, para cada entidade, chave primária, atributos obrigatórios/opcionais e chaves estrangeiras.

**Relacionamentos pertinentes:**

| Relacionamento | Origem | Destino | Tipo de Conexão |
|---|---|---|---|
| CLIENTE ↔ ENDERECO | (1,1) | (0,N) | Um para Muitos (1:N) |
| CLIENTE ↔ PEDIDO | (0,1) | (0,N) | Um para Muitos (1:N) Opcional |
| FUNCIONARIO ↔ PEDIDO | (1,1) | (0,N) | Um para Muitos (1:N) |
| CATEGORIA ↔ PRODUTO | (1,1) | (0,N) | Um para Muitos (1:N) |
| PEDIDO ↔ PRODUTO | (1,N) | (0,N) | Muitos para Muitos (N:M), resolvido via ITEM_PEDIDO |
| PEDIDO ↔ PAGAMENTO | (1,1) | (1,N) | Um para Muitos (1:N) Obrigatório |
| PRODUTO ↔ MOVIMENTACAO_ESTOQUE | (1,1) | (0,N) | Um para Muitos (1:N) |
| FORNECEDOR ↔ MOVIMENTACAO_ESTOQUE | (0,1) | (0,N) | Um para Muitos (1:N) Opcional |

- **CLIENTE ↔ ENDERECO (1:N):** um cliente pode ter nenhum ou vários endereços; cada endereço pertence a um único cliente.
- **CLIENTE ↔ PEDIDO (1:N opcional):** cada pedido pode ou não estar associado a um cliente, respeitando a regra de venda solta.
- **FUNCIONARIO ↔ PEDIDO (1:N):** cada pedido tem um funcionário responsável, dando rastreabilidade e base para comissão.
- **CATEGORIA ↔ PRODUTO (1:N):** cada produto pertence a uma categoria, evitando repetição de dados de classificação.
- **PEDIDO ↔ PRODUTO (N:M via ITEM_PEDIDO):** um pedido pode conter vários produtos e um produto pode estar em vários pedidos; a quantidade e o preço praticado não são atributos do produto, mas da sua participação em um pedido específico.
- **PEDIDO ↔ PAGAMENTO (1:N):** permite que um mesmo pedido seja quitado com mais de uma forma ou parcela de pagamento.
- **PRODUTO ↔ MOVIMENTACAO_ESTOQUE (1:N):** mantém o histórico de entradas/saídas/ajustes de cada produto, enquanto `quantidade_estoque` reflete apenas o saldo atual.
- **FORNECEDOR ↔ MOVIMENTACAO_ESTOQUE (1:N opcional):** nem toda movimentação está ligada a uma compra de fornecedor (ex.: ajustes internos), por isso a associação é opcional.

**Restrições e políticas organizacionais aplicadas ao modelo:** a exigência da LGPD (Seção 4) reforça a separação entre dados operacionais e dados pessoais sensíveis em CLIENTE e ENDERECO, e justifica que o controle de acesso (Diretor/Gerente) seja tratado como regra de negócio desde o nível conceitual. A operação presencial com equipe reduzida (Seção 4) é o que mantém o modelo enxuto, sem entidades voltadas a múltiplos canais de venda.

---

## 7. Diagrama Entidade-Relacionamento (DER)


O DER está anexado separadamente ao repositório, em imagem (`img/der.png` — ajustar caminho conforme o arquivo real).

O diagrama deve representar todas as 10 entidades levantadas nas Seções 5 e 6 — **incluindo CLIENTE, FUNCIONARIO, FORNECEDOR, CATEGORIA, PRODUTO, PEDIDO, ENDERECO, ITEM_PEDIDO, PAGAMENTO e MOVIMENTACAO_ESTOQUE** — com seus atributos e as cardinalidades exatamente como descritas na tabela de relacionamentos da Seção 6.


---

## 8. Justificativa Técnica


A modelagem foi desenvolvida para representar, de forma estruturada, os principais elementos do processo de cadastro de clientes, funcionários, fornecedores, produtos, pedidos, endereços, pagamentos e movimentações de estoque. A definição das entidades e atributos foi baseada nas necessidades operacionais levantadas na pesquisa de campo, buscando garantir que as informações essenciais às operações comerciais fossem representadas sem redundância desnecessária.

Algumas decisões de abstração merecem destaque:

- **ITEM_PEDIDO como entidade associativa, em vez de atributo multivalorado em PEDIDO:** a alternativa de armazenar produtos diretamente como uma lista dentro de PEDIDO violaria a 1ª Forma Normal e impediria consultas relacionais eficientes (ex.: total vendido por produto). Criar uma entidade própria permite guardar quantidade, preço praticado e subtotal por item, sem repetir dados do PRODUTO.
- **Preço duplicado em PRODUTO (`preco_varejo`/`preco_atacado`) em vez de uma tabela de tabelas de preço:** para o porte atual da organização (2 tabelas fixas, sem variação por cliente ou período), duas colunas são suficientes e mais simples do que uma entidade `TABELA_PRECO` separada, que seria uma abstração desnecessária nesta etapa.
- **ENDERECO separado de CLIENTE:** evita fixar um único conjunto de campos de endereço por cliente e permite múltiplos endereços (residencial, comercial, entrega), sem exigir alteração estrutural futura.
- **MOVIMENTACAO_ESTOQUE como histórico, em vez de apenas atualizar `quantidade_estoque`:** manter apenas o saldo atual impediria auditoria e rastreamento de entradas/saídas ao longo do tempo — informação relevante dado o crescimento projetado de 30% ao ano.
- **PEDIDO ↔ CLIENTE opcional (0,1):** alternativa seria tornar o cliente obrigatório em todo pedido, mas isso contrariaria a regra observada em campo de venda balcão sem identificação (RF02).

O modelo foi estruturado para permitir evolução posterior para os modelos lógico e físico, nos quais serão definidos chaves estrangeiras, tipos de dados, restrições de integridade e mecanismos de implementação no banco de dados.

---

## 9. Uso de Inteligência Artificial

| Item | Registro |
|---|---|
| **Ferramenta e etapa** | ChatGPT / Gemini — organização dos requisitos e formatação do Dicionário de Dados em Markdown. |
| **Motivação** | Agilizar a estruturação técnica das informações coletadas no questionário de negócio para o modelo acadêmico exigido. |
| **Prompt(s) utilizados** | "Com base nas respostas do questionário de negócio, estruture os Requisitos Funcionais, Regras de Negócio e Dicionário de Dados para a Entrega 1." |
| **Resposta recebida** | A IA retornou uma primeira versão dos Requisitos Funcionais/Não Funcionais e das tabelas do Dicionário de Dados, incluindo a sugestão de tornar a identificação do cliente obrigatória em todo pedido. |
| **Fontes consultadas e verificadas** | Nenhuma fonte externa foi citada pela IA nessa etapa; o conteúdo gerado foi verificado apenas contra as respostas do questionário de negócio e as observações da visita de campo à RIVIAR PRESENTES. |
| **Trechos rejeitados ou corrigidos** | O grupo rejeitou a sugestão de tornar a identificação do cliente obrigatória em todos os pedidos e ajustou a cardinalidade CLIENTE↔PEDIDO para opcional (0,1), a fim de respeitar a regra de venda solta informada pelo entrevistado. |
| **Justificativa da escolha final** | A cardinalidade opcional foi mantida porque reflete uma prática real e frequente da organização (venda balcão sem cadastro); tornar o cliente obrigatório geraria atrito no atendimento presencial e não correspondia ao processo observado em campo. |
| **Reflexão crítica** | A IA tende a sugerir modelos "mais normalizados" por padrão (ex.: cliente obrigatório), sem considerar particularidades operacionais levantadas apenas na pesquisa de campo. Isso reforça a necessidade de validar toda sugestão da IA contra a observação real da organização, e não aceitar recomendações genéricas de modelagem sem esse contraste. |

---

## Referências Bibliográficas

ELMASRI, Ramez; NAVATHE, Shamkant B. Sistemas de Banco de Dados. 7. ed. São Paulo: Pearson, 2011.
