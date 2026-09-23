# Respostas – Lista de Exercícios 2

## Revisão: comandos NoSQL usados na lista

Revisão rápida dos comandos que aparecem na lista (principalmente o shell do MongoDB, usado na questão 2). Os exemplos seguem a sugestão da lista: banco `escola` com a coleção `alunos` (campos `nome`, `mediaGeral`, `avaliacao`).

### Banco e coleção

```js
use escola                     // seleciona o banco (cria-o ao inserir o 1º documento)
show dbs                       // lista os bancos
show collections               // lista as coleções do banco atual
db.createCollection("alunos")  // opcional: a coleção também é criada no 1º insert
```

### Inserir (Create)

```js
db.alunos.insertOne({ nome: "João", mediaGeral: 7, avaliacao: "Na média" })

db.alunos.insertMany([
  { nome: "Maria", mediaGeral: 9.5, avaliacao: "" },
  { nome: "Pedro", mediaGeral: 10,  avaliacao: "" },
  { nome: "Ana",   mediaGeral: 5,   avaliacao: "" }
])
```

- `insertOne` → insere **um** documento; `insertMany` → recebe uma **lista** de documentos.
- Se não for informado, o MongoDB cria o `_id` (ObjectId) automaticamente.

### Consultar (Read)

```js
db.alunos.find({})                        // filtro vazio = retorna TODOS os documentos
db.alunos.find()                          // mesmo efeito
db.alunos.find({ nome: "João" })          // igualdade
db.alunos.findOne({ nome: "João" })       // só o primeiro que casar
db.alunos.find({}, { nome: 1, _id: 0 })   // projeção: mostra só o nome
db.alunos.find().sort({ mediaGeral: -1 }).limit(3)  // ordena (−1 = decrescente) e limita
db.alunos.countDocuments({ mediaGeral: { $gte: 7 } })
```

> ⚠️ `find({})` **não** retorna lista vazia: o filtro `{}` significa "sem restrição", então vêm todos os documentos.

### Operadores de comparação

| Operador | Em inglês | Significado | Exemplo |
|---|---|---|---|
| `$eq` / `$ne` | *equal* / *not equal* | igual / diferente | `{ mediaGeral: { $ne: 7 } }` |
| `$gt` / `$gte` | *greater than* / *greater than or equal* | maior / maior ou igual | `{ mediaGeral: { $gt: 9 } }` |
| `$lt` / `$lte` | *less than* / *less than or equal* | menor / menor ou igual | `{ mediaGeral: { $lt: 5 } }` |
| `$in` / `$nin` | *in* / *not in* | valor está / não está na lista | `{ mediaGeral: { $in: [7, 10] } }` |

### Operadores lógicos

```js
// $or: pelo menos uma condição verdadeira
db.alunos.find({ $or: [ { mediaGeral: 7 }, { mediaGeral: 10 } ] })

// $and: todas verdadeiras (vírgula no mesmo filtro já é um AND implícito)
db.alunos.find({ mediaGeral: { $gte: 7, $lte: 9 } })
```

> ⚠️ `$in` x `$or`: `{ mediaGeral: { $in: [7, 10] } }` é equivalente ao `$or` acima. Ele busca quem tem média **igual a 7 ou igual a 10** (não é um intervalo de 7 a 10). Para **o mesmo campo**, `$in` é a forma mais simples e recomendada. `$or` é necessário quando as condições envolvem **campos diferentes**.

### Atualizar (Update)

```js
// atualiza o 1º documento que casar com o filtro
db.alunos.updateOne({ nome: "João" }, { $set: { mediaGeral: 8 } })

// atualiza TODOS os documentos que casarem com o filtro
db.alunos.updateMany(
  { mediaGeral: { $gt: 9 } },
  { $set: { avaliacao: "Ótimo desempenho!" } }
)
```

- Sintaxe: `update...(filtro, operação)`.
- `$set` → define/altera campo; `$unset` → remove campo; `$inc` → incrementa valor numérico.
- Sem um operador como `$set`, `updateOne`/`updateMany` dão erro. Para trocar o documento inteiro existe o `replaceOne`.

### Remover (Delete)

```js
db.alunos.deleteOne({ nome: "Ana" })
db.alunos.deleteMany({ mediaGeral: { $lt: 5 } })
db.alunos.deleteMany({})   // cuidado: apaga todos os documentos da coleção
```

### Resumo CRUD: SQL x MongoDB

| Operação | SQL | MongoDB |
|---|---|---|
| Inserir | `INSERT INTO alunos ...` | `db.alunos.insertOne({...})` |
| Consultar | `SELECT * FROM alunos WHERE mediaGeral > 9` | `db.alunos.find({ mediaGeral: { $gt: 9 } })` |
| Atualizar | `UPDATE alunos SET avaliacao = '...' WHERE mediaGeral > 9` | `db.alunos.updateMany({ mediaGeral: { $gt: 9 } }, { $set: { avaliacao: "..." } })` |
| Remover | `DELETE FROM alunos WHERE ...` | `db.alunos.deleteMany({...})` |

### MongoDB no Python (pymongo)

