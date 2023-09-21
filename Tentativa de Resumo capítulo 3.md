# AULA 11/09 (3.1) 
## 3.1 - Introdução ao modelo relacional
Relação é a chave para o modelo relacional(por incrível que pareça), uma relação consiste em um esquema e uma instância de relação, em que a instância é a tabela e o esquema os cabeçalhos de coluna da tabela.
Esquema especifica:
- Nome da relação
- Nome de cada campo(coluna ou atributo)
- Domínio de cada campo
Instância:
- Tuplas/Registros do SGBD
- Pode ser considerada como uma tabela em que cada registro é uma linha dessa tabela e todas as linhas tem o mesmo tamanho.
Cada relação é definida como um conjunto de linhas únicas
![[Pasted image 20230911201725.png]]
A ordem da listagem não importa, mas tem uma convenção alternativa que é listar os campos em uma ordem específica e referir a um campo pela posição.
Nesse caso a posição tem significado, no SQL a primeira representação é mais usada em instrução de recuperação de tuplas (buscas) e a abaixo para inserção de tuplas.
![[Pasted image 20230911202414.png]]
Esquemas de relação especificam o domínio de cada campo ou coluna na instância
Restrições de domínio:
- Especificam a condição de restrição que cada instância deve ter, que cada elemento da coluna deve pertencer ao "tipo"(domínio), que foi designado na criação da instância, por exemplo, um nome não pode ser um inteiro. 
*Chaves denotam um conjunto, exemplo:
{Id-Aluno: 66666, nome: Tirucrops, login: tiru@mnz.com, idade: 19, média: 2.4}*
Instância de relação são aquelas que satisfazem as restrições do domínio.
- Grau(aridade) de uma relação é o numero de campos.
- Cardinalidade de uma instância é o número de tuplas que ela tem.
- No exemplo: Grau = 5, Cardinalidade = 6
### 3.1.1 COMANDOS BÁSICOS SQL
Em sql, tabela = relação.
DDL é o subconjunto que suporta criação, exclusão e modificação de tabelas.
Comandos básicos:
Criar tabela ex:
``` SQL
CREATE TABLE Filmes (nome CHAR(50),
				   nota REAL,
				   lançamento DATE,
				   duração_min INTEGER
				  )
```
Colocar uma tupla na tabela:  
```SQL
INSERT
INTO Filmes(nome,nota,lançamento,duração_min)
VALUES (Oppenheimer, 9,4, 20/08/2023, 180)
```
pode-se omitir into, mas é uma boa prática colocar ele.

Excluir tupla:
```SQL
DELETE
FROM Filmes F
WHERE A.nome = "Oppenheimer"
```
Modificar os valores de coluna em uma linha existente:
```SQL
UPDATE Filmes F
SET F.nota = F.nota - 0,6
WHERE F.nome = "Oppenheimer"
```
WHERE é aplicada primeiro e determina quais linhas devem ser modificadas, set determina como deve ser modificado.
outro exemplo:
```SQL
UPDATE Filmes F
SET F.nota = F.nota - 0,6
WHERE F.nota >= 8,8
```
# AULA 13/09 (até 3.4)
## 3.2 Restrições de integridade sobre relações 
- Restrição de integridade, "é uma forma de dizer se uma linha nova na tabela é válida ou não", em outras palavras, elas estão lá para certificar se uma instância é válida ou não, existindo diferentes tipos de restrições que podem ir desde o tipo do dado inserido em determinada coluna, até se o dado satisfaz as condições de chave, se for um valor que não pode ter aparecido anteriormente, por exemplo.  Importante frisar que somente instâncias válidas são inseridas em um SGBD(se ele foi bem feito).  As restrições:
		- São geradas na modelagem do sistema, em que o administrador ou usuário final define quais serão as restrições do esquema.
		- São verificadas no momento da execução de um app de BD em que o SGBD vai checar se existem violações e proíbe possíveis alterações que venham a infringir as restrições de integridade.
### 3.2.1 Restrição de chave
- Uma tupla não pode ser completamente igual a outra, portanto, para isso é preciso que um ou mais dos campos seja o identificador único da tupla (ex: matrícula dos alunos), esse campo é chamado de chave candidata.
	- "Duas tuplas distintas válidas" não podem ter a mesma chave candidata
	- Nenhum subconjunto do conjunto de campos em uma tuplas pode ser considerada uma chave. ex: Num contexto em que o um atributo *matrícula* é a chave enquanto *nome* é só um atributo o conjunto {*matrícula, nome*} não é uma chave candidata, e sim uma **superchave**, ou seja, um conjunto de campos que tem a chave candidata no meio.
