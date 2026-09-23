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

| Operador | Significado | Exemplo |
|---|---|---|
| `$eq` / `$ne` | igual / diferente | `{ mediaGeral: { $ne: 7 } }` |
| `$gt` / `$gte` | maior / maior ou igual | `{ mediaGeral: { $gt: 9 } }` |
| `$lt` / `$lte` | menor / menor ou igual | `{ mediaGeral: { $lt: 5 } }` |
| `$in` / `$nin` | valor está / não está na lista | `{ mediaGeral: { $in: [7, 10] } }` |

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