```python
from pymongo import MongoClient

cliente = MongoClient("mongodb://localhost:27017/")
alunos = cliente["escola"]["alunos"]

alunos.insert_one({"nome": "João", "mediaGeral": 7})   # dicionário Python vira documento BSON
for a in alunos.find({"mediaGeral": {"$gt": 9}}):
    print(a)
```

- Os métodos seguem o padrão *snake_case*: `insert_one`, `find`, `update_many`, `delete_one`...
- O **dicionário Python** é o tipo natural para representar um documento no pymongo.

### Outros bancos citados

- **Neo4j (grafo) – Cypher:** linguagem de consulta do Neo4j, **não** do MongoDB.
  ```cypher
  CREATE (a:Aluno {nome: "João"})-[:CURSA]->(d:Disciplina {nome: "Ciência de Dados"})
  MATCH (a:Aluno)-[:CURSA]->(d:Disciplina) RETURN a.nome, d.nome
  ```
- **Redis (chave-valor):** `SET chave valor`, `GET chave`, `DEL chave`.
- **HBase (colunar):** os dados são buscados pela **RowKey**, ex.: `get 'alunos', 'row1', 'info'` (tabela, RowKey, família de colunas).

---

## Questão 1

**Enunciado:** Com relação aos tipos de bancos NoSQL, analise as afirmativas a seguir:

I. Bancos de dados de documentos armazenam dados como documentos (JSON, XML, etc.). Um exemplo de banco deste tipo é o MongoDB.
II. O banco de dados Neo4J é de um tipo de banco que possuem vértices e arestas representando as relações entre esses vértices.
III. Banco de dados colunares guardam colunas juntas, ao invés de linhas, sendo o tipo de banco do Neo4J.

Estão corretas as afirmativas:

a) I, III
b) I, II
c) II, III
d) I, II, III

**Resposta: b) I, II**

### Explicação

- **I** → Correta. Bancos **orientados a documentos** armazenam os dados como documentos semiestruturados (JSON, BSON, XML etc.), e cada documento pode ter uma estrutura própria. O **MongoDB** é o exemplo mais conhecido (usa BSON, um JSON binário).
- **II** → Correta. O **Neo4j** é um banco **orientado a grafos**: as entidades são **vértices (nós)** e os relacionamentos entre elas são **arestas (arcos)**, ambos podendo ter propriedades.
- **III** → Errada. A primeira parte está certa: bancos **colunares** guardam os dados por coluna (ou família de colunas) em vez de por linha. O erro está em dizer que o Neo4j é desse tipo. Ele é de **grafo**, como a própria afirmativa II diz. Exemplos de bancos colunares são **Cassandra** e **HBase**.

Como só I e II estão corretas, a alternativa certa é a **b**.

### Conceito-chave

| Tipo NoSQL | Como organiza os dados | Exemplos |
|---|---|---|
| Chave-valor | Pares `chave → valor` | Redis, DynamoDB |
| Documentos | Documentos JSON/BSON/XML | MongoDB, CouchDB |
| Colunar | Colunas / famílias de colunas | Cassandra, HBase |
| Grafo | Vértices (nós) e arestas (relacionamentos) | Neo4j |

## Questão 2

**Enunciado:** Considerando os comandos disponíveis no shell do MongoDB e uma coleção de dados alunos não vazia, com atributos nome, mediaGeral e avaliacao, analise as seguintes afirmativas:

I. O seguinte comando retorna uma lista vazia, uma vez que os critérios de busca não foram definidos:

```js
db.alunos.find( {} )
```

II. No comando abaixo, o programador colocou erroneamente o $in no lugar do $or para encontrar alunos baseado nos valores de sua nota média geral:

```js
db.alunos.find( { mediaGeral: { $in: [ 7, 10 ] } } )
```

III. Para atualizar na coleção alunos o atributo avaliação de todos os alunos com média maior que 9, podemos executar o seguinte comando:

```js
db.alunos.updateMany( { mediaGeral: { $gt: 9 } }, { $set: { avaliacao: "Ótimo desempenho!" } } )
```

IV. Para inserir um novo registro na coleção "alunos", podemos executar o seguinte comando:

```js
db.alunos.insertOne( { nome: "João", mediaGeral: 7, avaliacao: "Na média" } )
```

Estão corretas as afirmativas:

a) I e II
b) II e III
c) III e IV
d) I e IV

**Resposta: c) III e IV**

### Explicação

- **I** → Errada. O filtro vazio `{}` significa "nenhuma restrição", então o `find({})` retorna **todos** os documentos da coleção. Como a coleção não está vazia, o resultado também não será vazio. É o equivalente a um `SELECT * FROM alunos` sem `WHERE`.
- **II** → Errada. Não há erro no uso do `$in`. O comando busca alunos com `mediaGeral` **igual a 7 ou igual a 10**, o que dá o mesmo resultado de:
  ```js
  db.alunos.find({ $or: [ { mediaGeral: 7 }, { mediaGeral: 10 } ] })
  ```
  Quando as alternativas são valores de **um mesmo campo**, a própria documentação do MongoDB recomenda usar `$in` em vez de `$or`, porque é mais simples e mais eficiente. O `$or` é necessário quando as condições envolvem **campos diferentes**.
