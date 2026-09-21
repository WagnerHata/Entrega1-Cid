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

# Justificativa Técnica — Dicionário de Dados Conceitual e DER

## 1. Justificativa da Modelagem

A modelagem foi desenvolvida com o objetivo de representar, de forma estruturada, os principais elementos envolvidos no processo de **cadastro de clientes, funcionários, fornecedores, produtos, pedidos, endereços, pagamentos e movimentações de estoque**.

A definição das entidades e seus respectivos atributos foi baseada nas necessidades operacionais do sistema, buscando garantir que as informações essenciais para as operações comerciais sejam representadas sem gerar redundância desnecessária.

O modelo também foi estruturado de forma a permitir sua evolução posterior para os modelos **lógico e físico**, nos quais serão definidos detalhes como chaves estrangeiras, tipos de dados, restrições de integridade e mecanismos de implementação no banco de dados.

---

## 2. Justificativa das Entidades

### CLIENTE

A entidade `CLIENTE` representa as pessoas ou organizações que realizam compras no sistema.

Foram definidos atributos básicos de identificação e contato, como nome, documento, telefone e e-mail. O documento foi definido como opcional porque o sistema deve permitir **atendimentos rápidos ou vendas sem cadastro completo**, mantendo a possibilidade de identificação formal quando necessário.

O endereço não é tratado como obrigatório diretamente para todo cliente, pois sua necessidade depende da operação realizada. Entretanto, quando houver entrega, deve existir um endereço válido associado ao cliente.

Essa abordagem evita exigir informações desnecessárias em vendas presenciais simples e, ao mesmo tempo, permite atender operações que dependam de entrega.

---

### FUNCIONARIO

A entidade `FUNCIONARIO` representa os colaboradores responsáveis pelas operações realizadas no sistema.

A associação de cada pedido a um funcionário permite identificar **quem realizou o atendimento ou registrou a venda**, proporcionando rastreabilidade das operações.

O atributo `cargo` permite diferenciar as funções desempenhadas pelos colaboradores, enquanto `comissao_percentual` permite representar a regra de comissão aplicável aos funcionários que possuem direito a comissão sobre vendas.

O CPF foi definido como obrigatório e único para evitar o cadastro duplicado de um mesmo funcionário.

---

### FORNECEDOR

A entidade `FORNECEDOR` representa as empresas responsáveis pelo fornecimento dos produtos comercializados.

A identificação por CNPJ permite manter um cadastro único de cada fornecedor. A razão social ou nome fantasia permite sua identificação no sistema, enquanto o telefone possibilita o contato operacional para compras, reposições e demais atividades relacionadas ao abastecimento do estoque.

A existência dessa entidade também permite relacionar posteriormente fornecedores às movimentações de entrada de estoque.

---

### CATEGORIA

A entidade `CATEGORIA` foi criada para permitir a classificação dos produtos.

A separação entre `CATEGORIA` e `PRODUTO` evita que informações de classificação sejam repetidas em cada registro de produto.

Por exemplo, diversos produtos podem pertencer à categoria `Bebidas`, `Roupas` ou `Eletrônicos`, mantendo uma única definição da categoria.

O nome da categoria deve ser único para impedir a existência de categorias duplicadas com a mesma finalidade.

---

### PRODUTO

A entidade `PRODUTO` representa os itens comercializados pelo estabelecimento.

O atributo `codigo_estoque` funciona como identificador operacional do produto, permitindo sua localização e controle no estoque.

A existência de dois preços, `preco_varejo` e `preco_atacado`, permite representar diferentes condições comerciais de venda.

O atributo `quantidade_estoque` representa o saldo disponível e deve ser atualizado de acordo com as entradas e saídas registradas no sistema.

A associação com `CATEGORIA` permite organizar os produtos de maneira estruturada e facilita consultas, relatórios e operações de gerenciamento do catálogo.

---

### PEDIDO

A entidade `PEDIDO` representa uma operação comercial registrada no sistema.

O pedido concentra informações gerais da venda, como data e hora, status, cliente e funcionário responsável.

O atributo `status` permite representar diferentes etapas do ciclo de vida da operação, como orçamento, pedido aberto, conclusão ou cancelamento.

O `valor_total` é derivado dos produtos e quantidades presentes no pedido, evitando que o valor seja definido manualmente de forma independente dos itens comercializados.

A associação com `CLIENTE` é opcional porque o sistema precisa permitir vendas sem cadastro de cliente, enquanto a associação com `FUNCIONARIO` é obrigatória para garantir a identificação do responsável pela operação.

---

### ENDERECO

A entidade `ENDERECO` foi separada de `CLIENTE` para permitir que um mesmo cliente possua **mais de um endereço**.

Essa separação também evita armazenar um conjunto fixo de campos de endereço diretamente em `CLIENTE`, proporcionando maior flexibilidade para situações como endereço residencial, comercial ou diferentes locais de entrega.

O atributo `principal` permite identificar o endereço padrão do cliente e pode ser utilizado para facilitar o preenchimento de operações de entrega.

---

## 3. Justificativa dos Relacionamentos

### CLIENTE ↔ ENDERECO

O relacionamento foi definido como **1:N**, pois um cliente pode possuir nenhum ou vários endereços cadastrados, enquanto cada endereço pertence a um único cliente.

A cardinalidade permite representar clientes sem endereço cadastrado e clientes que possuem diferentes endereços.

---

### CLIENTE ↔ PEDIDO

O relacionamento foi definido como **1:N opcional**.

Um cliente pode realizar nenhum ou vários pedidos, enquanto cada pedido pode estar associado a um cliente ou ser realizado como uma venda sem identificação do cliente.

Essa decisão está diretamente relacionada à regra de negócio de permitir **venda solta ou atendimento rápido**.

