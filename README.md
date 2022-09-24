# db_faculdade
Atividade proposta ao final do curso de Administrador de banco de dados do SENAI


## Projeto db_faculdade

A atividade consiste na criação de um banco de dados para uma faculdade. O projeto consta com os modelos conceitual, lógico e físico. O objetivo é realizar o controle centralizado dos alunos, professores, cursos, disciplinas, histórico escolar e turmas. O banco possue funções, views, procedures e trigger. 

### Regras de negocio:

* Um aluno só pode estar matriculado em um curso por vez, epossuem um código de identificação (RA);
* Cursos são compostos por várias disciplinas;
* Cada disciplina terá no máximo 30 alunos por turma, podem ser obrigatórias ou optativas, dependendo do curso, e pertencem a departamentos específicos. Cada disciplina possui um código de identificação;
* Alunos podem trancar matrícula, não estando então matriculados em nenhuma disciplina no semestre;
* Em cada semestre, cada aluno pode se matricular em no máximo 9 disciplinas;
* O aluno só pode ser reprovado no máximo 3 vezes na mesma disciplina;
* A faculdade terá no máximo 3000 alunos matriculados simultaneamente, em 10 cursos;
* Entram 300 alunos novos por ano;
* Existem 90 disciplinas no total disponíveis;
* Um histórico escolar traz todas as disciplinas cursadas por aluno. Incluindo nota final, Frequência e período do curso realizado;
* Professores podem ser cadastrados mesmo sem lecionar disciplinas;
* Existem 40 professores trabalhando na escola. Cada professor irá lecionar no máximo 4 disciplinas diferentes, e deve estar vinculado a um departamento, e  identificados por um código de professor;

Modelo Conceitual:

![Conceitual_db_faculdade](https://user-images.githubusercontent.com/112898303/192119876-131b7bbb-eab5-4265-a57c-7c482305c3a8.png)


Modelo lógico:

![Logico_db_faculdade](https://user-images.githubusercontent.com/112898303/192120330-2a533da4-27fe-447b-b96f-2073abbc30da.png)

















