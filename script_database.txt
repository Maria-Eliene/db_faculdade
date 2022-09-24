CREATE DATABASE IF NOT EXISTS db_teste_faculdade;

USE db_teste_faculdade;

CREATE TABLE departamento (
	cod_departamento INT NOT NULL PRIMARY KEY AUTO_INCREMENT,
    	nome_departamento VARCHAR (30) 
);


CREATE TABLE tipo_telefone (
	cod_tipo_telefone INT NOT NULL PRIMARY KEY AUTO_INCREMENT, 
    	tipo_telefone VARCHAR (15)
);


CREATE TABLE tipo_logradouro (
	cod_tipo_logradouro INT PRIMARY KEY NOT NULL AUTO_INCREMENT,
    	tipo_logradouro VARCHAR (15)
);


CREATE TABLE professor (
	cod_professor INT NOT NULL PRIMARY KEY AUTO_INCREMENT ,
    	nome_professor VARCHAR (25) NOT NULL, 
    	sobrenome_professor VARCHAR (30) NOT NULL,
    	fk_cod_departamento INT NOT NULL,
    	status_professor BOOLEAN,
CONSTRAINT professor_ibfk_1 FOREIGN KEY (fk_cod_departamento) REFERENCES departamento (cod_departamento)
);


CREATE TABLE disciplina (
	cod_disciplina INT NOT NULL PRIMARY KEY AUTO_INCREMENT, 
    	nome_disciplina VARCHAR (20),
    	num_alunos_disciplina INT NOT NULL, 
    	descricao TEXT (100),
    	carga_horaria INT,
	fk_cod_departamento INT NOT NULL,
CONSTRAINT disciplina_ibfk_1 FOREIGN KEY (fk_cod_departamento) REFERENCES departamento (cod_departamento)
);


CREATE TABLE curso(
	cod_curso INT NOT NULL PRIMARY KEY AUTO_INCREMENT,
    	nome_curso VARCHAR (30),
    	fk_cod_departamento INT NOT NULL,
CONSTRAINT curso_ibfk_1 FOREIGN KEY (fk_cod_departamento) REFERENCES departamento (cod_departamento)
);


CREATE TABLE turma (
	cod_turma INT NOT NULL PRIMARY KEY AUTO_INCREMENT, 
    	periodo VARCHAR (15),
    	num_alunos INT,
    	dt_inicio DATE, 
    	dt_fim DATE,
    	fk_cod_curso INT NOT NULL,
CONSTRAINT turma_ibfk_1 FOREIGN KEY (fk_cod_curso) REFERENCES curso (cod_curso) 
);


CREATE TABLE endereco (
	cod_endereco INT PRIMARY KEY NOT NULL AUTO_INCREMENT,
    	endereco  VARCHAR (50),
    	numero int,
    	complemento VARCHAR (20),
    	cep VARCHAR (8),
    	fk_cod_tipo_logradouro INT NOT NULL,
CONSTRAINT endereco_ibfk_1 FOREIGN KEY (fk_cod_tipo_logradouro) REFERENCES tipo_logradouro (cod_tipo_logradouro)
);


CREATE TABLE professor_disciplina (
	fk_cod_professor INT NOT NULL,
    	fk_cod_disciplina INT NOT NULL, 
CONSTRAINT professor_disciplina_ibfk_1 FOREIGN KEY (fk_cod_professor) REFERENCES professor (cod_professor),
CONSTRAINT professor_disciplina_ibfk_2 FOREIGN KEY (fk_cod_disciplina) REFERENCES disciplina (cod_disciplina) 
);


CREATE TABLE curso_disciplina (
	fk_cod_curso INT NOT NULL,
    	fk_cod_disciplina INT NOT NULL, 
CONSTRAINT curso_disciplina_ibfk_1 FOREIGN KEY (fk_cod_curso) REFERENCES curso (cod_curso),
CONSTRAINT curso_disciplina_ibfk_2 FOREIGN KEY (fk_cod_disciplina) REFERENCES disciplina (cod_disciplina)	
);


