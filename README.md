# hospital
## Descrição do Hospital 
Um pequeno hospital local busca desenvolver um novo sistema que atenda melhor às suas necessidades. Atualmente, parte da operação ainda se apoia em planilhas e arquivos antigos, mas espera-se que esses dados sejam transferidos para o novo sistema assim que ele estiver funcional. Neste momento, é necessário analisar com cuidado as necessidades desse cliente e sugerir uma estrutura de banco de dados adequada por meio de um Diagrama Entidade-Relacionamento.

## Estudo do caso

 O hospital necessita de um sistema para sua área clínica que ajude a controlar consultas realizadas. Os médicos podem ser generalistas, especialistas ou residentes e têm seus dados pessoais cadastrados em planilhas digitais. Cada médico pode ter uma ou mais especialidades, que podem ser pediatria, clínica geral, gastroenterologia e dermatologia. Alguns registros antigos ainda estão em formulário de papel, mas será necessário incluir esses dados no novo sistema.

Os pacientes também precisam de cadastro, contendo dados pessoais (nome, data de nascimento, endereço, telefone e e-mail), documentos (CPF e RG) e convênio. Para cada convênio, são registrados nome, CNPJ e tempo de carência.

As consultas também têm sido registradas em planilhas, com data e hora de realização, médico responsável, paciente, valor da consulta ou nome do convênio, com o número da carteira. Também é necessário indicar na consulta qual a especialidade buscada pelo paciente.