- Chave primária, chave candidata que normalmente é usada para se referir a uma tupla no SGBD.
#### Restrição de chave em SQL

```SQL
CREATE TABLE Alunos( id CHAR(20),
					 nome CHAR(30),
					 login CHAR(20),
					 idade INTEGER,
					 media REAL,
					 UNIQUE (nome, idade),
					 CONSTRAINT Chave Alunos PRIMARY KEY (id)
				   )
```

### 3.2.2 Restrição de chave estrangeira
- Restrição mais comum entre duas relações.
- Eu tenho uma tabela "principal", nela eu tenho uma chave candidata, numa tabela "secundária" tem um campo que faz referência a tabela principal, ex: alunos e "alunos matriculados", na segunda eu tenho um campo de matrícula, pela restrição de chave estrangeira é preciso que essa matrícula esteja presente na tabela alunos. Também não pode excluir uma tupla somente em uma das tabelas, caso esteja nas duas.
#### Restrição de chave estrangeira em SQL
```SQL
CREATE TABLE Matriculado (id CHAR(20),
						 id_curso CHAR(20),
						 nota CHAR(10),
						 PRIMARY KEY (id,id_aluno),
						 FOREIGN KEY (id) REFERENCES Alunos.
						 )
```
### 3.2.3 Restrições Gerais
- As duas anteriores podem ser consideradas parte essencial do modelo, portanto recebem atenção especial nas modelagens.
- Existem algumas restrições mais gerais, Ex: Só podem ser cadastrados alunos com mais de 18 anos, ou se um aluno tem mais de 20 anos, precisa ter uma determinada nota mínima.
## 3.3 Verificação de restrições
- **PRIMARY KEY e UNIQUE**: se um comando causa uma violação de uma das restrições, ele é rejeitado.
- Potenciais violações são checadas no final de cada instrução SQL, mas podem ser checadas somente depois, para o final da transação que está executando a instrução.
- em relação as restrições de chave estrangeira pode-se dizer que:
	- Se tentar inserir uma tupla na tabela auxiliar com ID que não existe na principal, o comando será rejeitado.
	- Se uma linha é excluída na principal existem 4 opções do que pode acontecer:
		1.  Excluir as linhas da tabela auxiliar que referenciam essa linha da tabela principal.
		2.  Proibir a exclusão de linhas da principal que sejam referenciadas na tabela auxiliar.
		3. ![[Pasted image 20230915141719.png]]
		4. ![[Pasted image 20230915141730.png]]
SQL permite usar qualquer um dos 4 pra DELETE e UPDATE, a ação a ser tomada pode ser especificada na criação da tabela

```SQL
CREATE TABLE Matriculados (id CHAR(20),
						   id_curso CHAR(20),
						   nota CHAR(10),
						   PRIMARY KEY (id,id_curso),
						   FOREIGN KEY (id) REFERENCES Alunos
											ON DELETE CASCADE
											ON UPDATE NO ACTION)
```
No exemplo acima é instruído que quando a Chave em aluno for deletada, a chave em matriculados também deve ser. No update ele recorre a "NO ACTION", ou seja, nada deve ser feito e a ação deve ser rejeitada, quando "id" for alterado em Alunos, caso uma linha de Matriculados faça referência a esse "id". 
"CASCADE" diz que se uma coisa é feita em Alunos deve ser feita também em Matriculados, nesse caso é usado no DELETE mas também poderia ter sido feita no UPDATE.
### 3.1.1 Transações e restrições
- Por padrão, restrições são checadas no final de instruções SQL
- Existem casos em que essa verificação precisa ser adiada, no exemplo do livro (pág 60), não seria possível inserir a primeira tupla tanto para Aluno, quanto para curso, para isso se utiliza o modo DEFERRED no SQL, que só vai checar a condição depois da transação ser efetivada, por um momento o banco de dados pode ficar inconsistente, por exemplo se forem adicionados Alunos a Cursos que ainda não foram criados, mas serão adicionados em um outro comando SQL antes do *commit*. 
```SQL
SET CONSTRAINT nome-restrição DEFERRED 
```

## 3.4 Consultando bancos de dados relacionais
- Basicamente uma pergunta sobre os dados
- SELECT, FROM, WHERE 
ex:
```SQL
SELECT * 
FROM Filmes F
WHERE F.nota > 8,6
```
asterísco significa que vai manter no resultado todos os campos da tupla selecionada (vai retornar a tupla toda).
```SQL
SELECT L.NOME
FROM Emprestimo E, Leitores L
WHERE E.em_id = 20 AND E.le_id = L.le_id
```