CREATE TABLE aluno (
	RA INT NOT NULL PRIMARY KEY AUTO_INCREMENT,
    	nome_aluno VARCHAR (25) NOT NULL,
    	sobrenome_aluno VARCHAR (30) NOT NULL,
    	cpf VARCHAR (11) NOT NULL,
    	sexo CHAR (1) NOT NULL, 
    	mae VARCHAR (50) NOT NULL,
    	Pai VARCHAR (50) NOT NULL,
    	email VARCHAR (45) NOT NULL,
    	telefone VARCHAR (10) NOT NULL,
    	status_aluno boolean NOT NULL,
    	fk_cod_curso INT NOT NULL, 
	fk_cod_turma INT NOT NULL, 
    	fk_cod_endereco INT NOT NULL, 
CONSTRAINT aluno_ibfk_1 FOREIGN KEY (fk_cod_curso) REFERENCES curso (cod_curso),
CONSTRAINT aluno_ibfk_2 FOREIGN KEY (fk_cod_turma) REFERENCES turma (cod_turma),
CONSTRAINT aluno_ibfk_3 FOREIGN KEY (fk_cod_endereco) REFERENCES endereco (cod_endereco)
);


CREATE TABLE historico(
	cod_historico INT NOT NULL PRIMARY KEY AUTO_INCREMENT,
    	dt_inicio DATE, 
    	dt_fim DATE, 
    	fk_RA  INT NOT NULL,
CONSTRAINT historico_ibfk_1 FOREIGN KEY (fk_RA) REFERENCES aluno (RA) 
);


CREATE TABLE telefone (
	cod_telefone INT NOT NULL PRIMARY KEY AUTO_INCREMENT,
    	num_telefone INT NOT NULL,
    	fk_cod_tipo_telefone INT NOT NULL, 
CONSTRAINT telefone_ibfk_1 FOREIGN KEY (fk_cod_tipo_telefone) REFERENCES tipo_telefone (cod_tipo_telefone)
);


CREATE TABLE telefone_aluno (
	cod_tel_aluno INT NOT NULL PRIMARY KEY AUTO_INCREMENT,
    	fk_cod_telefone INT NOT NULL, 
    	fk_RA INT NOT NULL,
CONSTRAINT telefone_aluno_ibfk_1 FOREIGN KEY (fk_cod_telefone) REFERENCES telefone (cod_telefone),    
CONSTRAINT telefone_aluno_ibfk_2 FOREIGN KEY (fk_RA) REFERENCES aluno (RA) 
);


CREATE TABLE disciplina_historico (
	nota FLOAT (4.2), 
    	frequencia INT, 
    	fk_cod_disciplina INT NOT NULL,
    	fk_cod_historico INT NOT NULL, 
CONSTRAINT disciplina_historico_ibfk_1 FOREIGN KEY (fk_cod_disciplina) REFERENCES disciplina (cod_disciplina),
CONSTRAINT disciplina_historico_ibfk_2 FOREIGN KEY (fk_cod_historico) REFERENCES historico (cod_historico) 
);


CREATE TABLE aluno_disciplina (
	fk_cod_disciplina INT NOT NULL,
    	fk_RA INT NOT NULL,
CONSTRAINT aluno_disciplina_ibfk_2 FOREIGN KEY (fk_cod_disciplina) REFERENCES disciplina (cod_disciplina) ,
CONSTRAINT aluno_disciplina_ibfk_1 FOREIGN KEY (fk_RA) REFERENCES aluno (RA)
);

-- Views para manter a integridade das tabelas 

CREATE VIEW vw_aluno_curso_disciplina AS
SELECT 	a.RA, CONCAT(a.nome_aluno,' ' , a.sobrenome_aluno) Aluno,  
		c.cod_curso, c.nome_curso Curso,
        d.nome_disciplina Disciplina