Deseja-se ainda informatizar a receita do médico, de maneira que, no encerramento da consulta, ele possa registrar os medicamentos receitados, a quantidade e as instruções de uso. A partir disso, espera-se que o sistema imprima um relatório da receita ao paciente ou permita sua visualização via internet.
![hospital](https://github.com/ErickGabrielDev/hospital/assets/159559836/92337f27-8a92-45a4-895e-c98bc822c1be)

## O segredo do Hospital 
Após a primeira versão do projeto de banco de dados para o sistema hospitalar, notou-se a necessidade de expansão das funcionalidades, incluindo alguns requisitos essenciais a essa versão do software. As funcionalidades em questão são para o controle na internação de pacientes. Será necessário expandir o Modelo ER desenvolvido e montar o banco de dados, criando as tabelas para o início dos testes.

## Estudo de caso
Considere a seguinte descrição e o diagrama ER abaixo:

No hospital, as internações têm sido registradas por meio de formulários eletrônicos que gravam os dados em arquivos.

Para cada internação, são anotadas a data de entrada, a data prevista de alta e a data efetiva de alta, além da descrição textual dos procedimentos a serem realizados.

As internações precisam ser vinculadas a quartos, com a numeração e o tipo.

Cada tipo de quarto tem sua descrição e o seu valor diário (a princípio, o hospital trabalha com apartamentos, quartos duplos e enfermaria).

Também é necessário controlar quais profissionais de enfermaria estarão responsáveis por acompanhar o paciente durante sua internação. Para cada enfermeiro(a), é necessário nome, CPF e registro no conselho de enfermagem (CRE).

A internação, obviamente, é vinculada a um paciente – que pode se internar mais de uma vez no hospital – e a um único médico responsável.
![hospital2](https://github.com/ErickGabrielDev/hospital/assets/159559836/abdfdea8-7e32-41c1-a930-b34e7c7420cd)

# BancoDeDados-Hospital
```sql
create database hospital;
use hospital;

create table medico (
	codigo_medico int primary key,
	nome_medico varchar(50) not null,
	cpf_medico varchar(15) unique not null,
	rg_medico varchar(9) unique not null,
	cargo_medico varchar(20) not null, 
    crm_medico varchar(20) not null,
	data_nasc_medico date not null
);

create table especialidade_medico(
	codigo_medico int not null,
    codigo_especialidade int not null
);

create table paciente (
	codigo_paciente int primary key,
	nome_paciente varchar(50) not null,
	cpf_paciente varchar(11) unique not null, 
	rg_paciente varchar(9) unique not null, 
	telefone_paciente varchar(11) not null,
	email_paciente varchar(50) unique not null,
	data_nasc_paciente date not null,
	numero_carteira_convenio int not null
);

create table convenio (
	numero_carteira int primary key, 
	nome_convenio varchar(50) not null,
	tempo_carencia varchar(10) not null,
	cnpj_convenio varchar(14) unique not null
);

create table consulta(
	codigo_consulta int primary key,
	data_consulta date not null,
	horario_consulta time not null,
	valor_consulta decimal(5,2) not null,
	forma_pagamento varchar(20) not null,
	codigo_paciente int not null,
	codigo_medico int not null, 
	codigo_especialidade int not null,
    numero_carteira_convenio int
);

create table especialidade(
	codigo_especialidade int primary key,
	descricao_especialidade varchar(50) not null
);

create table receita(
	codigo_receita int primary key,
    nome_medicamento varchar(100) not null,
    instrucoes_medicamento varchar(255) not null,
    qtd_medicamento int not null,
    codigo_consulta int not null
);

create table endereco(
	codigo_endereco int primary key,
    rua varchar(100) not null,
    bairro varchar(100) not null,
    cep varchar(9) not null,
    numero int not null,
    complemento varchar(100),
    codigo_paciente int not null
);

create table internacao(
	codigo_internacao int primary key,
    data_prevista_alta date not null,
    data_entrada date not null,
    data_alta date,
    codigo_medico int not null,
    numero_quarto int not null,
    codigo_paciente int not null,
    numero_carteira_convenio int
);

create table quarto(
	numero_quarto int primary key,
    codigo_tipo_quarto int not null
);

create table procedimento(
	codigo_procedimento int primary key,
    nome_procedimento varchar(50) not null,
    descricao_procedimento varchar(100) not null
);

create table procedimento_internacao(
	codigo_internacao int not null,
    codigo_procedimento int not null
);

create table enfermeiro(
	codigo_enfermeiro int primary key,
    nome_enfermeiro varchar(50) not null,
    rg_enfermeiro varchar(9) unique not null,
    cpf_enfermeiro varchar(15) unique not null,
	cre_enfermeiro varchar(20) unique not null,
    data_nasc_enfermeiro date not null
);

create table tipo_quarto(
	codigo_tipo_quarto int primary key,
    diaria_quarto decimal(5,2) not null,
    descricao_quarto varchar(200) not null
);

create table enfermeiro_internacao(
	codigo_enfermeiro int not null,
    codigo_internacao int not null
);

alter table paciente add foreign key fk_numero_carteira_convenio (numero_carteira_convenio) references convenio(numero_carteira);

alter table consulta add foreign key fk_codigo_especialidade (codigo_especialidade) references especialidade(codigo_especialidade);

alter table consulta add foreign key fk_codigo_medico (codigo_medico) references medico(codigo_medico);

alter table consulta add foreign key fk_codigo_paciente (codigo_paciente) references paciente(codigo_paciente);

alter table receita add foreign key fk_codigo_consulta (codigo_consulta) references consulta(codigo_consulta);

alter table consulta add foreign key fk_numero_carteira_convenio (numero_carteira_convenio) references convenio(numero_carteira);

alter table endereco add foreign key fk_codigo_paciente (codigo_paciente) references paciente(codigo_paciente);

alter table internacao add foreign key fk_numero_quarto (numero_quarto) references quarto(numero_quarto);

alter table procedimento_internacao add foreign key fk_codigo_internacao (codigo_internacao) references internacao(codigo_internacao);
alter table procedimento_internacao add foreign key fk_codigo_procedimento (codigo_procedimento) references procedimento(codigo_procedimento);

alter table internacao add foreign key fk_codigo_medico (codigo_medico) references medico(codigo_medico);

alter table enfermeiro_internacao add foreign key fk_codigo_enfermeiro (codigo_enfermeiro) references enfermeiro(codigo_enfermeiro);
alter table enfermeiro_internacao add foreign key fk_codigo_internacao (codigo_internacao) references internacao(codigo_internacao);

alter table quarto add foreign key fk_tipo_quarto (codigo_tipo_quarto) references tipo_quarto(codigo_tipo_quarto);

alter table especialidade_medico add foreign key fk_codigo_medico (codigo_medico) references medico(codigo_medico);
alter table especialidade_medico add foreign key fk_codigo_especialidade (codigo_especialidade) references especialidade(codigo_especialidade);

alter table internacao add foreign key fk_codigo_paciente (codigo_paciente) references paciente(codigo_paciente);

alter table internacao add foreign key fk_numero_carteira_convenio (numero_carteira_convenio) references convenio(numero_carteira);
```

#Inclusão de Médicos e Especialidades
```sql
INSERT INTO medico (codigo_medico, nome_medico, cpf_medico, rg_medico, cargo_medico, crm_medico, data_nasc_medico)
VALUES
(1, 'Dr. João Silva', '123.456.789-00', '123456789', 'Clínico Geral', 'CRM12345', '1980-05-15'),
(2, 'Dra. Maria Santos', '987.654.321-00', '987654321', 'Pediatra', 'CRM54321', '1975-08-20'),
(3, 'Dr. Pedro Oliveira', '111.222.333-00', '111222333', 'Ortopedista', 'CRM67890', '1982-03-10'),
(4, 'Dr. Ana Souza', '444.555.666-00', '444555666', 'Ginecologista', 'CRM13579', '1988-11-25'),
(5, 'Dra. Luiza Costa', '777.888.999-00', '777888999', 'Cardiologista', 'CRM24680', '1970-09-05'),
(6, 'Dr. Marcos Ferreira', '222.333.444-00', '222333444', 'Neurologista', 'CRM97531', '1985-07-12'),
(7, 'Dr. Camila Almeida', '555.666.777-00', '555666777', 'Dermatologista', 'CRM86420', '1983-01-30'),
(8, 'Dra. Renata Oliveira', '999.888.777-00', '999888777', 'Oftalmologista', 'CRM35791', '1978-04-18'),
(9, 'Dr. André Santos', '333.222.111-00', '333222111', 'Urologista', 'CRM65432', '1987-06-22'),
(10, 'Dra. Sofia Lima', '666.777.888-00', '666777888', 'Psiquiatra', 'CRM12378', '1981-02-08');

-- Inclusão de especialidades
INSERT INTO especialidade (codigo_especialidade, descricao_especialidade)
VALUES
(1, 'Pediatria'),
(2, 'Clínica Geral'),
(3, 'Gastrenterologia'),
(4, 'Dermatologia'),
(5, 'Ortopedia'),
(6, 'Cardiologia'),
(7, 'Neurologia');

-- Relacionamento entre médicos e especialidades
INSERT INTO especialidade_medico (codigo_medico, codigo_especialidade)
VALUES
(1, 2), -- Dr. João Silva - Clínica Geral
(2, 1), -- Dra. Maria Santos - Pediatria
(3, 5), -- Dr. Pedro Oliveira - Ortopedia
(4, 4), -- Dr. Ana Souza - Dermatologia
(5, 6), -- Dra. Luiza Costa - Cardiologia
(6, 7), -- Dr. Marcos Ferreira - Neurologia
(7, 4), -- Dr. Camila Almeida - Dermatologia
(8, 6), -- Dra. Renata Oliveira - Cardiologia
(9, 5), -- Dr. André Santos - Ortopedia
(10, 7); -- Dra. Sofia Lima - Neurologia
```

#Inclusão de Consultas
```sql
INSERT INTO consulta (codigo_consulta, data_consulta, horario_consulta, valor_consulta, forma_pagamento, codigo_paciente, codigo_medico, codigo_especialidade, numero_carteira_convenio)
VALUES
(1, '2021-05-15', '10:00:00', 150.00, 'Cartão', 1, 4, 4, 456789),
(2, '2020-07-20', '14:30:00', 120.00, 'Dinheiro', 2, 2, 1, 234567),
(3, '2019-09-10', '09:15:00', 180.00, 'Cheque', 3, 6, 7, 345678),
(4, '2018-04-25', '11:00:00', 200.00, 'Cartão', 4, 8, 6, 456789),
(5, '2017-08-12', '16:45:00', 100.00, 'Dinheiro', 5, 5, 6, 567890),
(6, '2016-11-30', '08:30:00', 160.00, 'Cartão', 6, 10, 7, 678901),
(7, '2015-03-05', '13:00:00', 130.00, 'Dinheiro', 7, 7, 4, 789012),
(8, '2021-01-08', '15:45:00', 170.00, 'Cartão', 8, 9, 5, 890123),
(9, '2020-10-22', '10:30:00', 190.00, 'Dinheiro', 9, 3, 5, 901234),
(10, '2019-06-18', '14:00:00', 140.00, 'Cartão', 10, 1, 2, 123456),
(11, '2018-02-14', '09:45:00', 210.00, 'Cheque', 1, 3, 5, 234567),
(12, '2017-09-30', '11:30:00', 110.00, 'Dinheiro', 2, 4, 4, 345678),
(13, '2016-04-05', '16:15:00', 130.00, 'Cartão', 3, 5, 6, 456789),
(14, '2015-08-21', '08:00:00', 180.00, 'Cheque', 4, 6, 7, 567890),
(15, '2021-02-28', '10:15:00', 150.00, 'Dinheiro', 5, 8, 6, 678901),
(16, '2020-11-10', '14:30:00', 200.00, 'Cartão', 6, 9, 5, 789012),
(17, '2019-07-17', '09:00:00', 160.00, 'Dinheiro', 7, 7, 4, 890123),
(18, '2018-03-23', '12:45:00', 170.00, 'Cartão', 8, 10, 7, 901234),
(19, '2017-10-09', '15:30:00', 190.00, 'Cheque', 9, 2, 1, 123456),
(20, '2016-05-15', '08:30:00', 140.00, 'Dinheiro', 10, 1, 2, 234567);
```
#Inclusão de Convênios
```
INSERT INTO convenio (numero_carteira, nome_convenio, tempo_carencia, cnpj_convenio)
VALUES
(123456, 'Convênio A', '12 meses', '12345678901234'),
(234567, 'Convênio B', '6 meses', '23456789012345'),
(345678, 'Convênio C', '9 meses', '34567890123456'),
(456789, 'Convênio D', '8 meses', '45678901234567'),
(567890, 'Convênio E', '10 meses', '56789012345678');
```

#Criação de Entidades de Relacionamento
```sql 
UPDATE paciente SET numero_carteira_convenio = 123456 WHERE codigo_paciente IN (1, 4, 7, 10);
UPDATE paciente SET numero_carteira_convenio = 234567 WHERE codigo_paciente IN (2, 5, 8);
UPDATE paciente SET numero_carteira_convenio = 345678 WHERE codigo_paciente IN (3, 6);
UPDATE paciente SET numero_carteira_convenio = 456789 WHERE codigo_paciente IN (9);
UPDATE paciente SET numero_carteira_convenio = 567890 WHERE codigo_paciente IN (2);

-- Corrigindo chave estrangeira entre convênio e médico
ALTER TABLE medico ADD COLUMN codigo_convenio int;
ALTER TABLE medico ADD CONSTRAINT fk_codigo_convenio FOREIGN KEY (codigo_convenio) REFERENCES convenio(numero_carteira);
```

#Inclusão de Internações
```sql
INSERT INTO internacao (codigo_internacao, data_prevista_alta, data_entrada, data_alta, codigo_medico, numero_quarto, codigo_paciente, numero_carteira_convenio)
VALUES
(1, '2021-07-10', '2021-06-25', '2021-07-05', 4, 101, 1, 456789),
(2, '2020-11-15', '2020-11-01', '2020-11-10', 8, 102, 2, 567890),
(3, '2019-12-20', '2019-12-05', '2019-12-18', 5, 103, 3, 345678),
(4, '2018-09-25', '2018-09-10', '2018-09-22', 6, 104, 4, 567890),
(5, '2017-05-30', '2017-05-15', '2017-05-28', 10, 105, 5, 678901),
(6, '2021-03-08', '2021-02-20', '2021-03-05', 2, 101, 6, 234567),
(7, '2020-08-12', '2020-08-01', '2020-08-10', 3, 102, 7, 345678),
(8, '2019-11-18', '2019-11-05', '2019-11-15', 7, 103, 8, 789012),
(9, '2018-04-25', '2018-04-10', '2018-04-20', 9, 104, 9, 456789),
(10, '2017-07-01', '2017-06-20', '2017-06-30', 1, 105, 10, 123456);
```

#Inclusão de Profissionais de Enfermagem
```sql
INSERT INTO enfermeiro (codigo_enfermeiro, nome_enfermeiro, rg_enfermeiro, cpf_enfermeiro, cre_enfermeiro, data_nasc_enfermeiro)
VALUES
(1, 'Fernanda Oliveira', '111222333', '123.456.789-00', 'COREN12345', '1980-05-15'),
(2, 'Carlos Santos', '222333444', '987.654.321-00', 'COREN54321', '1975-08-20'),
(3, 'Amanda Lima', '333444555', '111.222.333-00', 'COREN67890', '1982-03-10'),
(4, 'Rafaela Souza', '444555666', '444.555.666-00', 'COREN13579', '1988-11-25'),
(5, 'José Costa', '555666777', '777.888.999-00', 'COREN24680', '1970-09-05'),
(6, 'Mariana Almeida', '666777888', '222.333.444-00', 'COREN97531', '1985-07-12'),
(7, 'Lucas Ferreira', '777888999', '555.666.777-00', 'COREN86420', '1983-01-30'),
(8, 'Fátima Oliveira', '888999000', '999.888.777-00', 'COREN35791', '1978-04-18'),
(9, 'Paulo Santos', '999000111', '333.222.111-00', 'COREN65432', '1987-06-22'),
(10, 'Ana Lima', '000111222', '666.777.888-00', 'COREN12378', '1981-02-08');
```

#Relacionamento entre Internação e Enfermeiro

```sql
INSERT INTO enfermeiro_internacao (codigo_enfermeiro, codigo_internacao)
VALUES
(1, 1),
(2, 2),
(3, 3),
(4, 4),
(5, 5),
(6, 6),
(7, 7),
(8, 8),
(9, 9),
(10, 10);
```

#Inclusão de Tipos de Quarto
```sql
INSERT INTO tipo_quarto (codigo_tipo_quarto, diaria_quarto, descricao_quarto)
VALUES
(101, 150.00, 'Apartamento'),
(102, 100.00, 'Quarto Duplo'),
(103, 80.00, 'Enfermaria');
```