- **III** → Correta. `updateMany` atualiza **todos** os documentos que casam com o filtro. O filtro `{ mediaGeral: { $gt: 9 } }` (*greater than*) seleciona quem tem média maior que 9, e o `$set` define o campo `avaliacao` com o novo valor. Se o `updateOne` fosse usado, só o primeiro aluno encontrado seria atualizado.
- **IV** → Correta. `insertOne` insere **um** documento na coleção. O `_id` é gerado automaticamente pelo MongoDB.

Como só III e IV estão corretas, a alternativa certa é a **c**.

> 💡 **Ao testar no mongosh:** o PDF da lista usa aspas "curvas" (“ ”), vindas do editor de texto. Ao copiar os comandos para o shell, troque-as por aspas retas (`"`), senão dá erro de sintaxe.

## Questão 3

**Enunciado:** Classifique as bibliotecas abaixo em função de sua maior aplicabilidade/direcionamento, conforme classes abaixo:

a) visualização, plotagem
b) computação científica
c) aquisição, tratamento e análise/consulta de dados
d) aprendizagem de máquina
e) big data

**Resposta:**

(**c**) Pandas
(**b**) SciPy
(**e**) Spark
(**d**) pyTorch
(**b**) NumPy
(**d**) scikit-Learn
(**e**) Hive
(**a**) matplotlib
(**a**) seaborn
(**c**) pymongo
(**d**) tensorFlow
(**c**) Selenium

### Explicação

| Biblioteca | Classe | Por quê |
|---|---|---|
| **Pandas** | c) aquisição, tratamento e análise | Lê dados de CSV, Excel, SQL, JSON etc. e usa *DataFrames* para limpar, filtrar, agrupar e analisar |
| **SciPy** | b) computação científica | Rotinas de otimização, integração, álgebra linear, estatística e processamento de sinais, construídas sobre o NumPy |
| **Spark** | e) big data | Processamento distribuído **em memória** de grandes volumes de dados em clusters (Apache Spark / PySpark) |
| **pyTorch** | d) aprendizagem de máquina | *Framework* de *deep learning* (redes neurais) criado pelo Facebook/Meta |
| **NumPy** | b) computação científica | *Arrays* multidimensionais eficientes e operações matemáticas vetorizadas. É a base do Pandas, SciPy e scikit-learn |
| **scikit-Learn** | d) aprendizagem de máquina | Algoritmos clássicos de ML: classificação, regressão, clusterização, validação de modelos |
| **Hive** | e) big data | *Data warehouse* do ecossistema **Hadoop**, que permite consultar dados do HDFS com uma linguagem parecida com SQL (HiveQL) |
| **matplotlib** | a) visualização | Biblioteca base de gráficos em Python (linhas, barras, dispersão, histogramas) |
| **seaborn** | a) visualização | Construída sobre o matplotlib, voltada a gráficos estatísticos mais prontos e bonitos |
| **pymongo** | c) aquisição e consulta | *Driver* oficial para acessar o **MongoDB** pelo Python: inserir, consultar e atualizar documentos |
| **tensorFlow** | d) aprendizagem de máquina | *Framework* de *deep learning* criado pelo Google |
| **Selenium** | c) aquisição de dados | Automatiza um navegador. Em ciência de dados é usado para ***web scraping***, coletando dados de páginas web (inclusive as dinâmicas, com JavaScript) |

### Resumo por classe

- **a) Visualização:** matplotlib, seaborn
- **b) Computação científica:** NumPy, SciPy
- **c) Aquisição, tratamento e análise/consulta:** Pandas, pymongo, Selenium
- **d) Aprendizagem de máquina:** scikit-Learn, pyTorch, tensorFlow
- **e) Big data:** Spark, Hive

## Questão 4

**Enunciado:** Entre as funcionalidades do NumPy, podemos destacar:

a) tratamento flexível para dados ausentes
b) melhor performance em seus arrays do que os tipos primitivos de Python
c) facilita agregação de dados
d) é um concorrente do Pandas

**Resposta: b) melhor performance em seus arrays do que os tipos primitivos de Python**

### Explicação

- **a)** → Errada. O tratamento flexível de dados ausentes é característica do **Pandas**, com `isna()`, `fillna()`, `dropna()` etc. No NumPy, um `NaN` só existe em arrays de ponto flutuante e "contamina" os cálculos (ex.: `np.sum` de um array com `NaN` resulta em `NaN`).
- **b)** → Correta. O `ndarray` do NumPy guarda elementos de **um único tipo** em um bloco **contíguo de memória** e executa as operações em código C compilado, de forma **vetorizada** (sem laço em Python). Por isso é muito mais rápido e ocupa menos memória que uma `list` do Python, que guarda referências para objetos espalhados na memória e pode misturar tipos.
- **c)** → Errada. Agregação de dados (agrupar e resumir, como `groupby().sum()` ou `pivot_table`) é um ponto forte do **Pandas**. O NumPy até tem funções como `sum` e `mean`, mas não oferece agrupamento por categorias.
- **d)** → Errada. NumPy e Pandas são **complementares**. O Pandas é construído **sobre** o NumPy: cada coluna de um `DataFrame` é, por baixo, um array do NumPy.