FROM aluno a
JOIN curso c 
	ON c.cod_curso = a.fk_cod_curso
JOIN disciplina d 
	ON c.cod_curso = d.cod_disciplina
ORDER BY RA;


CREATE VIEW vw_professor_departamento AS
SELECT 	p.cod_professor, CONCAT(p.nome_professor,' ' , p.sobrenome_professor) Professor ,
		d.cod_departamento, d.nome_departamento 
FROM professor p 
JOIN departamento d 
	ON p.cod_professor = d.cod_departamento;


DELIMITER $$
CREATE FUNCTION fn_remove_acento (texto CHAR (200))
RETURNS VARCHAR (200)
READS SQL DATA 
BEGIN 
	SET texto = REPLACE (texto, 'á', 'a'), texto = REPLACE (texto, 'Á', 'A'),
		texto = REPLACE (texto, 'à', 'a'), texto = REPLACE (texto, 'À', 'A'),
		texto = REPLACE (texto, 'ã', 'a'), texto = REPLACE (texto, 'Ã', 'A'),
		texto = REPLACE (texto, 'â', 'a'), texto = REPLACE (texto, 'À', 'A'),
		texto = REPLACE (texto, 'ä', 'a'), texto = REPLACE (texto, 'Ä', 'E'),
		texto = REPLACE (texto, 'é', 'e'), texto = REPLACE (texto, 'É', 'E'),
		texto = REPLACE (texto, 'è', 'e'), texto = REPLACE (texto, 'È', 'E'), 
		texto = REPLACE (texto, 'ê', 'e'), texto = REPLACE (texto, 'Ê', 'E'), 
		texto = REPLACE (texto, 'í', 'i'), texto = REPLACE (texto, 'Í', 'I'), 
		texto = REPLACE (texto, 'ì', 'i'), texto = REPLACE (texto, 'Ì', 'I'),
		texto = REPLACE (texto, 'î', 'i'), texto = REPLACE (texto, 'Î', 'I'), 
		texto = REPLACE (texto, 'ó', 'o'), texto = REPLACE (texto, 'Ó', 'O'), 
		texto = REPLACE (texto, 'ò', 'o'), texto = REPLACE (texto, 'Ò', 'O'), 
		texto = REPLACE (texto, 'õ', 'o'), texto = REPLACE (texto, 'Õ', 'O'), 
		texto = REPLACE (texto, 'ô', 'o'), texto = REPLACE (texto, 'Ô', 'O'), 
		texto = REPLACE (texto, 'ú', 'u'), texto = REPLACE (texto, 'Ú', 'U'), 
		texto = REPLACE (texto, 'ù', 'u'), texto = REPLACE (texto, 'Ù', 'U'), 
		texto = REPLACE (texto, 'û', 'u'), texto = REPLACE (texto, 'Û', 'U'),
		texto = REPLACE (texto, '´', ' '); 
RETURN texto;    
END $$

DELIMITER ;

SELECT fn_remove_acento ('sorrir é bom demais');


