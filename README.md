#  Dashboard Corporativo - Integração MySQL & Power BI

##  Descrição do Projeto
Este projeto consiste no desenvolvimento de um **Dashboard Corporativo** voltado para a análise de dados de colaboradores, gerência e departamentos. O fluxo de trabalho englobou desde a ingestão de dados em um banco **MySQL** (modelo *azure_company*), passando pela extração, transformação e limpeza de dados (**ETL**) no **Power Query**, até a criação de um painel interativo no **Power BI Desktop**.

---

##  Processo Executado (Tratamento de Dados no Power Query)

Durante a fase de transformação de dados, foram executadas as seguintes etapas para garantir a consistência e qualidade da análise:

1. **Ingestão de Dados:** Conexão direta com o banco de dados MySQL local contendo as tabelas do esquema corporativo.
2. **Limpeza e Padronização de Tipos:**
   * Ajuste dos tipos de dados para texto, inteiros, datas e valores monetários (ex: coluna `Salário`).
   * Substituição de valores nulos e tratamento de colunas sem correspondência.
3. **Tratamento de Nomes e Colunas:**
   * Remoção de colunas redundantes ou desnecessárias para a modelagem visual.
   * Renomeação de campos em inglês/técnicos para português (ex: `Salary` ➔ `Salário`).
4. **Construção de Chaves e Relacionamentos:**
   * Junção de dados entre colaboradores e seus respectivos gerentes/departamentos para permitir análises hierárquicas.

---

##  Conceitos de Transformação: Mesclar vs. Combinar Consultas

### Diferença Fundamental
* **Mesclar Consultas (*Merge*):** Funciona de forma similar a um `JOIN` em SQL (à esquerda, interno, etc.). Une duas ou mais tabelas na **horizontal**, adicionando novas colunas a uma tabela com base em uma coluna de correspondência (chave primária/estrangeira).
* **Combinar Consultas (*Append*):** Funciona de forma similar a um `UNION ALL` em SQL. Une duas ou mais tabelas na **vertical**, empilhando linhas que possuem a mesma estrutura de colunas.

###  Justificativa da Escolha no Projeto
Neste projeto, **apenas a operação de Mesclar Consultas foi adotada**. 

**Justificativa:** A necessidade do negócio era enriquecer a tabela de colaboradores com informações complementares de outras entidades (como dados do Gerente e localização do Departamento). Como a estrutura do modelo relacional trazia entidades distintas conectadas por chaves (ex: `Ssn` e `Super_ssn`), o processo exigiu a adição de **colunas adicionais na horizontal** para cruzamento de dados, e não o empilhamento de novas linhas.

---

##  Consulta SQL Utilizada

Abaixo está a estrutura da consulta SQL (DML) utilizada para a verificação e extração primária dos dados no ambiente MySQL local:

```sql
-- Seleção de colaboradores e mapeamento do relacionamento com gerentes
SELECT 
    e.Fname AS Colaborador,
    m.Fname AS Gerente,
    d.Dname AS Departamento,
    e.Salary AS Salario
FROM employee e
LEFT JOIN employee m ON e.Super_ssn = m.Ssn
LEFT JOIN departament d ON e.Dno = d.Dnumber;