### Exemplo

```python
import numpy as np

lista = [1, 2, 3, 4]
arr = np.array([1, 2, 3, 4])

[x * 2 for x in lista]   # lista Python: precisa de laço → [2, 4, 6, 8]
arr * 2                  # NumPy: operação vetorizada → array([2, 4, 6, 8])
```

## Questão 5

**Enunciado:** Qual dos itens a seguir NÃO é característica do ambiente Jupyter:

a) permite execução iterativa por meio do IPython
b) é baseado no modelo de desenvolvimento edição-compilação-execução
c) permite integrar equações em LaTex e tags HTML em células Markdown
d) O Google Colab é baseado em Jupyter

**Resposta: b) é baseado no modelo de desenvolvimento edição-compilação-execução**

### Explicação

A questão pede o que **NÃO** é característica do Jupyter.

- **a)** → É característica. O Jupyter nasceu do projeto **IPython** (*Interactive Python*), e o IPython é o *kernel* que executa o código Python nos notebooks. A execução é **interativa e incremental**: cada célula roda separadamente, o resultado aparece logo abaixo e as variáveis ficam na memória para as próximas células.
- **b)** → **NÃO é característica** (resposta). O ciclo **editar → compilar → executar** é típico de linguagens compiladas como C e Java: escreve-se o programa inteiro, compila-se e só então ele é executado. O Jupyter segue o modelo **REPL** (*Read-Eval-Print Loop*: ler, avaliar, mostrar o resultado e repetir), sem etapa de compilação separada. É isso que o torna bom para exploração de dados.
- **c)** → É característica. Células **Markdown** aceitam texto formatado, equações em **LaTeX** (ex.: `$y = ax + b$`) e **tags HTML**, permitindo misturar código, resultados e documentação num mesmo notebook.
- **d)** → É característica. O **Google Colab** é um ambiente de notebooks Jupyter hospedado na nuvem do Google (com GPU/TPU gratuitas) e usa o mesmo formato de arquivo `.ipynb`.

### Conceito-chave

| Modelo | Como funciona | Exemplo |
|---|---|---|
| Edição-compilação-execução | Escreve o programa inteiro → compila → executa | C, Java |
| Interativo (REPL) | Executa trecho a trecho e vê o resultado na hora | Jupyter, IPython, terminal do Python |

## Questão 6

**Enunciado:** Sobre arquivos de imagem enquanto fontes de dados, podemos afirmar que:

a) são dados semi-estruturados, de esquema flexível
b) dado ser um dado binário, embora ineficiente, pode ser salvo como um tipo binData no mongodb
c) são dados não estruturados, com um esquema rígido
d) em nenhuma hipótese necessitam ser transformados em dados estruturados para análise, após o PDI

**Resposta: b) dado ser um dado binário, embora ineficiente, pode ser salvo como um tipo binData no mongodb**

### Explicação

- **a)** → Errada. Imagens são dados **não estruturados**. Dados **semiestruturados** são os que têm marcações que descrevem a própria estrutura, como JSON, XML e HTML.
- **b)** → Correta. Uma imagem é um arquivo **binário**, e o BSON (formato interno do MongoDB) tem o tipo **`BinData`** (*binary data*) para guardar bytes brutos dentro de um documento. É possível, mas **ineficiente**: o documento fica pesado, cada consulta carrega a imagem inteira e há o limite de **16 MB por documento** (acima disso é preciso usar o **GridFS**). Na prática, costuma-se guardar a imagem em disco ou num serviço de armazenamento de arquivos e salvar no banco só o **caminho/URL** e os metadados.
- **c)** → Errada. Imagens são, sim, **não estruturadas**, mas a segunda parte está errada: dados não estruturados **não têm esquema** (nem rígido, nem flexível). Esquema rígido é característica de dados **estruturados**, como as tabelas de um banco relacional.
- **d)** → Errada. Depois do **PDI** (Processamento Digital de Imagens), normalmente é **preciso** transformar a imagem em dados estruturados para analisá-la: extrair **características** (*features*) como cor média, bordas, contagem de objetos ou rótulos de classificação, organizando-as numa tabela ou vetor numérico que os modelos conseguem usar.

### Conceito-chave

| Tipo de dado | Esquema | Exemplos |
|---|---|---|
| Estruturado | Rígido, definido antes (tabelas) | Tabelas SQL, planilhas |
| Semiestruturado | Flexível, descrito pelo próprio dado | JSON, XML, HTML, documentos MongoDB |
| Não estruturado | Sem esquema | Imagens, áudio, vídeo, texto livre |

## Questão 7

**Enunciado:** Quais são os 3Vs mais importantes do Big Data?

a) volume, velocidade, viabilidade
b) volume, velocidade, variedade
c) valor, velocidade, viabilidade
d) velocidade, variedade, valor

**Resposta: b) volume, velocidade, variedade**

### Explicação