DELIMITER \\ 
CREATE FUNCTION fn_maiuscula (texto VARCHAR(200))
RETURNS VARCHAR(200)
READS SQL DATA 
BEGIN
	SET 	texto = REPLACE(texto, 'a', 'A'), texto = REPLACE(texto, 'b', 'B'), texto = REPLACE(texto, 'c', 'C'),
		texto = REPLACE(texto, 'd', 'D'), texto = REPLACE(texto, 'e', 'E'), texto = REPLACE(texto, 'f', 'F'),
		texto = REPLACE(texto, 'g', 'G'), texto = REPLACE(texto, 'h', 'H'), texto = REPLACE(texto, 'i', 'I'),
		texto = REPLACE(texto, 'j', 'J'), texto = REPLACE(texto, 'k', 'K'), texto = REPLACE(texto, 'l', 'L'),
		texto = REPLACE(texto, 'm', 'M'), texto = REPLACE(texto, 'n', 'N'), texto = REPLACE(texto, 'o', 'O'),
		texto = REPLACE(texto, 'n', 'N'), texto = REPLACE(texto, 'o', 'O'), texto = REPLACE(texto, 'p', 'P'),
		texto = REPLACE(texto, 'q', 'Q'), texto = REPLACE(texto, 'r', 'R'),texto = REPLACE(texto, 's', 'S'), 
		texto = REPLACE(texto, 't', 'T'),texto = REPLACE(texto, 'u', 'U'), texto = REPLACE(texto, 'v', 'V'),
		texto = REPLACE(texto, 'w', 'W'), texto = REPLACE(texto, 'x', 'X'), texto = REPLACE(texto, 'y', 'Y'),
		texto = REPLACE(texto, 'z', 'Z'), texto = REPLACE(texto, 'ç', 'Ç');
RETURN texto;
END \\ 
DELIMITER ;


-- carga de dados / INSERTs no banco com procedures 

DELIMITER //
CREATE PROCEDURE sp_insert_departamento (n_departamento VARCHAR(30))
BEGIN 
    INSERT INTO departamento (nome_departamento)
    VALUES (fn_maiuscula(n_departamento));
END//
DELIMITER ;

CALL sp_insert_departamento ('Artes');
CALL sp_insert_departamento ('Arquitetura');
CALL sp_insert_departamento('Direito');
CALL sp_insert_departamento('Física');
CALL sp_insert_departamento('Química'); 
CALL sp_insert_departamento('Geografia');
CALL sp_insert_departamento('Engenharia'); 
CALL sp_insert_departamento('Educação Física');
CALL sp_insert_departamento('Matematica');
CALL sp_insert_departamento('Biologia');
CALL sp_insert_departamento('Filosofia');
CALL sp_insert_departamento ('Informatica');
CALL sp_insert_departamento ('Geografia');
CALL sp_insert_departamento ('Literatura');
CALL sp_insert_departamento ('Nutrição');


DELIMITER \\
CREATE PROCEDURE sp_insert_tipo_telefone (tel_tipo_telefone VARCHAR (15))
BEGIN
    INSERT tipo_telefone (tipo_telefone)
    VALUES (fn_maiuscula(tel_tipo_telefone));
END \\
DELIMITER ;

CALL sp_insert_tipo_telefone ('Residencial');
CALL sp_insert_tipo_telefone ('Comercial');
CALL sp_insert_tipo_telefone('celular');
CALL sp_insert_tipo_telefone('Whatsapp');


DELIMITER \\
CREATE PROCEDURE sp_insert_tipo_logradouro (l_tipo_logradouro VARCHAR (15))
BEGIN
    INSERT tipo_logradouro (tipo_logradouro)
    VALUES (fn_maiuscula(l_tipo_logradouro));
END \\

DELIMITER ;

CALL sp_insert_tipo_logradouro ('Avenida');
CALL sp_insert_tipo_logradouro ('Alamenda'); 
CALL sp_insert_tipo_logradouro ('Rua');
CALL sp_insert_tipo_logradouro ('Chácara');
CALL sp_insert_tipo_logradouro ('Praça');
CALL sp_insert_tipo_logradouro ('Quadra');


DELIMITER $$
CREATE PROCEDURE sp_insert_professor (p_nome_professor VARCHAR (25), p_sobrenome_professor VARCHAR (30), 
				      p_fk_cod_departamento INT, p_status_professor INT)
BEGIN
    INSERT professor (nome_professor, sobrenome_professor, fk_cod_departamento, status_professor)
    VALUES (fn_maiuscula(p_nome_professor),fn_maiuscula(p_sobrenome_professor), p_fk_cod_departamento, p_status_professor);
END $$ 

DELIMITER ;