# AULA 18/09 (3.5)
## 3.5 PROJETO LÓGICO DO BANCO DE DADOS DO ER PARA O RELACIONAL 
OBS: ER = Entidade Relacionamento , extremamente útil para representar o projeto no alto nível.

### 3.5.1 Entidades viram tabelas
- Os atributos da entidade viram as colunas da tabela.
- A entidade Bandas que tem como atributos nome, país e idade, e tem como chave o nome fica assim:  
```SQL
CREATE TABLE Bandas(nome CHAR(11),
				    país CHAR(30),
				    idade INTEGER,
				    PRIMARY KEY (cpf))
```
obs: nome não é uma boa chave identificadora pelo fato de duas bandas poderem ter o mesmo nome.

### 3.5.2 Relacionamentos sem restrições para tabelas
- Para representar um relacionamento deve-se identificar as entidades que participam do relacionamento e dar valores aos atributos que descrevem o relacionamento, sendo estes, os atributos de chave primária de cada conjunto de entidade participante e os atributos descritivos do conjunto de relacionamento.

 Dado o exemplo:
![[Pasted image 20230918180856.png]]
Inicialmente seriam criadas as 3 tabelas, Funcionários(cpf), Departamentos(id-depto) e Localidades (endereço), com seus respectivos atributos, depois seria criada uma quarta tabela para representar o relacionamento,, que tem como atributos as chaves primárias das outras 3 tabelas e outro atributo próprio "desde":
```SQL
CREATE TABLE Trabalha_em2(cpf CHAR(11),
						 id_depto INTEGER,
						 endereço CHAR(20),
						 desde DATE,
						 PRIMARY KEY (cpf, id-depto, endereço)
						 FOREIGN KEY (cpf) REFERENCES Funcionarios,
						 FOREIGN KEY(id-depto) REFERENCES Departamentos,
						 FOREIGN KEY(endereço) REFERENCES Localidades)
```
existe uma restrição **NOT NULL** implícita, que é aplicada aos atributos cpf, endereço e id-depto, uma vez que os 3 juntos formam a chave primária da tabela.
no caso do auto relacionamento em uma entidade:
![[Pasted image 20230918182303.png]]
```SQL
CREATE TABLE Reporta_a(cpf_supervisor CHAR(11),
					   cpf_subordinado CHAR(11),
					   PRIMARY KEY (cpf_supervisor, cpf_subordinado),
					   FOREIGN KEY (cpf_supervisor) REFERENCES Funcionarios(cpf),
					   FOREIGN KEY (cpf_subordinado) REFERENCES Funcionarios(cpf))
```
como o nome dos atributos são diferentes do da tabela funcionários é preciso referenciar explicitamente o atributo da tabela original relacionado.

### 3.5.3 Relacionamentos com restrição de chave
![[Pasted image 20230918183308.png]]
no caso a seguir a restrição de chave do exemplo é que o departamento só pode ter um funcionário gerenciando. uma das formas de fazer isso é assim como no exemplo anterior criar uma tabela auxiliar.
```SQL
CREATE TABLE Gerencia(cpf CHAR(11),
					  id-depto INTEGER, 
					  desde DATE
					  PRIMARY KEY (cpf, id-depto),
					  FOREIGN KEY(cpf) REFERENCES Funcionários,
					  FOREIGN KEY(id-depto) REFERENCES Departamentos)
```
para não ter que criar a tabela pode-se incluir as restrições na tabela da entidade que tem a chave

