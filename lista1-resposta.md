# Conceitos gerais de NoSQL

Resumo dos tipos de banco NoSQL que aparecem (ou são úteis de conhecer) para a lista, com características, principais conceitos e exemplos.

## 1. Chave-Valor (Key-Value)

**Conceito:** o modelo mais simples — cada item é um par `chave → valor`, como um dicionário/hash map gigante e distribuído.

**Características:**
- Busca **apenas pela chave exata** (não é possível consultar o conteúdo do valor).
- O valor é tratado como uma "caixa preta" (opaco) — pode ser texto, JSON, binário, o que for.
- Localização dos dados via **tabelas de hash distribuídas (DHT)**.
- Altíssima performance e escalabilidade horizontal.
- Sem esquema, sem relacionamentos.

**Exemplos:** Redis, DynamoDB, Azure Table Storage, Riak.

## 2. Orientado a Documentos (Document Store)

**Conceito:** armazena dados como **documentos semi-estruturados** (geralmente JSON/BSON), onde cada documento pode ter uma estrutura própria.

**Características:**
- Diferente do chave-valor, permite **consultar/indexar campos internos** do documento, não só a chave.
- **Schemaless**: documentos na mesma coleção podem ter campos diferentes entre si.
- Relacionamentos são resolvidos por **embedding** (aninhar dados relacionados dentro do mesmo documento).
- Boa escalabilidade horizontal, ótimo para dados variáveis/evolutivos.

**Exemplos:** MongoDB, CouchDB.

## 3. Colunar / Wide-Column

**Conceito:** dados organizados em famílias de colunas em vez de linhas fixas — otimizado para escrita/leitura em massa e grandes volumes.

**Características:**
- Cada "linha" pode ter um conjunto diferente de colunas.
- Muito usado em big data / séries temporais / alta taxa de escrita.
- Dados costumam ser desnormalizados (duplicados) de propósito para evitar joins.

**Exemplos:** Cassandra, HBase, Google Bigtable.

## 4. Grafo (Graph Database)

**Conceito:** dados modelados como **nós (entidades)** conectados por **arestas (relacionamentos)** — é o único tipo NoSQL em que o relacionamento é o conceito central.

**Características:**
- Cada aresta é única e pode carregar atributos próprios.
- Ideal para dados altamente conectados: redes sociais, recomendação, fraude, grafos de conhecimento.
- Consultas percorrem relacionamentos diretamente (muito mais eficiente que JOINs recursivos em SQL).

**Exemplos:** Neo4j, Amazon Neptune.

## Quadro-resumo

| Tipo | Unidade de dado | Busca | Relacionamento | Exemplo | Questão da lista |
|---|---|---|---|---|---|
| Chave-valor | par chave/valor | só pela chave (hash/DHT) | não | Redis, DynamoDB | Q1, Q3 |
| Documentos | documento JSON/BSON | por campo interno | embedding | MongoDB | Q1, Q3, Q5 |
| Colunar | famílias de colunas | por chave de linha/coluna | desnormalizado | Cassandra | — |
| Grafo | nós + arestas | por relacionamento | é o foco do modelo | Neo4j | Q4 |

## Conceitos gerais que amarram as questões

- **Schemaless** (sem esquema fixo) — Q3.
- **Trade-off CAP / modelo BASE** (consistência eventual em troca de disponibilidade/performance) — Q2.
- **Escalabilidade horizontal** como motivação central para usar NoSQL — Q2, Q7.
- **NoSQL = "Not Only SQL"**, não "sem SQL" — Q3, Q7.

---

## Questão 1

**Enunciado:** Verdadeiro ou Falso? Como forma de permitir as buscas em documentos semi-estruturados, um banco de dados NoSQL do tipo orientado a documentos armazena objetos indexados por chaves utilizando tabelas de hash distribuídas (CESPE/TCU 2015).

**Resposta: Falso**

### Explicação

A questão mistura dois conceitos de NoSQL diferentes: **banco orientado a documentos** e **tabela de hash distribuída (DHT)**.

**Bancos orientados a documentos (ex: MongoDB)**

Armazenam dados como **documentos semi-estruturados** (geralmente JSON/BSON), onde cada documento pode ter uma estrutura interna própria (campos, subdocumentos, arrays), sem esquema fixo.

A característica que **permite buscas** nesse tipo de banco é justamente a capacidade de **indexar e consultar o conteúdo interno dos documentos** — ou seja, é possível buscar por qualquer campo dentro do documento (`{"nome": "Marcos"}`, `{"idade": {"$gt": 30}}` etc.), não apenas pela chave que identifica o documento.

