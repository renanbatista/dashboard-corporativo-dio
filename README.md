
# Desafio DIO: Criando um Dashboard Corporativo com Integração com MySQL e Azure

Este projeto foi desenvolvido como parte do curso "Formação Power BI Analyst" da DIO. O objetivo foi criar um banco de dados MySQL em um servidor externo e estabelecer uma conexão com o Power BI. As transformações foram executadas no Power Query e um relatório visual foi criado para validar a correta importação dos dados.

## Tabelas Criadas no Banco de Dados MySQL

- **employee**
- **departament**
- **dept_locations**
- **project**
- **works_on**
- **dependent**

## Diretrizes para Transformação dos Dados

### 1. Verificação dos Cabeçalhos e Tipos de Dados

#### employee
| Nome     | Tipo de Dados   |
|----------|-----------------|
| Fname    | varchar(15)     |
| Minit    | char(1) YES     |
| Lname    | varchar(15)     |
| Ssn      | char(9)         |
| Bdate    | date            |
| Address  | varchar(30)     |
| Sex      | char(1)         |
| Salary   | decimal(10,2)   |
| Super_ssn| char(9)         |
| Dno      | int(11)         |

#### departament
| Nome            | Tipo de Dados   |
|-----------------|-----------------|
| Dname           | varchar(15)     |
| Dnumber         | int(11)         |
| Mgr_ssn         | char(9)         |
| Mgr_start_date  | date            |
| Dept_create_date| date            |

#### dept_locations
| Nome     | Tipo de Dados   |
|----------|-----------------|
| Dnumber  | int(11)         |
| Dlocation| varchar(15)     |

#### project
| Nome     | Tipo de Dados   |
|----------|-----------------|
| Pname    | varchar(15)     |
| Pnumber  | int(11)         |
| Plocation| varchar(15)     |
| Dnum     | int(11)         |

#### works_on
| Nome     | Tipo de Dados   |
|----------|-----------------|
| Essn     | char(9)         |
| Pno      | int(11)         |
| Hours    | decimal(3,1)    |

#### dependent
| Nome           | Tipo de Dados   |
|----------------|-----------------|
| Essn           | char(9)         |
| Dependent_name | varchar(15)     |
| Sex            | char(1)         |
| Bdate          | date            |
| Relationship   | varchar(8)      |

### 2. Modificação dos Valores Monetários
- Coluna `salary` da tabela `employee` foi modificada para o tipo `double preciso`.

### 3. Verificação e Análise de Nulos
- Campo nulo localizado não foi removido.
- Registros com campos nulos: `employee` - Campo `Super_ssn` para o registro com `Ssn 888665555`.

### 4. Verificação de Colaboradores Sem Gerente
- SELECT * FROM `employee` WHERE Super_ssn is null
- **Resultado:** SSN: 888665555

### 5. Verificação de Departamentos Sem Gerente
- SELECT * FROM `departament` WHERE Mgr_ssn not in (SELECT Ssn from employee)
- **Resultado:** Nenhum departamento sem gerente.

### 6. Preenchimento de Lacunas de Gerentes (Não Aplicável)
- Não existem departamentos sem gerente.

### 7. Verificação do Número de Horas dos Projetos
- SELECT P.PNumber, P.Pname, SUM(W.Hours) as Hours FROM project as P INNER JOIN works_on as W ON P.PNumber = W.Pno GROUP BY P.PNumber, P.Pname ORDER BY SUM(W.Hours) DESC

### 8. Separação de Colunas Complexas
- Coluna `Address` da tabela `employee` foi separada.

### 9. Mescla de Consultas
- Mescla de `employee` e `departament`:
  ```sql
  SELECT E.*, D.Dname FROM `employee` as E INNER JOIN departament as D ON E.Dno = D.Dnumber
  ```

### 10. Eliminação de Colunas Desnecessárias

### 11. Junção de Colaboradores e Respectivos Gerentes
- SELECT Fname, Minit, LName, Ssn, Bdate, Address, Sex, Salary, Dno, (select FName from employee WHERE Ssn = E.Super_ssn) as Manager FROM `employee` as E WHERE 1

### 12. Mescla de Nome e Sobrenome dos Colaboradores
- SELECT CONCAT(Fname, ' ', LName) as name, Minit, Ssn, Bdate, Address, Sex, Salary, Super_ssn, Dno FROM `employee`

### 13. Mescla de Nomes de Departamentos e Localização
- SELECT D.*, L.Dlocation FROM `departament` as D INNER JOIN dept_locations as L ON D.Dnumber = L.Dnumber

### 14. Justificativa para Utilização de Mescla (Não Aplicável)

### 15. Agrupamento de Colaboradores por Gerente
- SELECT E.Fname, E.LName, E.Ssn, COUNT(*) as employees FROM `employee` as E INNER JOIN employee as E2 ON E.Ssn = E2.Super_ssn GROUP BY E.Fname, E.LName, E.Ssn ORDER BY COUNT(*) DESC

| Nome     | Quantidade |
|----------|------------|
| Franklin | 3          |
| James    | 2          |
| Jennifer | 2          |

### 16. Eliminação de Colunas Desnecessárias
- As colunas desnecessárias foram removidas de cada tabela.

## Conclusão
Este projeto, guiado pela professora Juliana Mascarenhas, faz parte do módulo "Criando um Relatório de Vendas Elegante com Power BI" da DIO. Através dele, foram aplicados conceitos de integração, transformação e visualização de dados utilizando MySQL e Power BI.