```SQL
CREATE TABLE Depto_gerencia (id-depto INTEGER,
							 nome-depto CHAR(20),
							 orçamento REAL,
							 cpf CHAR(11),
							 desde DATE,
							 PRIMARY KEY (id-depto),
							 FOREIGN KEY (cpf) REFERENCES Funcionários)
```
- A desvantagem é que caso alguns departamentos não tenham gerentes teremos vários espaços null nas tabelas. Mas ainda é vantajoso caso se tenha mais de uma relação evitando ter de buscar em mais tabelas, o que geraria uma ineficiência.
### 3.5.4 Relacionamentos com restrição de participação
dado o exemplo:
![[Pasted image 20230919145907.png]]
pela restrição de participação todo departamento precisa de um gerente e pela restrição de chave cada departamento só pode ter um gerente, ficando assim no sql:
```SQL
CREATE TABLE Depto_gerencia(id-depto INTEGER,
						    nome-depto CHAR(20),
						    orçamento REAL,
						    PRIMARY KEY (id-depto),
						    FOREIGN KEY (cpf) REFERENCES Funcionários,
								    ON DELETE NO ACTION)

```
- a última linha diz respeito ao fato de que um gerente só pode ser excluído após o seu departamento ter a gerencia assumida por outro Funcionário. 
### 3.5.5 Relacionamentos com entidades fracas
![[Pasted image 20230919151439.png]]
uma estratégia seria criar uma tabela auxiliar que englobaria tanto apólice quanto dependente, assim como feito no exemplo de depto_gerencia,  que ficaria
```SQL
CREATE TABLE Apólice_dependente(nomed CHAR(20),
							    idade INTEGER,
							    custo REAL,
							    cpf CHAR(11),
							    PRIMARY KEY (cpf, nomed),
							    FOREIGN KEY (cpf) REFERENCES Funcionários
												  ON DELETE CASCADE)
```
o uso do CASCADE é para caso a tupla de um funcionário seja excluída, as de seus respectivos dependentes também sejam.
### 3.5.6 Subclasses no SQL 
![[Pasted image 20230919152903.png]]
duas opções :
- criar uma tabela para cada uma das entidades, com funcionários horistas e contratados referenciando a tabela de Funcionários.
```SQL
CREATE TABLE Funcion_Horistas(cpf CHAR(11),
							  salario_hora REAL,
							  H=horas_trabalhadas INTEGER
							  PRIMARY KEY (cpf),
							  FOREIGN KEY (cpf) REFERENCES Funcionários
												ON DELETE CASCADE)

CREATE TABLE Funcion_Contratados(cpf CHAR(11),
								 id-contrato INTEGER,
								 PRIMARY KEY (cpf),
								 FOREIGN KEY (cpf), REFERENCES Funcionários
													ON DELETE CASCADE)
```
- criar uma tabela para funcionários horistas e outra para funcionários contratados, sem tabela "geral" Funcionários
```SQL
CREATE TABLE Funcion_Horistas(cpf CHAR(11),
							  nome CHAR(20),
							  vaga CHAR(30),
							  salario_hora REAL,
							  horas_trabalhadas INTEGER,
							  PRIMARY KEY(cpf))

CREATE TABLE Funcion_Contratados(cpf CHAR(11),
								 nome CHAR(20),
								 vaga CHAR(30),
								 id-contrato INTEGER,
								 PRIMARY KEY (cpf))
```

o problema desse caso é se tiver um tipo de funcionário que tenha uma modalidade de trabalho diferente, tendo em vista que não existe um local certo para ele. No primeiro caso ele apenas não seria inserido nas tabelas das subclasses. 

 ***obs: códigos podem estar errados***


# AULA 20/09 (3.6 e 3.7)
## 3.6 VISÕES (view)
 - Tabela cujas linhas não são armazenadas explicitamente no banco de dados.
 - no exemplo a seguir, se cria uma visão que contém todos os alunos que tiraram B em algum dos cursos que fazem
 
```SQL
CREATE VIEW Estudantes-B(nome, id-aluno, curso)
	   AS SELECT A.nome , A.id-aluno, M.id-curso
	   FROM Alunos A, Matriculados M
	   WHERE A.id-aluno = M.id-aluno AND M.nota = "B" 
```
- essas visões podem ser usadas como tabelas bases, para novas consultas.

### 3.6.1 Visões, independências de dados, segurança
- visões podem ser úteis para manter a segurança dos dados armazenados, tendo em vista que em muitos casos não é desejável que o usuário tenha acesso a todas as informações de uma tabela.
- se um esquema de uma relação em um banco de dados é alterada mas não se quer atingir o esquema externo, basta que seja criada uma visão para o esquema antigo.
### 3.6.2 Atualizações em visões
- Em resumo, visões como o nome já indica devem servir, em maioria, para que o usuário final consiga visualizar os dados. No entanto visões não podem ser atualizadas.
- Essa proibição vem do fato de que atualizar uma visão pode trazer consequências indesejadas para as tabelas base.
- Em SQL, apenas uma classe restrita de visões pode ser atualizada.

## 3.7 Destruindo tabelas e visões
- DROP TABLE, destrói a tabela, se usar **CASCADE** o comando destrói também todas as visões ou restrições de integridade que fazem referencia a tabela. Se usar **RESTRICT**, só destruirá a tabela caso não tenha outras referências em visões ou restrição de integridade.
- ALTER TABLE, é usado para fazer alterações na tabela, como criar ou destruir colunas e restrições de integridade.