**Por que "tabela de hash distribuída" está errado aqui**

Uma **tabela de hash distribuída (DHT)** é a técnica típica dos bancos **chave-valor** (o outro tipo de NoSQL). Nesse modelo:
- Só é possível recuperar um objeto **pela chave exata** (como um dicionário gigante).
- Não há como buscar "por dentro" do valor armazenado — para o banco, o valor é uma "caixa preta" opaca.

Isso é o **oposto** do que torna os bancos de documentos úteis: a vantagem deles é justamente permitir buscas ricas sobre o conteúdo semi-estruturado, e não apenas localizar um item por hash de chave.

**Resumo da armadilha**

| Tipo NoSQL | Como localiza dados | Busca no conteúdo? |
|---|---|---|
| Chave-valor | Hash da chave (DHT) | Não (caixa preta) |
| Orientado a documentos | Índices sobre campos internos | Sim |

A questão descreve o mecanismo do **chave-valor** e atribui erroneamente ao **orientado a documentos** — por isso é **Falso**.

## Questão 2

**Enunciado:** Considere as seguintes características de um projeto de banco de dados (IBGE/2016). I. O modelo de dados é conhecido a priori e é estável; II. A integridade dos dados deve ser rigorosamente mantida; III. Velocidade e escalabilidade são preponderantes. Dessas características, o emprego de bancos de dados NoSQL é favorecido somente por:

**Resposta: a) III**

### Explicação

- **I. Modelo de dados conhecido a priori e estável** → favorece **relacional (SQL)**. Quando a estrutura dos dados é conhecida e não muda, faz sentido usar um esquema fixo (schema), que é o ponto forte do modelo relacional.
- **II. Integridade rigorosa dos dados** → favorece **relacional (SQL)**. Bancos relacionais são construídos em torno de ACID (Atomicidade, Consistência, Isolamento, Durabilidade). NoSQL geralmente troca consistência forte por desempenho/disponibilidade (CAP, modelo BASE).
- **III. Velocidade e escalabilidade preponderantes** → favorece **NoSQL**. É justamente para isso que o NoSQL foi criado: escalar horizontalmente e entregar alta performance em grande volume de dados, geralmente abrindo mão de integridade/consistência rígida.

**Resumo do trade-off**

| Prioridade | Modelo mais indicado |
|---|---|
| Esquema estável e conhecido | Relacional (SQL) |
| Integridade rigorosa (ACID) | Relacional (SQL) |
| Velocidade e escalabilidade | NoSQL |

Como somente o item III favorece o uso de NoSQL, a resposta correta é **a) III**.

## Questão 3

**Enunciado:** Sobre os banco de dados NoSQL, assinale a afirmativa correta.
a) não podem ser indexados
b) são considerados bancos de dados relacionais
c) deve ser definido um esquema fixo antes de qualquer operação
d) são exemplos de NoSQL: mongoDB, Firebird, DynamoDB, SQLite, Access, Azure Table

**Resposta: d) (com ressalva — ver explicação)**

### Explicação

- **a) "não podem ser indexados"** → Falso. Bancos NoSQL podem sim ser indexados (índices em campos, compostos, de texto, geoespaciais etc.). Indexação não é exclusividade do modelo relacional.
- **b) "são considerados bancos de dados relacionais"** → Falso. NoSQL significa "Not Only SQL", ou seja, sistemas que fogem do modelo relacional tradicional (tabelas, chaves estrangeiras, esquema fixo). É uma contradição direta chamar NoSQL de relacional.
- **c) "deve ser definido um esquema fixo antes de qualquer operação"** → Falso. É o contrário: uma das principais características do NoSQL é o schema flexível (schemaless) — é possível inserir documentos/registros com estruturas diferentes sem declarar um esquema rígido antecipadamente.
- **d) "são exemplos de NoSQL: mongoDB, Firebird, DynamoDB, SQLite, Access, Azure Table"** → Candidata a correta, mas com erro na lista: mistura bancos NoSQL com bancos relacionais.

| Banco | Tipo |
|---|---|
| MongoDB | NoSQL (documentos) |
| DynamoDB | NoSQL (chave-valor, AWS) |
| Azure Table | NoSQL (chave-valor, Microsoft) |
| Firebird | Relacional (SQL) |
| SQLite | Relacional (SQL) |
| Access | Relacional (SQL) |

### Conclusão

Por eliminação, **d** é a alternativa "menos errada" (é a única que ao menos cita exemplos reais de NoSQL, ainda que misturados com exemplos errados), sendo normalmente o gabarito adotado nesse tipo de questão. Ressalva importante: **Firebird, SQLite e Access são bancos relacionais, não NoSQL** — a frase como um todo contém um erro factual, possivelmente por erro de digitação/transcrição do material original.