---

### FUNCIONARIO ↔ PEDIDO

O relacionamento é **1:N**.

Um funcionário pode registrar vários pedidos ao longo de sua atividade, enquanto cada pedido possui um funcionário responsável pelo atendimento ou registro.

Essa relação permite manter a rastreabilidade das vendas e também fornece base para o cálculo de comissões.

---

### CATEGORIA ↔ PRODUTO

O relacionamento é **1:N**.

Uma categoria pode conter vários produtos, enquanto cada produto pertence a uma categoria.

Essa estrutura evita a repetição das informações da categoria em cada produto e mantém uma classificação padronizada.

---

### PEDIDO ↔ PRODUTO

O relacionamento é **N:M**.

Um pedido pode conter vários produtos e um mesmo produto pode aparecer em diversos pedidos diferentes.

Esse relacionamento necessita de uma estrutura intermediária, conceitualmente representada como **ITEM_PEDIDO**, responsável por armazenar informações específicas da participação do produto no pedido, como:

* quantidade;
* preço praticado no momento da venda;
* subtotal;
* eventualmente descontos ou outras informações comerciais.

Essa separação é importante porque a quantidade e o preço de um produto não são características permanentes do produto, mas informações referentes à sua participação em uma determinada venda.

---

### PEDIDO ↔ PAGAMENTO

O relacionamento foi definido como **1:N**, permitindo que um pedido possua um ou mais pagamentos.

Essa estrutura possibilita representar situações em que uma mesma compra seja quitada utilizando diferentes formas ou parcelas de pagamento.

Por exemplo, um pedido pode possuir pagamentos associados a diferentes modalidades, conforme as regras comerciais adotadas pelo estabelecimento.

---

### PRODUTO ↔ MOVIMENTACAO_ESTOQUE

O relacionamento é **1:N**.

Um produto pode possuir diversas movimentações de estoque ao longo do tempo, incluindo entradas, saídas, ajustes e outras operações.

Essa estrutura permite manter um histórico das alterações do estoque em vez de registrar somente o saldo atual.

Dessa forma, `quantidade_estoque` representa o estado atual, enquanto `MOVIMENTACAO_ESTOQUE` permite representar o histórico das operações que modificaram esse estado.

---

### FORNECEDOR ↔ MOVIMENTACAO_ESTOQUE

O relacionamento foi definido como **1:N opcional**.

Um fornecedor pode estar associado a várias movimentações de entrada de estoque, enquanto uma movimentação pode possuir ou não um fornecedor identificado.

A associação opcional permite representar movimentações internas, ajustes ou outras operações que não estejam diretamente relacionadas a uma compra realizada de um fornecedor.

---

## 4. Justificativa das Cardinalidades

As cardinalidades foram definidas com base nas regras operacionais do sistema.

De forma geral:

* `1:1` indica participação obrigatória de uma ocorrência em relação à outra;
* `0:1` indica que a associação é opcional e limitada a uma ocorrência;
* `0:N` indica que uma entidade pode não possuir nenhuma ou possuir várias ocorrências relacionadas;
* `1:N` indica que existe pelo menos uma ocorrência no lado correspondente;
* `N:M` indica que várias ocorrências de uma entidade podem estar relacionadas a várias ocorrências da outra.

As cardinalidades devem representar as **regras reais do negócio**, e não apenas a estrutura técnica do banco de dados.

---

## 5. Observação sobre o Nível Conceitual

Embora este documento seja classificado como **Dicionário de Dados Conceitual (Preliminar)**, alguns elementos apresentados possuem características normalmente detalhadas no modelo lógico.

São exemplos:

* `id_*`;
* indicação explícita de **Chave Primária**;
* indicação de **Chave Estrangeira**;
* utilização do conceito de `NULL`;
* detalhes específicos de implementação.

Esses elementos foram mantidos nesta etapa porque facilitam a identificação das entidades e atributos e servem como base para a construção posterior do modelo lógico.

No **DER conceitual propriamente dito**, a prioridade deve ser a representação das entidades, atributos relevantes, relacionamentos e cardinalidades. A implementação das chaves estrangeiras e dos tipos de dados deve ser detalhada no modelo lógico.

---

## 6. Justificativa da Separação entre Conceitual, Lógico e Físico

A divisão entre os níveis de modelagem permite separar as regras de negócio dos detalhes de implementação.

### Modelo Conceitual

Representa:

* entidades;
* atributos relevantes;
* relacionamentos;
* cardinalidades;
* regras de negócio principais.

### Modelo Lógico

Define:

* chaves primárias;
* chaves estrangeiras;
* tabelas;
* normalização;
* atributos e seus tipos;
* resolução de relacionamentos N:M.

### Modelo Físico

Define aspectos específicos do SGBD utilizado, como:

* tipos de dados específicos;
* índices;
* constraints;
* sequences ou auto incremento;
* estratégias de armazenamento;
* otimizações de desempenho.

Essa separação reduz a dependência do modelo conceitual em relação à tecnologia escolhida e facilita futuras alterações na implementação do sistema.

---

## 7. Conclusão

A modelagem proposta representa os principais processos do domínio comercial: **cadastro de clientes, gestão de funcionários, fornecedores, categorização de produtos, vendas, pagamentos e controle de estoque**.

A estrutura também contempla situações importantes do negócio, como vendas sem cadastro obrigatório de cliente, múltiplos endereços por cliente, diferentes modalidades de preço, múltiplos produtos por pedido, múltiplos pagamentos e histórico de movimentações de estoque.

O modelo conceitual serve, portanto, como base para a construção do **modelo lógico**, no qual os relacionamentos N:M poderão ser resolvidos por entidades associativas e as chaves e restrições de integridade serão especificadas de forma técnica.


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