CALL sp_insert_professor ('Marcela','Faria','1','1'); 
CALL sp_insert_professor ('Marcos','Fernandes','2','1'); 
CALL sp_insert_professor ('Joana','Silva','3','1');
CALL sp_insert_professor ('Marilia','Mendes','4','0'); 
CALL sp_insert_professor ('Jorge','Freire','5','1'); 
CALL sp_insert_professor ('Carla','Mascarenhas','6','1'); 
CALL sp_insert_professor ('Pedro','Alves','7','1'); 
CALL sp_insert_professor ('Thiago','costa','8','1');
CALL sp_insert_professor ('Ana','Gomes','9','1'); 
CALL sp_insert_professor ('Paulo','Souza','10','0'); 
CALL sp_insert_professor('Rafaela','Morais','11','1');


DELIMITER $$
CREATE PROCEDURE sp_insert_disciplina (d_nome_disciplina VARCHAR (25), d_num_alunos_disciplina INT, 
				       d_descricao TEXT, d_carga_horaria INT, d_fk_cod_departamento INT)
BEGIN
    INSERT disciplina (nome_disciplina, num_alunos_disciplina, descricao, carga_horaria, fk_cod_departamento)
    VALUES (fn_maiuscula(d_nome_disciplina) ,d_num_alunos_disciplina, fn_maiuscula(d_descricao), d_carga_horaria, d_fk_cod_departamento);

END $$ 

DELIMITER ;

CALL sp_insert_disciplina ('Estatística','20',' ','36','9');
CALL sp_insert_disciplina ('Gramática','20',' ','28','4');
CALL sp_insert_disciplina ('Ciêcias','36',' ','24','10');
CALL sp_insert_disciplina ('Geometria','15',' ','42','9');
CALL sp_insert_disciplina ('Fisica quântica','34',' ','30','4');
CALL sp_insert_disciplina ('Álgebra linear','20',' ','24','4');
CALL sp_insert_disciplina ('Artes cênicas','28',' ','24','1');
CALL sp_insert_disciplina ('Estrutura de dados','14',' ','24','9');
CALL sp_insert_disciplina ('Filosofia','26',' ','28','11');
CALL sp_insert_disciplina ('Genética','15',' ','30','10');
CALL sp_insert_disciplina ('Sociologia','25',' ','32','6');



DELIMITER $$
CREATE PROCEDURE sp_insert_curso (n_curso VARCHAR (30), c_fk_cod_departamento INT)
BEGIN 	
    INSERT INTO curso (nome_curso, fk_cod_departamento) VALUES (fn_maiuscula(n_curso), c_fk_cod_departamento);
END $$
DELIMITER ;

CALL sp_insert_curso ('Biologia','10');
CALL sp_insert_curso ('Artes cênicas','1');
CALL sp_insert_curso ('Arquitetura e urbanismo','2');
CALL sp_insert_curso ('Direito','3');
CALL sp_insert_curso ('Física','4');	
CALL sp_insert_curso ('Química','5'); 
CALL sp_insert_curso ('Geografia','6');
CALL sp_insert_curso ('Engenharia civil','7');
CALL sp_insert_curso ('Engenharia ambiental','7');
CALL sp_insert_curso ('Engenharia elétrica','8');
CALL sp_insert_curso ('Matemática','9');
CALL sp_insert_curso ('Ciências biológicas','10');
CALL sp_insert_curso ('Nutrição','15');
CALL sp_insert_curso ('Ciências da computação','12');
CALL sp_insert_curso ('Filosofia','11');


DELIMITER \\
CREATE PROCEDURE sp_insert_turma (t_periodo VARCHAR (15), t_num_alunos INT, t_dt_inicio DATE, t_dt_fim DATE, t_fk_cod_curso INT)
BEGIN
    INSERT INTO turma (periodo, num_alunos, dt_inicio, dt_fim, fk_cod_curso)
    VALUES (fn_maiuscula(t_periodo), t_num_alunos, t_dt_inicio, t_dt_fim, t_fk_cod_curso);