Os **3Vs** clássicos do Big Data foram propostos por Doug Laney (analista do META Group, hoje Gartner) em 2001 e são as três características que tornam os dados difíceis de tratar com as ferramentas tradicionais:

- **Volume** → a **quantidade** enorme de dados gerada e armazenada (terabytes, petabytes...).
- **Velocidade** → a **rapidez** com que os dados são gerados e precisam ser processados, muitas vezes em tempo real (ex.: sensores, redes sociais, transações).
- **Variedade** → a diversidade de **tipos e formatos**: dados estruturados (tabelas), semiestruturados (JSON, XML) e não estruturados (imagens, vídeos, texto).

Com o tempo, outros Vs foram acrescentados, como **Veracidade** (confiabilidade e qualidade dos dados) e **Valor** (utilidade dos dados para o negócio), formando os "5Vs". Mas os três **originais e mais importantes** são volume, velocidade e variedade.

Por que as outras estão erradas:

- **a)** e **c)** → **Viabilidade** não faz parte dos Vs clássicos.
- **d)** → Troca **volume**, o V mais básico do Big Data, por **valor**, que é um dos Vs acrescentados depois.

## Questão 8

**Enunciado:** Em um banco de dados o que é missing data, usualmente preenchido com NaN ao importar dados para um dataframe em Pandas?

a) o mesmo que outlier
b) dados faltantes, toda e qualquer falha na obtenção de respostas sobre os elementos selecionados e designados para pertencerem à amostra
c) ocorre quando não há falta de informação no banco de dados
d) são dados antigos e ultrapassados

**Resposta: b) dados faltantes, toda e qualquer falha na obtenção de respostas sobre os elementos selecionados e designados para pertencerem à amostra**

### Explicação

*Missing data* (dados faltantes ou ausentes) é quando um elemento da amostra **deveria** ter um valor num campo, mas esse valor não foi obtido. Por exemplo: a pessoa não respondeu uma pergunta do questionário, um sensor falhou ou houve erro na digitação ou importação. Ao carregar os dados no Pandas, essas lacunas aparecem como **`NaN`** (*Not a Number*).

- **a)** → Errada. **Outlier** é um valor que **existe**, mas é muito diferente dos demais (ex.: uma idade de 150 anos). No *missing data*, o valor **não existe**.
- **b)** → Correta. É exatamente a definição: qualquer falha em obter a informação de um elemento que faz parte da amostra.
- **c)** → Errada. É o contrário: *missing data* ocorre justamente quando **há** falta de informação.
- **d)** → Errada. Dados antigos ou desatualizados têm valor, só que ultrapassado. Isso é um problema de **atualidade** dos dados, não de ausência.

### Tratamento no Pandas

```python
import pandas as pd

df = pd.read_csv("alunos.csv")

df.isna().sum()                       # quantos valores faltam em cada coluna
df.dropna()                           # remove as linhas com algum NaN
df["mediaGeral"].fillna(df["mediaGeral"].mean())  # preenche com a média da coluna
```

A escolha entre **remover** e **preencher** (imputar) depende de quantos dados faltam e de por que faltam: remover muitas linhas pode enviesar a análise.

## Questão 9

**Enunciado:** Qual seria uma boa definição para cientista de dados:

a) alguém com competências em programação e estatística, sem necessariamente conhecimento do negócio
b) alguém que embora conheça o negócio muito bem e programe tão bem quanto, não conhece técnicas estatísticas nem interpreta minimamente os resultados
c) alguém que equilibra a seleção de técnicas de programação e técnicas estatísticas, aplicadas a um negócio com o qual procura interagir com especialistas para melhor proposta de solução
d) alguém que domina os bancos de dados NoSQL

**Resposta: c)**

### Explicação

O cientista de dados fica na interseção de **três áreas**: **programação/computação**, **estatística/matemática** e **conhecimento do negócio (domínio)**. É o famoso diagrama de Venn de Drew Conway.

- **a)** → Errada. Sem conhecimento do negócio, a pessoa não sabe quais perguntas fazer nem como interpretar os resultados no contexto real.
- **b)** → Errada. Sem estatística, não há como escolher técnicas adequadas nem avaliar se os resultados são confiáveis.
- **c)** → Correta. Equilibra programação e estatística aplicadas a um negócio, e **interage com especialistas** do domínio para chegar à melhor solução.
- **d)** → Errada. Dominar NoSQL é só uma habilidade técnica entre várias; não define a profissão.

## Questão 10

**Enunciado:** Sobre conceitos gerais da área de ciências de dados, marque V ou F. Quando for F, justifique:

**Resposta:**