## Questão 4

**Enunciado:** Verdadeiro ou Falso? Com relação à forma como os dados são armazenados e manipulados no desenvolvimento de aplicações, julgue os itens a seguir. Em um banco de dados NoSQL do tipo grafo, cada arco é definido por um identificador único e expresso como um par chave/valor.

**Resposta: Verdadeiro**

### Explicação

A afirmação descreve corretamente o **modelo de grafo com propriedades (property graph model)**, usado por bancos como Neo4j.

Nesse modelo:
- Um **nó (vértice)** representa uma entidade e é definido por um **identificador único** + um rótulo (label) + um conjunto de propriedades expressas como pares **chave/valor** (ex.: `nome: "Marcos"`, `idade: 30`).
- Um **arco (aresta/relacionamento)** conecta dois nós e também é definido por um **identificador único**, além de um tipo/rótulo (ex.: `SEGUE`, `COMPROU`) e, assim como os nós, pode carregar suas próprias propriedades expressas como pares **chave/valor** (ex.: `desde: 2020`, `peso: 0.8`).

Ou seja, tanto nós quanto arcos são "cidadãos de primeira classe" no modelo de grafo, cada um com **id único** e **atributos representados como chave/valor** — é exatamente o que o enunciado descreve para o arco. Por isso a afirmação é **Verdadeira**.

Isso reforça a diferença do grafo para os demais tipos NoSQL: enquanto em chave-valor e documentos o relacionamento não é um conceito de primeira classe, no grafo o relacionamento (arco) é tratado com a mesma riqueza estrutural de uma entidade (nó).

## Questão 6

**Enunciado:** Em uma tabela chamada Contribuinte de um banco de dados padrão SQL aberto e em condições ideais há o campo idContribuinte do tipo inteiro e chave primária. Há também o campo nomeContribuinte que é do tipo varchar. Nessa tabela, um Auditor Fiscal deseja alterar o nome do contribuinte de id 1 para 'Marcos Silva'. Para isso, terá que utilizar o comando:

a) ALTER TABLE Contribuinte SET nomeContribuinte = 'Marcos Silva' WHERE idContribuinte = 1
b) UPDATE Contribuinte SET nomeContribuinte = 'Marcos Silva' WHERE idContribuinte=1
c) UPDATE nomeContribuinte TO 'Marcos Silva' FROM Contribuinte WHERE idContribuinte=1

**Resposta: b) UPDATE Contribuinte SET nomeContribuinte = 'Marcos Silva' WHERE idContribuinte=1**

### Explicação

Essa questão testa a diferença entre comandos de **DDL** (Data Definition Language) e **DML** (Data Manipulation Language) em SQL.

- **a) `ALTER TABLE ... SET ...`** → Errado. `ALTER TABLE` é um comando de **DDL**, usado para alterar a **estrutura** da tabela (adicionar/remover/renomear colunas, mudar tipos, constraints etc.) — não serve para alterar o **valor** de um dado já existente numa linha. Além disso, `ALTER TABLE` não usa cláusula `SET ... WHERE`; essa sintaxe é do `UPDATE`.
- **b) `UPDATE Contribuinte SET nomeContribuinte = 'Marcos Silva' WHERE idContribuinte=1`** → Correto. `UPDATE` é o comando de **DML** padrão para alterar o **valor** de uma ou mais colunas em linhas existentes. A sintaxe é: `UPDATE <tabela> SET <coluna> = <novo_valor> WHERE <condição>`. O `WHERE idContribuinte=1` garante que só a linha do contribuinte de id 1 seja alterada.
- **c) `UPDATE nomeContribuinte TO 'Marcos Silva' FROM Contribuinte WHERE idContribuinte=1`** → Errado. Não é sintaxe SQL válida — o padrão SQL não usa `UPDATE <coluna> TO <valor> FROM <tabela>`; essa estrutura mistura elementos que não existem no comando `UPDATE`.

### Conceito-chave

| Comando | Categoria | Para que serve |
|---|---|---|
| `CREATE`, `ALTER`, `DROP` | DDL | Definir/alterar a **estrutura** (tabelas, colunas, constraints) |
| `INSERT`, `UPDATE`, `DELETE` | DML | Manipular os **dados/valores** dentro das tabelas |

Como o objetivo é alterar o **valor** de um campo em uma linha específica (e não a estrutura da tabela), o comando correto é `UPDATE`, tornando a alternativa **b** a correta.