END \\

DELIMITER ;
    
CALL sp_insert_turma ('Noturno','25','2022-06-15','2022-12-20','1');
CALL sp_insert_turma ('Matutino','25','2022-06-15','2022-12-20','1');
CALL sp_insert_turma ('Noturno','25','2022-06-15','2022-12-20','2');
CALL sp_insert_turma ('Vespertino','25','2022-06-15','2022-12-20','3');
CALL sp_insert_turma ('Noturno','25','2022-02-15','2022-06-20','4');
CALL sp_insert_turma ('Matutuino','25','2022-06-15','2022-12-20','5');
CALL sp_insert_turma ('Noturno','25','2022-02-15','2022-06-20','6');
CALL sp_insert_turma ('Matutino','25','2022-01-15','2022-06-20','7');
CALL sp_insert_turma ('Noturno','25','2022-06-15','2022-12-20','8');
CALL sp_insert_turma ('Vespertino','25','2022-06-15','2022-12-20','9');
CALL sp_insert_turma ('Noturno','25','2022-02-18','2022-07-20','10');
CALL sp_insert_turma ('Matutino','25','2022-06-15','2022-12-20','11');
        

DELIMITER \\
CREATE PROCEDURE sp_insert_endereco (e_endereco VARCHAR(50), e_numero INT, e_complemento VARCHAR(20), e_cep VARCHAR (20), e_fk_cod_tipo_logradouro INT)
BEGIN 	
    INSERT INTO endereco (endereco, numero, complemento, cep, fk_cod_tipo_logradouro) 
    VALUES (fn_maiuscula(e_endereco), e_numero, fn_maiuscula(e_complemento), fn_maiuscula(e_cep), e_fk_cod_tipo_logradouro);
END \\
DELIMITER ;

CALL sp_insert_endereco ('QNJ 09','6','próximo alameda','72140090','6');
CALL sp_insert_endereco ('QNJ 8','15','sem complemento','72140090','6');
CALL sp_insert_endereco ('Quadra 240 nº','11','3º Avenida','74256544','6'); 
CALL sp_insert_endereco ('Av. Castelo Branco nº','2','Santa Inês','75836481','1');
CALL sp_insert_endereco ('Rua São joão','45','Palmares','22462864','3');
CALL sp_insert_endereco ('Praça Pinheiros nº','12','Parque Santos','87463281','5');
CALL sp_insert_endereco('EPTG','47','Posto Ipiranga','86774245','1');
CALL sp_insert_endereco('Alameda Santos','7','Baixada Santista','45638356','2');
CALL sp_insert_endereco('Chácara Alcântara','45','Bento Gonçalves','75278431','4');
CALL sp_insert_endereco('Chácara Itapema','35','Itapema do Sul','12345643','4');
CALL sp_insert_endereco('Alameda','123','Condominio soares','75638356','4');
CALL sp_insert_endereco('Avenida Aracucárias','1212','Apto 201','71936250','1');
CALL sp_insert_endereco('QNQ 01 Conjunto 2','2','Comercial','72277654','6');
CALL sp_insert_endereco('Av.das nações','1234','Bloco H','72234354','1');
CALL sp_insert_endereco('Av.Antonio Mascarenhas','1202','Centro','73457654','1');


INSERT INTO professor_disciplina (fk_cod_professor, fk_cod_disciplina)
VALUES 	('1','6'), ('2','1'), ('3','8'), ('4','4'),
	('5','4'), ('6','1'), ('7','3'), ('8','7'),
	('9','9'), ('10','2'), ('11','8');


INSERT INTO curso_disciplina (fk_cod_curso, fk_cod_disciplina)
VALUES 	('1','6'), ('2','3'), ('3','1'), ('4','4'), ('5','9'), 
	('6','1'), ('7','7'), ('8','3'), ('9','7'), ('10','9'), ('11','8');