| # | Afirmativa | V/F |
|---|---|---|
| 1 | Aprendizagem de máquina (ML) e mineração de dados (DM) podem ser consideradas a mesma coisa no âmbito de ciência de dados | **F** |
| 2 | Técnicas como clusterização, detecção de anomalias e classificação são normalmente associadas ao resultado de técnicas de ML | **V** |
| 3 | Uma nuvem de palavras é uma técnica gráfica simples de realizar análise exploratória de dados | **V** |
| 4 | A ciência de dados não é apenas ML e DM, sendo estas parte da etapa de transformação no ciclo de vida do dado | **F** |
| 5 | O termo ETL (Extração, Transformação e Carga) está presente na etapa de produção do ciclo do dado, sendo processo comum em data warehouses | **F** |
| 6 | Dashboard é uma técnica de visualização de resultados | **V** |
| 7 | Estão entre os motores para o desenvolvimento da ciência de dados: IaaS, PaaS e SaaS | **V** |
| 8 | O MongoDB Atlas é um exemplo de DBaaS no contexto cloud computing | **V** |
| 9 | O sistema de arquivos distribuído HDFS é a base para a plataforma Hadoop e para o Google File System | **F** |

### Explicação

O **ciclo de vida do dado** visto em aula tem as etapas: **Produção → Armazenamento → Transformação → Análise → Descarte**.

1. **F** → ML e DM são áreas **relacionadas, mas diferentes**. Pelos slides: **ML** é o *projeto e avaliação de algoritmos para extração de padrões a partir de dados*; **DM** é a *análise de dados estruturados, usualmente com ênfase comercial*. A mineração de dados **usa** algoritmos de ML, mas não é a mesma coisa.
2. **V** → Clusterização (agrupar clientes parecidos), detecção de anomalias (fraudes, *outliers*) e classificação (dizer a qual classe um dado pertence) são tarefas típicas resolvidas com técnicas de ML.
3. **V** → A nuvem de palavras mostra as palavras mais frequentes de um texto em tamanho maior. É uma forma simples e visual de explorar dados textuais.
4. **F** → A primeira parte está certa (ciência de dados abrange todo o ciclo, não só ML e DM), mas ML e DM fazem parte da etapa de **Análise**, não da de Transformação. A Análise vai *de uma simples consulta SQL até modelos de classificação com redes neurais*.
5. **F** → O ETL fica na etapa de **Transformação**, que converte o dado do modelo de armazenamento para um modelo próprio para consumo/análise (ex.: carga em *data warehouses*). A etapa de **Produção** é a geração do dado: texto digitado, sensores, fotos, cliques, GPS etc.
6. **V** → *Dashboards* (painéis) reúnem gráficos e indicadores para apresentar os resultados de forma visual.
7. **V** → Nos slides, os motores da ciência de dados incluem **virtualização**, **sistemas distribuídos** e **computação em nuvem (IaaS, PaaS, SaaS)**, que deram acesso barato a infraestrutura de armazenamento e processamento.
8. **V** → O **MongoDB Atlas** é o MongoDB oferecido como serviço gerenciado na nuvem: **DBaaS** (*Database as a Service*). O provedor cuida da instalação, *backup* e escalabilidade.
9. **F** → A ordem está invertida: o **Google File System (GFS)**, de 2003, é que serviu de **inspiração** para o **HDFS**. O HDFS é a base do Hadoop, mas não do GFS.

## Questão 11

**Enunciado:** Para indexação de páginas web, o Google usa o MapReduce. Sobre a ação de Map, pode-se afirmar que:

a) agrega os resultados, gerando um resultado final
b) é executada em nós distribuídos, contabilizando e separando itens comuns
c) é executado em memória, tal como o Spark
d) não pode ser implementado em mongoDB

**Resposta: b) é executada em nós distribuídos, contabilizando e separando itens comuns**

### Explicação

O MapReduce divide o processamento em duas fases:

- **Map** → cada nó do *cluster* processa **sua parte** dos dados em paralelo e gera pares `<chave, valor>`. Na contagem de palavras, por exemplo, cada ocorrência vira `<palavra, 1>`. Depois esses pares são **agrupados por chave** (fase *shuffle*).
- **Reduce** → recebe os pares agrupados e **agrega** os valores de cada chave, gerando o resultado final (ex.: `<Dad, 2>`).

- **a)** → Errada. Agregar e gerar o resultado final é papel do **Reduce**.
- **b)** → Correta. O Map roda de forma distribuída nos nós, contabilizando e separando os itens por chave.
- **c)** → Errada. O MapReduce do Hadoop grava os resultados intermediários em **disco**. Processar **em memória** é justamente a vantagem do **Spark** sobre o MapReduce.
- **d)** → Errada. O MongoDB tem o comando `mapReduce` (hoje considerado obsoleto e substituído pelo *aggregation pipeline*, mas existe).

## Questão 12

**Enunciado:** Tenho um problema com variável resposta numérica e variável independente numérica. Qual técnica não se aplica para a construção de um modelo de predição?

a) regressão linear
b) rede neural
c) random forest
d) regressão logística

**Resposta: d) regressão logística**

### Explicação

A **regressão logística** é usada quando a variável **resposta é categórica** (ex.: sim/não, spam/não spam, doente/saudável). Ela calcula a **probabilidade** de o dado pertencer a uma classe, por isso não serve para prever um valor numérico.

As demais se aplicam a resposta numérica com entrada numérica:

- **Regressão linear** → a técnica clássica para esse caso (ex.: preço em função da quantidade vendida).
- **Rede neural** e **Random Forest** → técnicas gerais, que funcionam tanto para classificação quanto para regressão.

