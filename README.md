# Modelagem de Banco de Dados para Gestão de Vendas Varejo e Atacado
**Entrega 1 — Modelo Conceitual (DER)**

Wagner - 47584629 | Augusto - 48832588 | Felipe - 47868384 | Matheus - 47854928

---

## Introdução

### Problema
A organização enfrenta dificuldades no acompanhamento centralizado do histórico de transações, controle de preços no atendimento presencial e rastreamento de alterações e acréscimos em pedidos em aberto. A falta de um sistema integrado dificulta a obtenção ágil de métricas de desempenho diárias, como o Ticket Médio.

### Objetivos
Projetar e modelar a camada conceitual de um banco de dados relacional para centralizar o cadastro de clientes, estruturar o catálogo de produtos com precificação dupla (atacado/varejo), permitir a gestão flexível de pedidos e gerar insumos para relatórios gerenciais diários.

### Delimitação
O escopo desta Entrega 1 limita-se ao levantamento de requisitos de negócio, mapeamento de processos, construção do Dicionário de Dados Conceitual e elaboração do Diagrama Entidade-Relacionamento (DER) para a operação comercial atual.

---

## Desenvolvimento

### Caracterização da Organização

- **Nome e natureza da organização:** Empresa comercial de pequeno porte com atuação no comércio varejista e atacadista em ambiente presencial. *RIVIAR PRESENTES*.
- **Contexto e porte:** Organização com fins lucrativos. A operação conta com 2 funcionários utilizando o sistema simultaneamente durante turnos de 12 horas. O volume médio varia de **60 a 100 vendas por dia** (~1.800 a 3.000 transações/mês), com projeção de crescimento de 30% ao ano.
- **Problemas e necessidades identificados:** Descentralização das informações de vendas, risco de inconsistência no histórico de preços praticados quando há reajuste no catálogo e ausência de relatórios automatizados de consolidação diária.
- **Justificativa da escolha:** A organização possui volume expressivo de transações e regras operacionais ricas (vendas balcão sem identificação de cliente, adição flexível de itens no mesmo pedido, precificação diferenciada por volume), sendo um cenário ideal para modelagem de banco de dados relacional.
- **Evidências da organização:** Imagem da visita: [img/local.jpeg](https://github.com/WagnerHata/Entrega1-Cid/blob/b4b3bbd4335d419c6112e43181bfeb7c06b7f28e/img/local.jpeg) | Instagram da empresa: https://www.instagram.com/riviarpresentes/ | Endereço 1: R. Sete de Setembro, 555 - Parque Suzano, Suzano - SP, 08673-020 ; Endereço 2: Av. Ver. Narciso Yague Guimarães, 1001 - Jardim Armenia, Mogi das Cruzes - SP, 08780-000 | Forma de contato: (11)98295-4874 ; contato@riviarpresentes.com.br ; Vitória e Ricardo.

---

### Processos de Negócio

#### Principais processos mapeados:
1. **Atendimento e Prospecção:** Identificação do perfil do cliente (presencial), apresentação do catálogo e negociação de valores (atacado/varejo).
2. **Elaboração e Flexibilização do Pedido:** Abertura do orçamento/pedido sob um código numérico. O cliente pode retornar e acrescentar/alterar itens no mesmo pedido sem a necessidade de gerar um novo registro.
3. **Despacho e Logística:** Preparação do produto e envio. Caso haja necessidade de envio via Correios, o endereço completo é exigido e vinculado ao cadastro.
4. **Atualização de Catálogo e Maquininhas:** Alteração de preços do produto na página e terminais de venda, mantendo o valor histórico congelado nas vendas antigas.

---

### Requisitos do Sistema

#### Requisitos Funcionais (RF)
- **RF01 - Cadastro de Clientes:** Permitir o registro de clientes com Nome, Telefone, E-mail, Documento (CPF/RG) e Endereço Completo para entregas.
- **RF02 - Venda Balcão (Venda Solta):** Permitir o registro de vendas/pedidos sem obrigatoriedade de vincular um cliente cadastrado.
- **RF03 - Precificação Dupla no Catálogo:** Permitir o cadastro de produtos com Código de Estoque (SKU), Código de Catálogo e valores diferenciados para Atacado e Varejo.
- **RF04 - Alteração de Pedidos em Aberto:** Permitir acrescentar, editar ou remover itens de um pedido previamente aberto utilizando seu identificador original.
- **RF05 - Preservação de Histórico de Preço:** Gravar o preço praticado do item no momento da venda, garantindo que reajustes futuros no catálogo não alterem o valor histórico das vendas passadas.
- **RF06 - Correção Lógica de Dados:** Garantir que exclusões acidentais sejam tratadas via sinalização/correção de registros, sem perda indesejada do histórico.
- **RF07 - Relatório Diário de Desempenho:** Emitir relatórios diários do faturamento consolidado, contagem de vendas e cálculo automático do **Ticket Médio**.

#### Requisitos Não Funcionais (RNF)
- **RNF01 - Concorrência e Disponibilidade:** O sistema deve suportar no mínimo 2 usuários simultâneos operando ativamente durante 12 horas diárias.
- **RNF02 - Desempenho:** Processar consultas e registros em tempo inferior a 2 segundos para o volume de até 100 vendas/dia, suportando crescimento projetado de 30%.
- **RNF03 - Segurança e LGPD:** O acesso aos dados pessoais confidenciais dos clientes e relatórios financeiros deve ser restrito exclusivamente aos perfis de Diretor e Gerente.

---

### Regras de Negócio

- Opcionalidade do Cliente:** Toda venda deve pertencer a 0 ou 1 Cliente (venda solta/balcão é permitida).
- Identificação Única do Pedido:** Cada pedido é identificado por um código sequencial único e pela data/hora do registro.
- Congelamento do Valor Unitário:** Ao incluir um produto no pedido, o valor praticado no momento (Atacado ou Varejo) deve ser copiado para o item do pedido.
- Nível de Acesso:** Apenas os cargos de Diretor e Gerente possuem permissão para visualizar e alterar dados sigilosos e relatórios de faturamento.

---

### Dicionário de Dados Conceitual (Preliminar)

## Entidade: CLIENTE

| **Atributo** | **Descrição** | **Regra de negócio associada** |
| --- | --- | --- |
| `id_cliente` | Identificador único do cliente | Chave Primária. Gerado automaticamente. |
| `nome` | Nome do cliente / identificador | Obrigatório. |
| `documento` | CPF ou CNPJ do cliente | Opcional no atendimento rápido; único se preenchido. |
| `telefone` | Telefone / WhatsApp | Obrigatório para contato/vendas virtuais. |
| `email` | E-mail do cliente | Opcional. |
| `endereco_completo` | Logradouro, número, CEP, cidade | Obrigatório apenas quando há entrega. |

## Entidade: FUNCIONARIO

| **Atributo** | **Descrição** | **Regra de negócio associada** |
| --- | --- | --- |
| `id_funcionario` | Identificador único do funcionário | Chave Primária. Gerado pelo sistema. |
| `nome` | Nome completo do funcionário | Obrigatório. |
| `cpf` | Documento do funcionário | Obrigatório e único. |
| `cargo` | Função (Vendedor, Caixas, Gerente) | Indicação da permissão e atribuição. |
| `comissao_percentual` | Percentual de comissão sobre vendas | Valor decimal padrão do vendedor (opcional). |

## Entidade: FORNECEDOR

| **Atributo** | **Descrição** | **Regra de negócio associada** |
| --- | --- | --- |
| `id_fornecedor` | Identificador do fornecedor | Chave Primária. |
| `razao_social` | Razão Social ou Nome Fantasia | Obrigatório. |
| `cnpj` | CNPJ da empresa fornecedora | Obrigatório e único. |
| `telefone_contato` | Canal direto de atendimento | Obrigatório. |

## Entidade: CATEGORIA

| **Atributo** | **Descrição** | **Regra de negócio associada** |
| --- | --- | --- |
| `id_categoria` | Identificador da categoria | Chave Primária. |
| `nome_categoria` | Nome do agrupamento (ex: Bebidas, Roupas) | Obrigatório e único. |

## Entidade: PRODUTO

| **Atributo** | **Descrição** | **Regra de negócio associada** |
| --- | --- | --- |
| `id_produto` | Identificador único do produto | Chave Primária. Gerado pelo sistema. |
| `codigo_estoque` | Código interno de estoque (SKU) | Obrigatório e único. |
| `nome_produto` | Nome / descrição do produto | Obrigatório. |
| `preco_varejo` | Preço de venda unitário no varejo | Valor decimal positivo obrigatório. |
| `preco_atacado` | Preço de venda unitário no atacado | Valor decimal positivo obrigatório. |
| `quantidade_estoque` | Saldo físico atual em estoque | Atualizado por entradas e vendas. |
| `id_categoria` | Referência à categoria do produto | Obrigatório. |

## Entidade: PEDIDO

| **Atributo** | **Descrição** | **Regra de negócio associada** |
| --- | --- | --- |
| `id_pedido` | Código identificador do pedido | Chave Primária. Único. |
| `data_hora` | Data e horário da venda | Preenchimento automático no registro. |
| `status` | Situação (Orçamento, Aberto, Concluído, Cancelado) | Permite alteração enquanto estiver Aberto. |
| `valor_total` | Somatório total do pedido | Calculado automaticamente a partir dos itens. |
| `id_cliente` | Referência ao cliente | Opcional (Permite NULL para venda solta). |
| `id_funcionario` | Funcionário responsável pelo atendimento | Obrigatório para identificação de autoria/comissão. |

## Entidade: ENDERECO

| **Atributo** | **Descrição** | **Regra de negócio associada** |
| --- | --- | --- |
| `id_endereco` | Identificador único do endereço | Chave Primária. Gerado automaticamente. |
| `logradouro` | Rua, Avenida, Alameda, etc. | Obrigatório. |
| `numero` | Número do imóvel | Obrigatório (usar "S/N" para imóveis sem número). |
| `complemento` | Apartamento, bloco, ponto de referência | Opcional. |
| `bairro` | Bairro ou distrito | Obrigatório. |
| `cidade` | Nome do município | Obrigatório. |
| `estado` | Sigla da unidade federativa (UF, ex: SP, RJ) | Obrigatório (2 caracteres). |
| `cep` | Código de Endereçamento Postal | Obrigatório para envios/entregas. |
| `principal` | Indica se é o endereço padrão do cliente | Booleano (Sim/Não). Ajuda a preencher vendas automaticamente. |
| `id_cliente` | Referência ao cliente proprietário | Chave Estrangeira. Obrigatório para vincular ao cliente. |

### Modelagem Conceitual e Diagrama (DER)

#### Entidades e Relacionamentos Mapeados:
| **Relacionamento**                    | **Origem** | **Destino** | **Tipo de Conexão**                      |
| ------------------------------------- | ---------- | ----------- | ---------------------------------------- |
| `CLIENTE` ↔ `ENDERECO`                | `(1,1)`    | `(0,N)`     | Um para Muitos (`1:N`)                   |
| `CLIENTE` ↔ `PEDIDO`                  | `(0,1)`    | `(0,N)`     | Um para Muitos (`1:N`) Opcional          |
| `FUNCIONARIO` ↔ `PEDIDO`              | `(1,1)`    | `(0,N)`     | Um para Muitos (`1:N`)                   |
| `CATEGORIA` ↔ `PRODUTO`               | `(1,1)`    | `(0,N)`     | Um para Muitos (`1:N`)                   |
| `PEDIDO` ↔ `PRODUTO`                  | `(1,N)`    | `(0,N)`     | Muitos para Muitos (`N:M`) com Atributos |
| `PEDIDO` ↔ `PAGAMENTO`                | `(1,1)`    | `(1,N)`     | Um para Muitos (`1:N`) Obrigatório       |
| `PRODUTO` ↔ `MOVIMENTACAO_ESTOQUE`    | `(1,1)`    | `(0,N)`     | Um para Muitos (`1:N`)                   |
| `FORNECEDOR` ↔ `MOVIMENTACAO_ESTOQUE` | `(0,1)`    | `(0,N)`     | Um para Muitos (`1:N`) Opcional          |

---

### Uso de Inteligência Artificial

| Item | O que registrar |
| :--- | :--- |
| **Ferramenta e etapa** | ChatGPT / Gemini — Organização dos requisitos e formatação do Dicionário de Dados em Markdown. |
| **Motivação** | Agilizar a estruturação técnica das informações coletadas no questionário de negócio para o modelo acadêmico exigido. |
| **Prompt utilizado** | *"Com base nas respostas do questionário de negócio, estruture os Requisitos Funcionais, Regras de Negócio e Dicionário de Dados para a Entrega 1."* |
| **Análise e Ajustes** | A IA sugeriu tornar a identificação do cliente obrigatória em todos os pedidos. O grupo rejeitou essa sugestão e ajustou a cardinalidade para opcional $(0,1)$ a fim de respeitar a regra de venda solta informada pelo entrevistado. |

---

## Conclusão

A modelagem conceitual desenvolvida atende integralmente aos requisitos operacionais levantados na pesquisa de campo. O modelo garante a integridade do histórico financeiro, possibilita a edição e inclusão de itens em pedidos em aberto e oferece suporte estruturado para relatórios diários de Ticket Médio e acompanhamento do crescimento da empresa.

---

## Referências Bibliográficas

- ELMASRI, Ramez; NAVATHE, Shamkant B. **Sistemas de Banco de Dados**. 7. ed. São Paulo: Pearson, 2011.