DELIMITER \\ 
CREATE PROCEDURE sp_insert_aluno (a_nome VARCHAR (25), a_sobrenome VARCHAR (30), a_sexo CHAR (1),a_mae VARCHAR (50), 
				 a_pai VARCHAR (50), a_email VARCHAR (50), a_cpf VARCHAR (14), a_telefone VARCHAR (10), 
				 a_status_aluno BOOLEAN, a_fk_cod_curso INT, a_fk_cod_turma INT, a_fk_cod_endereco INT)
BEGIN 	
    INSERT INTO  aluno (nome_aluno, sobrenome_aluno, sexo, mae, pai, email, cpf, telefone, status_aluno,
			fk_cod_curso, fk_cod_turma, fk_cod_endereco) 
    VALUES (fn_maiuscula(a_nome), fn_maiuscula(a_sobrenome), fn_maiuscula(a_sexo), fn_maiuscula(a_mae), fn_maiuscula(a_pai),
		        fn_maiuscula(a_email), a_cpf, a_telefone, a_status_aluno, a_fk_cod_curso, a_fk_cod_turma, a_fk_cod_endereco);
END \\
DELIMITER ;


CALL sp_insert_aluno ('marta', 'ferreira lima', 'f','joana maceno torres','jose antonio torres', 'joaana@gmail.com','86576576534','98765098','1','2','5','8');
CALL sp_insert_aluno ('Marcelo','Farias de Moraes Junior','M','Maria Antonia Faria','Marcelo de Rezende Faria','marcellojr2@gmail.com', '43454345676','987654232','1','1','1','1');
CALL sp_insert_aluno ('José','Guimarães Souza','M','Marta Souza','Roberto Guimarães Souza','jjosegui@gmail.com', '34343567376', '997664232','1','2','2','2');
CALL sp_insert_aluno ('Joana','Fonseca Gomes','F','Glória Medeiros Gomes','Murilo de Rezende Gomes','joaana2@gmail.com','98735465765','945658765','1','3','3','3');
CALL sp_insert_aluno ('Roberta','Freire Freitas','F','Georgina Freire Freitas','Antonio Borges Freitas','betinhafreire2@gmail.com', '43212456276','94547654','1','4','4','4');
CALL sp_insert_aluno ('Romana','Ribeiro Silva','F','Samantha Souza Silva','Teodoro Ribeiro Silva','romana21@gmail.com','43452312187','93438709','1','5','5','5');
CALL sp_insert_aluno ('Georgina','Gonçalves de Medeiros','F','Selma Santos Ribeiro','Fernando Pereira Ribeiro','geoginamedgmail.com','23455435676','997665231','1','6','6','6');
CALL sp_insert_aluno ('Lucas','Araújo Damasceno','M','Francisca Machado Damasceno','Wilson Araújo Damasceno','lukinhha2022@gmail.com','12387698876','99543098','1','7','7','7');
CALL sp_insert_aluno ('Patrick','Goes de Grawskik','M','Marta Goes Grawskik','Jhon Grawskik','pgrawskik@gmail.com','65498765423','99994587','1','8','8','8');
CALL sp_insert_aluno ('Nicolas','Fernandes Ribeiro','M','Maria Paulo Mendes Ribeiro','Marco Antonio Pereira Ribeiro','marcellojr2@gmail.com','23234354657','987654232','1','9','9','9');
CALL sp_insert_aluno ('Samara','de Oliveira','F','Ruth de Oliveira','Fred Felipe de Oliveira','marcellojr2@gmail.com','32987653876','987654232','1','10','10','10');
CALL sp_insert_aluno ('João Felipe','Moreira Costa','M','Guilhermina Santos Costa','Sergio Plinio Costa','jhonphelipe@gmail.com','29873546738','98564329','1','11','11','11');
CALL sp_insert_aluno ('Marilia','Farias de Moraes Junior','F','Sirlene Mascarenhas Souza','Pedro Pedreira Souza','marilinhafarias@yahoo.com','30983456726','98983498','1','12','12','12');
CALL sp_insert_aluno ('Rebeca','Gusmão Borges','F','Flora Gusmão Borges','Marcos Rezende Borges','rebecabgusmao@gmail.com','98735342323','984784232','1','3','3','3');
CALL sp_insert_aluno ('Pablo','Almeida Albuquerque','m','flavia de morais pessoa','antonio barbosa neto','pablinhoal@yahoo.com.br','87654398765','98765432','1','2','6','3');
CALL sp_insert_aluno ('Fabiola','Santos Pires','f','fernanda lima pires','angelo marques pires','angelouou@yahoo.com.br','87534208734','98433343','4','5','2','13');