| Resposta (Y) \ Entrada (X) | Numérica | Categórica |
|---|---|---|
| **Numérica** | Regressão linear, árvore, random forest, rede neural | ANOVA, árvore, random forest, rede neural |
| **Categórica** | **Regressão logística**, árvore, random forest, rede neural | **Regressão logística**, árvore, random forest, rede neural |

## Questão 13

**Enunciado:** Uma análise de variância seria aplicável à variáveis de entrada do tipo:

a) categóricas
b) numéricas
c) mistas
d) n.d.a

**Resposta: a) categóricas**

### Explicação

A **ANOVA** (*Analysis of Variance*) é um teste de hipóteses que compara as **médias de uma variável numérica entre grupos**. Os grupos são definidos por uma variável de entrada **categórica**. Exemplo dos slides: *gastos no cartão de crédito (numérica) em função do gênero (categórica)*.

- **b)** → Errada. Com entrada numérica e resposta numérica, usa-se **regressão linear**.
- **c)** → Errada. Com entradas **mistas** (categóricas e numéricas), usa-se a **ANCOVA** (*Analysis of Covariance*). Exemplo: salário em função de faixa etária, gênero e anos de empresa.

## Questão 14

**Enunciado:** São variáveis quantitativas discretas (pode haver mais de uma opção):

a) números de filho de um casal
b) altura de uma pessoa
c) número de clientes num banco
d) número de poltronas num cinema

**Resposta: a), c) e d)**

### Explicação

- **Quantitativa discreta** → resulta de uma **contagem**, com valores inteiros que se pode enumerar (0, 1, 2, 3...). Não existe "2,5 filhos".
- **Quantitativa contínua** → resulta de uma **medição** e pode assumir qualquer valor num intervalo (1,72 m; 1,725 m...).

- **a)** número de filhos → **discreta** (contagem).
- **b)** altura → **contínua** (medição), por isso não entra.
- **c)** número de clientes → **discreta** (contagem).
- **d)** número de poltronas → **discreta** (contagem).

## Questão 15

**Enunciado:** Na equação Y = ax + b, Y pode ser chamada de:

a) variável objetivo, variável instrumental, variável resposta, variável dependente
b) variável discordiana, variável pergunta, variável dependente
c) variável objetivo, variável target, variável resposta e variável dependente

**Resposta: c) variável objetivo, variável target, variável resposta e variável dependente**

### Explicação

Em `Y = ax + b`, o **Y** é o que se quer prever. Ele **depende** do valor de `x`. Os nomes usados para ele são:

- **Y** → variável **resposta**, **dependente**, **objetivo** ou **target**.
- **x** → variável **independente**, **de entrada**, **explicativa**, **preditora** ou **atributo**.

- **a)** → Errada. **Variável instrumental** é um conceito da econometria, usado para lidar com certos problemas de variáveis explicativas. Não é sinônimo de variável resposta.
- **b)** → Errada. "Variável discordiana" e "variável pergunta" não são termos usados.

## Questão 16

**Enunciado:** São variáveis qualitativas ordinais:

a) classe social
b) voltagem elétrica
c) cor dos olhos
d) time de futebol

**Resposta: a) classe social**

### Explicação

- **Qualitativa ordinal** → categorias que têm uma **ordem natural** (ex.: pequeno < médio < grande).
- **Qualitativa nominal** → categorias **sem ordem** entre si.

- **a)** classe social (A, B, C, D, E; ou baixa, média, alta) → **ordinal**, porque existe uma ordem entre as categorias.
- **b)** voltagem elétrica → **quantitativa** (é um número medido), não qualitativa.
- **c)** cor dos olhos → **nominal**: azul não é "maior" que castanho.
- **d)** time de futebol → **nominal**: não há ordem natural entre os times.

## Questão 17

**Enunciado:** Sobre a etapa de modelagem, um modelo pode ser construído vários objetivos, menos o de:

a) ordenação
b) estimativa
c) previsão
d) decisão

**Resposta: a) ordenação**

### Explicação

Um modelo é *uma especificação de relação matemática (ou probabilística) entre variáveis diferentes*: a partir das variáveis de entrada, ele produz uma resposta. Os exemplos dos slides mostram os objetivos possíveis:

- **Estimativa** → probabilidade de ganhar; probabilidade de um *banner* ser clicado.
- **Previsão** → lucro nos próximos anos; que time ganhará.
- **Decisão** → a mensagem é spam ou não? A transação é fraudulenta? Qual o impacto da ação A ou B no processo X?

**Ordenação** (colocar dados em ordem) é uma operação simples de manipulação de dados, feita com um `ORDER BY` ou um `sort()`. Não exige construir um modelo.

## Questão 18

**Enunciado:** Sobre modelos de armazenamento, julgue os itens a seguir em V e F. Se for F, justifique:

**Resposta:**

