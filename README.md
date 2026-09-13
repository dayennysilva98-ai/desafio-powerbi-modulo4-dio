# 📊 Desafio DIO: Processamento e Transformação de Dados com Power BI

[![Power BI](https://img.shields.io/badge/Power_BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)](https://powerbi.microsoft.com/)
[![MySQL](https://img.shields.io/badge/MySQL-005C84?style=for-the-badge&logo=mysql&logoColor=white)](https://www.mysql.com/)
[![DIO](https://img.shields.io/badge/Digital_Innovation_One-000000?style=for-the-badge&logo=dio&logoColor=white)](https://www.dio.me/)

Projeto desenvolvido para o módulo **"Processamento de Dados com Power BI e MySQL"** da **Digital Innovation One (DIO)**. O objetivo principal deste desafio é a extração, limpeza, transformação e modelagem de dados da base de dados relacional de uma empresa utilizando o **Power Query / Power BI Desktop**.

---

## 📂 Estrutura do Repositório

* 📑 **[`dados_desafio_company_transformado.xlsx`](dados_desafio_company_transformado.xlsx)**: Arquivo Excel final contendo as tabelas originais e todas as tabelas transformadas prontas para análise.
* 📜 **[`consultas_power_query.m`](consultas_power_query.m)**: Script contendo todas as fórmulas e etapas M do Power Query.
* 🗄️ **[`script_bd_company.sql`](script_bd_company.sql)**: Script SQL completo para criação e povoamento do banco de dados `azure_company` (MySQL).
---

## 🛠️ Etapas e Diretrizes de Transformação dos Dados

Abaixo estão descritas todas as 15 tarefas de transformação realizadas no **Power Query Editor**:

### 1. Verificação de Cabeçalhos e Tipos de Dados
* Garantida a promoção dos cabeçalhos das tabelas (`employee`, `department`, `dept_locations`, `project`, `works_on`, `dependent`).
* Tipagem das colunas ajustada devidamente (Texto, Números Inteiros, Datas e Moeda).

### 2. Modificação de Valores Monetários
* A coluna `Salary` da tabela `employee` foi alterada para o tipo **Número Decimal Fixo / Moeda (Currency)** para evitar inconsistências e erros de arredondamento em cálculos financeiros.

### 3. Tratamento de Valores Nulos
* Realizada a inspeção de valores nulos em todas as colunas.
* Verificado que os valores nulos encontrados são válidos e representam regras de negócio legítimas (ex: colaboradores sem gerentes diretos).

### 4. Análise de Nulos na Coluna `Super_ssn`
* Ao filtrar a coluna `Super_ssn` na tabela `employee`, constatou-se que o colaborador **James Borg** possui valor `null`.
* **Ação:** O registro foi mantido intacto, pois James Borg é o CEO / Gerente Geral da empresa e não possui supervisor acima dele na hierarquia.

### 5 & 6. Verificação de Departamentos Sem Gerente
* Inspecionadas as colunas `Mgr_ssn` e `Mgr_start_date` da tabela `department`. Todos os departamentos foram devidamente associados aos seus respectivos gerentes.

### 7. Verificação das Horas dos Projetos (`works_on`)
* Na tabela `works_on`, verificada a coluna `Hours`. Nulos e registros com 0.0 foram analisados para garantir a consistência das horas alocadas por projeto.

### 8. Separação de Colunas Complexas (`Address`)
* A coluna `Address` da tabela `employee` continha informações compostas no formato `Número-Rua-Cidade-Estado` (ex: `450-Stone-Houston-TX`).
* Foi utilizada a função de **Dividir Coluna por Delimitador (`-`)**, gerando 4 colunas individuais:
  * `Número`
  * `Rua`
  * `Cidade`
  * `Estado`

### 9. Mescla de `employee` e `department` (Nome do Departamento)
* Realizada a junção entre a tabela `employee` (coluna `Dno`) e `department` (coluna `Dnumber`) através do tipo **Externa Esquerda (Left Outer)**.
* Expandida a coluna `Dname` para associar o nome de cada departamento diretamente ao colaborador.

### 10. Junção de Colaboradores e seus Gerentes (Self-Join)
* Efetuada uma junção da tabela `employee` com ela mesma (**Self-Join**), relacionando a coluna `Super_ssn` com a coluna `Ssn`.
* Expandido o nome do supervisor e renomeado para **`Nome Gerente`**. O valor nulo do CEO foi tratado para exibir `"Sem Gerente (CEO)"`.

### 11. Concatenação de Nome e Sobrenome
* As colunas `Fname` e `Lname` da tabela `employee` foram mescladas com separador de espaço para criar a coluna unificada **`Nome Completo`**.

### 12. Mescla de Nomes de Departamentos e Localizações
* Na tabela `dept_locations`, foi realizada uma mescla com `department` usando `Dnumber` para trazer o nome do departamento (`Dname`).
* Em seguida, as colunas `Dname` e `Dlocation` foram mescladas com separador `" - "`, gerando a coluna **`Departamento_Localizacao`** (ex: `Research - Bellaire`). Essa combinação garante identificadores únicos para modelo dimensional estrela (Star Schema).

---

## ❓ Tarefa 13: Por que utilizar MESCLAR (Merge) e não ATRIBUIR (Append)?

> **Explicação Teórica:**
> 
> * **MESCLAR (Merge):** Realiza uma junção **horizontal** baseada em chaves relacionais (semelhante ao `JOIN` em SQL). É utilizado quando desejamos adicionar colunas de uma tabela a outra, enriquecendo os atributos de uma mesma entidade/linha.
> * **ATRIBUIR (Append):** Realiza uma junção **vertical** (semelhante ao `UNION ALL` em SQL), empilhando linhas de tabelas diferentes que possuem a mesma estrutura de colunas.
> 
> **Conclusão:** Como as tabelas `dept_locations` e `department` possuem atributos diferentes e o objetivo era associar o nome do departamento à sua respectiva localização lado a lado na mesma linha, apenas a operação de **Mesclar (Merge)** é aplicável.

---

### 14. Agrupamento de Dados por Gerente
* Criada uma consulta agrupada na tabela `employee` por `Nome Gerente`, contando o número de linhas para responder à pergunta de negócio: **"Quantos colaboradores existem sob a gestão de cada gerente?"**.

### 15. Eliminação de Colunas Desnecessárias
* Removidas colunas redundantes e chaves secundárias desnecessárias para otimizar o desempenho do modelo no Power BI.

---

## 🚀 Como Carregar no Power BI

1. Abra o **Power BI Desktop**.
2. Clique em **Obter Dados ➔ Excel** e selecione o arquivo [`dados_desafio_company_transformado.xlsx`](dados_desafio_company_transformado.xlsx).
3. Selecione as tabelas transformadas e clique em **Carregar** ou **Transformar Dados**.
4. *(Opcional)* Se preferir conectar ao banco relacional via SQL, utilize o script [`script_bd_company.sql`](script_bd_company.sql) em uma instância MySQL local ou Azure MySQL.

---

## 👨‍💻 Autor
## Daiene Cristina em 13/09/2026
Projeto elaborado como parte dos requisitos práticos do Bootcamp de Power BI Analyst da **Digital Innovation One (DIO)**.