INSERT INTO historico (dt_inicio, dt_fim, FK_RA)
VALUES 	('2022-06-15','2022-12-20','1'), ('2022-06-15','2022-12-20','2'), ('2022-06-15','2022-12-20','3'),
        ('2022-06-15','2022-12-20','4'), ('2022-06-15','2022-12-20','5'), ('2022-02-15','2022-06-20','6'),
        ('2022-06-15','2022-12-20','7'), ('2022-02-15','2022-06-20','8'), ('2022-01-15','2022-06-20','9'),
        ('2022-06-15','2022-12-20','10'), ('2022-06-15','2022-12-20','11'), ('2022-02-18','2022-07-20','12'),
        ('2022-06-15','2022-12-20','13');


INSERT INTO telefone (num_telefone, fk_cod_tipo_telefone)
VALUES 	('32345432','1'), ('43546576','2'), ('99876543','3'),
	('43227656','1'), ('34536546','2'), ('99845453','3'),
	('37654345','1'), ('45469876','2'), ('98768765','3'),
	('34567654','1'), ('33545467','2'), ('99765439','3'),
	('33434542','1'), ('33334545','2'), ('99342564','3');
        

INSERT INTO telefone_aluno (fk_cod_telefone, fk_RA)
VALUES 	('1','13'), ('2','11'), ('3','10'), 
	('1','9'), ('2','2'), ('3','7'),
	('1','6'), ('2','1'), ('3','5'),
        ('1','4'), ('2','8'), ('3','10'),
	('1','12'), ('2','12'), ('3','3');
        

INSERT INTO disciplina_historico (nota, frequencia, fk_cod_disciplina, fk_cod_historico)
VALUES 	('6.5','15','1','3'), ('9.0','20','8','6'), ('9.8','20','9','7'),
	('8.9','20','2','4'), ('4.5','18','7','5'), ('5.7','12','10','8'),
        ('7.8','13','3','2'), ('8.5','17','6','12'), ('6.8','10','11','9'),
        ('10.0','14','4','1'),('7.1','19','5','11'), ('8.5','18','5','10'),
        ('6.87','16','8','13');
        

INSERT INTO aluno_disciplina (fk_cod_disciplina, fk_RA)
VALUES 	('1','13'), ('10','4'), ('11','3'),
	('2','12'), ('9','5'), ('2','2'),
        ('3','11'), ('8','6'), ('4','1'),
        ('4','10'), ('7','7'), ('5','8'),
        ('5','9'),  ('6','8'), ('8','12');
        
-- Trigger criada para contar quantidade de disciplinas o aluno esta cursando 

DELIMITER //
CREATE TRIGGER tr_atualiza_disciplina
AFTER INSERT 
ON aluno
FOR EACh ROW
	BEGIN
		DECLARE total_disciplina INT;
		SET total_disciplina = (SELECT COUNT(fk_cod_disciplina) FROM aluno_disciplina WHERE RA =  NEW.RA);
        UPDATE aluno SET disciplina = total_disciplina WHERE RA = NEW.RA;
    END //
DELIMITER ;



