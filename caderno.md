# Ciência de Dados -> Anotações de Aula

 Reorganizado por tema a partir de anotações soltas.
 Termos ambíguos no original foram mantidos entre parênteses junto da interpretação mais provável.
 Itens marcados com ⚠️ foram indicados como prováveis conteúdos de prova.

## Tipos de Dados e Ferramentas Básicas

- **Dados estruturados, semiestruturados e não estruturados**
  (nota original: "dados tratados e semi e não pentágono ou pentaho" — provavelmente mistura dois tópicos: a classificação estruturados/semiestruturados/não estruturados, e o nome da ferramenta **Pentaho**, escrito com dúvida na grafia ["pentágono ou pentaho"]. Vale confirmar no slide da aula.)
- **Pentaho** — ferramenta de ETL/BI (mencionada na mesma anotação acima)
- Dado primário x dado secundário
  - Primário: coletado diretamente pelo pesquisador para o próprio estudo
  - Secundário: dado já existente, coletado por terceiros e reaproveitado
- Validação de dados
- Agregação em banco de dados
  - Agregação é uma operação feita "em cima" da base (processa/resume os dados sem alterar a base original)
- **Polars** — biblioteca Python para manipulação de DataFrames (alternativa ao pandas, focada em performance)
- DataFrame
- Google Colab

---

## Engenharia de Dados, IA Generativa e NLP

### Requisitos e Processos
- Requisitos funcionais e não funcionais
- Algoritmo **Apriori** — usado para mapear associações entre dados (regras de associação, ex.: análise de cesta de compras)
- Base transacional → processo com início, meio e fim
- **Data Mart** — subconjunto do Data Warehouse focado em uma área/departamento específico

### IA Generativa ⚠️ CAI NA PROVA
- **Few-shot, One-shot e Zero-shot Learning** no contexto de GenAI — anotado explicitamente como conteúdo de prova
  - **Zero-shot**: o modelo responde sem nenhum exemplo prévio
  - **One-shot**: o modelo recebe apenas um exemplo antes de responder
  - **Few-shot**: o modelo recebe poucos exemplos antes de responder
- Usar o exemplo do Josenalde no slide (revisar esse slide específico)

### Aprendizado de Máquina
- Tipos de aprendizado: **Supervisionado, Não supervisionado, Semi-supervisionado** e **Reforço** (o tipo que estava faltando na lista original)
- **Ground Truth** — conjunto de dados "verdade" usado como referência para validar modelos
- Rotuladores de imagens no mundo (correção de "rotula dores" → "rotuladores"; rotulação manual/crowdsourcing de dados)
- Curadoria de dados

### Processamento de Texto / NLP
- Calcular a frequência de palavras
- Biblioteca para ler sites jornalísticos (nota original: "biblioteca Article" — possivelmente **newspaper3k** ou similar; confirmar nome exato com o professor/slide)
- Usar em conjunto com **WordCloud**
- **StopWords**
- **Máscara** (imagem) — usada para dar formato a uma WordCloud

### Data Apps
- **POC** (Proof of Concept) — validação para o cliente
- MongoDB: não é tabela, é **coleção**
- **Streamlit** (correção de "StreamLIT")
- **Gradio**
- Pesquisar mais sobre Data App (lembrete pessoal — pendente)
- O resultado final vira um **Data App**
- **Cloud x On-premise** (correção de "Claud x on-promife")

---

## Bancos de Dados: SQL x MongoDB

- **Hadoop** sem HDFS (correção de "Hadook sem hdfs")
- Retrieve (uma das operações de CRUD)
- Atividade: comparar bancos de dados
- Puxar o banco e puxar a coleção (comandos no MongoDB)
- `insertOne` → biblioteca **pymongo** (correção de "pymonac") → mongo shell → tudo é feito em Python (estudar melhor isso)
- **SQL x MongoDB** (correção de "Sql vs Mong")
- ⚠️ **Comando de agregação vai cair na prova** — estudar bem!
- Variável numérica x variável categórica
- Como ver quais são as categorias de uma variável categórica? **R: `.unique()`**
