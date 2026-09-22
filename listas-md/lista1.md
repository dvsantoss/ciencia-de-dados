UNIVERSIDADE FEDERAL DO RIO GRANDE DO NORTE

UNIDADE ACADÊMICA ESPECIALIZADA EM CIÊNCIAS AGRÁRIAS

CURSO DE ANÁLISE E DESENVOLVIMENTO DE SISTEMAS

COMPONENTE CURRICULAR: FUNDAMENTOS E TÉCNICAS EM CIÊNCIAS DE DADOS

Lista de Exercícios 1

1) Verdadeiro ou Falso? Como forma de permitir as buscas em documentos semi-estruturados,
um banco de dados NoSQL do tipo orientado a documentos armazena objetos indexados por

chaves utilizando tabelas de hash distribuídas (CESPE/TCU 2015).

2) Considere as seguintes características de um projeto de banco de dados (IBGE/2016).
I. O modelo de dados é conhecido a priori e é estável;

II. A integridade dos dados deve ser rigorosamente mantida;

III. Velocidade e escalabilidade são preponderantes.

Dessas características, o emprego de bancos de dados NoSQL é favorecido somente por:

a. III

b. II e III

c. I

d. II

e. I e II

3) Sobre os banco de dados NoSQL, assinale a afirmativa correta.
a) não podem ser indexados

b) são considerados bancos de dados relacionais

c) deve ser definido um esquema fixo antes de qualquer operação

d) são exemplos de NoSQL: mongoDB, Firebird, DynamoDB,  SQLite,  Access, Azure Table

4) Verdadeiro ou Falso? Com relação à forma como os dados são armazenados e manipulados no
desenvolvimento de aplicações, julgue os itens a seguir. Em um banco de dados NoSQL do tipo

grafo, cada arco é definido por um identificador único e expresso como um par chave/valor.

5) Seja os comandos SQL abaixo que criaram a estrutura de BD (tabelas) na figura. Considerando
que as mesmas podem estar numa mesma coleção, escreva o arquivo JSON para a

representação NoSQL de documentos desta estrutura. Se possível instalar o mongoDB compass

em sua máquina ou utilizar no cloud.mongodb (ATLAS), importe o arquivo JSON produzido.

![Figure](lista1.assets/page-1-img-0.png)

![Figure](lista1.assets/page-2-img-0.png)

6) Em uma tabela chamada Contribuinte de um banco de dados padrão SQL aberto e em
condições ideais há o campo idContribuinte do tipo inteiro e chave primária. Há também o

campo nomeContribuinte que é do tipo varchar. Nessa tabela, um Auditor Fiscal deseja alterar

o nome do contribuinte de id 1 para 'Marcos Silva'. Para isso, terá que utilizar o comando:

a) ALTER TABLE Contribuinte SET nomeContribuinte = ‘Marcos Silva’ WHERE idContribuinte = 1

b) UPDATE Contribuinte SET nomeContribuinte = ‘Marcos Silva’ WHERE idContribuinte=1

c) UPDATE nomeContribuinte TO ‘Marcos Silva’ FROM Contribuinte WHERE idContribuinte=1

7) O termo NoSQL refere-se
a) a uma abordagem teórica que segue o princípio de não utilização da linguagem SQL em

bancos de dados heterogêneos.

b) ao aumento da escalabilidade das bases de dados neles armazenados, aliado a um

desempenho mais satisfatório no seu acesso.

c) à facilidade de implementação de bases de dados normalizadas, com vistas a minimização de

redundâncias no conjunto de dados