| # | Afirmativa | V/F |
|---|---|---|
| 1 | O modelo baseado em grafos atual remete ao modelo hierárquico dos anos 60 | **F** |
| 2 | O modelo 'não apenas SQL' (NoSQL) restringe o modelo relacional por não permitir relacionamentos | **F** |
| 3 | O modelo chave-valor é baseado em tabelas hash e um de seus bancos é o Redis | **V** |
| 4 | O modelo colunar Tall-narrow (TN) codifica o Timestamp binário no ID e possui poucas linhas e muitas colunas | **F** |
| 5 | O DynamoDB é um exemplo famoso de banco baseado em grafos | **F** |
| 6 | Os modelos baseados em grafos são interessantes e bem aplicáveis em semântica web | **V** |
| 7 | Uma família de colunas no HBase pode ser extraída a partir de um RowKey | **V** |
| 8 | A linguagem de consulta cypher é a base para extrações no mongoDB | **F** |
| 9 | Um TimeStamp é um valor de 64 bits no mongoDB e permite registrar os milissegundos desde 01.01.1970 até o instante da persistência do dado | **V** |
| 10 | Um dicionário Python é um tipo admissível para persistência direta no mongoDB | **V** |
| 11 | Um dicionário em Python pode ser transmitido sem transformação através do MQTT | **F** |
| 12 | O MongoDB possui o limite de 100 níveis de documentos aninhados, com 16MB por documento | **V** |
| 13 | Para documentos > 16 MB, uma solução é usar o GridFS do mongoDB, que divide o documento em coleções | **V** |

### Explicação

1. **F** → O modelo de grafos remete ao **modelo em rede (CODASYL)**, também pré-relacional dos anos 60. Nele os registros eram ligados por **links**, e os slides associam justamente esse modelo à ideia de grafo. O **modelo hierárquico** é uma **árvore** (cada filho tem um único pai), o que é mais restrito que um grafo.
   > ⚠️ Os slides colocam hierárquico e rede juntos como "pré-relacionais (anos 60)". Se o professor considerar os dois como um mesmo grupo, ele pode aceitar V. Vale confirmar.
2. **F** → NoSQL significa ***Not Only SQL*** ("não apenas SQL"). Ele não "restringe" o modelo relacional: é uma **alternativa** a ele. E os bancos NoSQL podem, sim, representar relacionamentos: os bancos de **grafos** são especializados nisso, e o MongoDB permite referências entre documentos.
3. **V** → O modelo chave-valor funciona como uma **grande tabela hash**: cada chave aponta para um valor. O **Redis** é um dos exemplos mais conhecidos (outros: DynamoDB, Couchbase).
4. **F** → É o contrário. O modelo **Tall-narrow** (alto e estreito) tem **muitas linhas e poucas colunas**: cada registro vira uma linha, e o *timestamp* é embutido na **RowKey** (ex.: `userID + timestamp`). O modelo com **poucas linhas e muitas colunas** é o **Flat-wide** (achatado e largo).
5. **F** → O **DynamoDB** (Amazon) é um banco **chave-valor** (também orientado a documentos). Um exemplo famoso de banco de grafos é o **Neo4j**.
6. **V** → Nos grafos, nós e arestas têm atributos e os relacionamentos têm direção. Isso representa bem as **ligações semânticas** entre conceitos, como na Web Semântica (RDF, ontologias).
7. **V** → No HBase, o acesso aos dados é feito pela **RowKey**. A partir dela é possível obter uma família de colunas específica, ex.: `get 'tabela', 'rowkey', 'familia'`.
8. **F** → **Cypher** é a linguagem de consulta do **Neo4j** (grafos). O MongoDB usa a sua própria **MQL** (*MongoDB Query Language*), com comandos como `find`, `updateMany` e `aggregate`.
9. **V** → É a definição dos slides: *Timestamp: milissegundos desde 1.Jan.1970 (Unix Epoch) – 64 bits*.
   > 💡 Tecnicamente, no BSON quem guarda milissegundos desde 1970 em 64 bits é o tipo **`Date`**. O tipo **`Timestamp`** também tem 64 bits, mas guarda **segundos** (32 bits) + um contador (32 bits), para uso interno do MongoDB. Para a prova, vale a definição do slide.
10. **V** → Com o **pymongo**, um dicionário Python pode ser passado direto para `insert_one()`. O *driver* converte o dicionário para BSON automaticamente.
11. **F** → O MQTT transmite a mensagem (*payload*) como **bytes/texto**. O dicionário precisa ser **serializado** antes, normalmente em **JSON** com `json.dumps()`. Do outro lado ele é convertido de volta com `json.loads()`. Os slides dizem: *mensagens podem ser encapsuladas em estrutura JSON (serialização) para transmissão*.
12. **V** → Conforme os slides e a documentação oficial: tamanho máximo de **16 MB por documento** e até **100 níveis** de documentos aninhados.
13. **V** → O **GridFS** é a solução do MongoDB para arquivos maiores que 16 MB.
    > 💡 Mais precisamente, o GridFS divide o arquivo em **pedaços** (*chunks*, de 255 kB por padrão) e usa **duas coleções**: `fs.files` (metadados do arquivo) e `fs.chunks` (os pedaços). Se o professor for rigoroso com o "divide o documento em coleções", a justificativa é essa: divide em *chunks*, guardados em coleções.
