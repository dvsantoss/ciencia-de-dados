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
