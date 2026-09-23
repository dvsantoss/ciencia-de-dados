# Respostas – Lista de Exercícios 2

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
