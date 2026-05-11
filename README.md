# Trabalho DataOps — Controle de Qualidade de Dados com Great Expectations

**MBA Engenharia de Dados — FIAP**  
**Disciplina:** DataOps  
**Alunos:** Vinicius Martins Cordeiro, Fabio Rizzi, Rodrigo Brito, Carlos Santos  
**Data:** Maio de 2026

---

## Objetivo

Implementar um pipeline de **controle de qualidade de dados** usando o framework
[Great Expectations (GX Core 0.18)](https://docs.greatexpectations.io) sobre o dataset
`curso` — tabela com dados acadêmicos de alunos do MBA.

Os problemas de qualidade são introduzidos **propositalmente** no dataset para que
os testes os detectem e evidenciem o valor de cada categoria de validação.

---

## Problemas de Qualidade Simulados

| Problema | Coluna | Descrição |
|----------|--------|-----------|
| Ambiguidade semântica | `NOTA_MAT_1` | Nota zero = não cursou OU tirou zero? |
| Nulos inconsistentes | `NOTA_MAT_4` | 30 alunos ainda não cursaram (NULL intencional) |
| Domínio não padronizado | `INGLES` | `sim`, `SIM`, `S`, `yes`, string vazia — mesma informação, grafias diferentes |
| Formato de data | `DATA_MATRICULA` | 10 registros em `DD/MM/YYYY` ao invés de `YYYY-MM-DD` |
| Inconsistência entre colunas | `REPROVACOES_MAT_4` | 4 alunos com reprovação em matéria que ainda não cursaram |

---

## Categorias de Testes Implementadas

| # | Categoria | O que valida | Resultado |
|---|-----------|-------------|-----------|
| 1 | **Schema** | Colunas presentes e tipos de dados corretos | ✅ 12/12 |
| 2 | **Volume** | Quantidade de registros dentro do esperado | ✅ 1/1 |
| 3 | **Valores Numéricos e Datas** | Ranges, média, desvio padrão, datas válidas | ⚠️ 12/13 |
| 4 | **Formato** | Nulos, regex, comprimento de strings | ⚠️ 6/9 |
| 5 | **Unicidade** | Chave primária, proporção de valores distintos | ✅ 4/4 |
| 6a | **Integridade Referencial** | Domínio de INGLES — dado cru | ❌ 0/1 |
| 6b | **Integridade Referencial** | Domínio de INGLES — após normalização | ✅ 2/2 |

**Total: 37/42 expectativas passaram (88.1%)**  
As falhas são **esperadas** — cada uma demonstra a detecção de um problema real.

---

## Estrutura do Projeto

```
Trabalho_DataOps/
├── dataops_qualidade_dados.ipynb   ← notebook principal
├── data/
│   └── curso.csv                   ← dataset com 200 registros e problemas intencionais
└── gx/
    ├── great_expectations.yml      ← configuração principal do projeto GX
    ├── expectations/
    │   └── suite_qualidade_dados.json  ← suite de regras versionada no Git
    └── uncommitted/
        └── data_docs/local_site/
            └── index.html          ← relatório HTML (Data Docs)
```

---

## Como Executar

### Pré-requisitos

```bash
pip install great-expectations pandas numpy
```

### Executar o Notebook

1. Clone o repositório:
   ```bash
   git clone https://github.com/vmartinscordeiro/Trabalho_DataOps.git
   cd Trabalho_DataOps
   ```

2. Abra o notebook no Jupyter:
   ```bash
   jupyter notebook dataops_qualidade_dados.ipynb
   ```

3. Execute todas as células em ordem (`Kernel > Restart & Run All`).

4. O relatório **Data Docs** será aberto automaticamente no browser ao final.

---

## Modelo de Maturidade

Os testes de qualidade devem ser introduzidos a partir do **Nível 2 — Gerenciado/Repetível**.

| Nível | Nome | Pipeline | Testes de Qualidade |
|-------|------|----------|--------------------|
| 1 | Reativo / Ad-hoc | SQL manual, phpMyAdmin | Não — problemas descobertos por acaso |
| **2** | **Gerenciado / Repetível** | **ETL automatizado, versionado** | **SIM — ponto crítico** |
| 3 | Definido / Padronizado | dbt, Airflow, CI/CD | Sim — integrados ao pipeline |
| 4 | Mensurável / Quantitativo | SLAs, observabilidade | Sim — monitoramento contínuo |
| 5 | Otimizado | ML, self-service, automação | Sim — qualidade como produto |

No **Nível 1** (cenário do Lab com phpMyAdmin), problemas são tolerados porque o analista vê os dados diretamente. No **Nível 2**, a carga passa a ser automatizada e um dado ruim se propaga para todos os consumidores downstream sem aviso.

---

## Tecnologias

- Python 3.x
- [Great Expectations 0.18](https://docs.greatexpectations.io) (GX Core)
- Pandas 2.x
- NumPy 1.26

---

## Referências

- Great Expectations — The Beginner's Guide to Data Quality Tests (material do curso)
- [Documentação oficial do Great Expectations](https://docs.greatexpectations.io)
- DAMA-DMBOK: Data Management Body of Knowledge
