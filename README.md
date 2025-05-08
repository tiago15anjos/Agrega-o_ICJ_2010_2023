# Agrega-o_ICJ_2010_2023


Scripts, dados agregados e documentação da Iniciação Científica Júnior (CNPq) de 2010 a 2023. Inclui pipelines em Python para transformar o arquivo original `ICJ_CNPQ_2010_2023_LIMPO.xlsx` nas tabelas‑fato **ICJ_Municipio_Ano_2010_2023.xlsx** e **ICJ_Instituicao_Ano_bolsistas_2010_2023.xlsx**.

> Este README funciona também como **guia de interpretação** dos arquivos resultantes.

## 1. Visão Geral
Este repositório contém arquivos derivados do banco de dados **ICJ_CNPQ_2010_2023_LIMPO.xlsx**, que registra todas as bolsas de Iniciação Científica Júnior concedidas pelo CNPq entre 2010 e 2023. Os dados originais foram consolidados em duas tabelas‑fato para facilitar análises comparativas e integrações com outras bases (INEP, IBGE, etc.).

| Arquivo | Descrição rápida |
|---------|------------------|
| **ICJ_Municipio_Ano_2010_2023.xlsx** | Métricas agregadas por município e ano. |
| **ICJ_Instituicao_Ano_bolsistas_2010_2023.xlsx** | Métricas agregadas por município, ano, instituição e demais dimensões de programa. |

---

## 2. Processo de Transformação
1. **Entrada**: `ICJ_CNPQ_2010_2023_LIMPO.xlsx` (77 965 linhas).  
2. **Ferramentas**: Python 3.11, Pandas 2.x.  
3. **Passos principais**:
   - Leitura do arquivo original.
   - Definição das chaves de agregação.
   - Agrupamento (`groupby`) com cálculo de:
     - `size` → contagem de bolsistas.
     - `mean` e `sum` das variáveis financeiras e de produção.
   - Geração de colunas derivadas via `agg` e renomeação dos níveis.
   - Exportação para `.xlsx`.

Scripts completos encontram‑se em `scripts/01_make_fatos.py` (ver repositório).

---

## 3. Dicionário de Dados
### 3.1 ICJ_Municipio_Ano_2010_2023.xlsx
**Chaves**: `Regiao_Geografica`, `Unidade_Federacao`, `Nome_Municipio`, `Codigo_IBGE`, `Ano`, `Sigla_UF`, `Pais`

| Coluna | Tipo | Descrição |
|--------|------|-----------|
| Qtd_Bolsistas | int | Número de bolsistas naquele município‑ano. |
| Valor_Rs_mean | float | Valor médio da bolsa (R$ correntes) no ano. |
| Valor_Rs_sum | float | Soma total dos valores de bolsa (R$). |
| Valor_USS_mean | float | Valor médio em US$ (câmbio na data da concessão). |
| Valor_USS_sum | float | Soma total em US$. |
| Benef_Modal/Ano_mean | float | Média de benefícios por modalidade/ano. |
| Benef_Modal/Ano_sum | float | Soma total de benefícios. |
| Bolsa/Ano_mean | float | Média de bolsas por bolsista no ano. |
| Bolsa/Ano_sum | float | Total de bolsas pagas. |
| Auxílio/Ano_mean | float | Média de auxílios complementares. |
| Auxílio/Ano_sum | float | Soma total de auxílios. |
| Instituicao_lista* | str | Lista única de instituições beneficiadas (*campo opcional*). |

### 3.2 ICJ_Instituicao_Ano_bolsistas_2010_2023.xlsx
**Chaves**: todas acima **+** `Instituição`, `Programa`, `Modalidade`, `Area`, `Grande_Area`, `Linha_de_Fomento`, `Origem_do_Recurso`

| Coluna | Tipo | Descrição |
|--------|------|-----------|
| Qtd_Bolsistas_Inst | int | Número de bolsistas daquela instituição no município‑ano. |
| Valor_Rs_mean / sum | idem acima, restrito à instituição. |
| Valor_USS_mean / sum | idem. |
| Benef_Modal/Ano_mean / sum | idem. |
| Bolsa/Ano_mean / sum | idem. |
| Auxílio/Ano_mean / sum | idem. |

---

## 4. Convenções e Boas Práticas
- **Nomes de colunas**: sufixos `_mean` e `_sum` indicam, respectivamente, média aritmética e somatório.
- **Datas**: o campo `Ano` refere‑se ao ano de início da bolsa.
- **Moedas**: `Valor_Rs_*` em reais correntes; `Valor_USS_*` usa cotação registrada pelo CNPq no momento da concessão.
- **Valores nulos**: Não foram imputados; linhas com `NaN` permanecem para rastreabilidade.
- **Licenciamento**: Dados públicos do CNPq, tratados sob **CC‑BY 4.0**.

---

## 5. Exemplos de Uso
```python
import pandas as pd
mun = pd.read_excel("ICJ_Municipio_Ano_2010_2023.xlsx")
# Exemplo: média anual de bolsistas por UF
uf_summary = (
    mun.groupby("Sigla_UF")
        ["Qtd_Bolsistas"].mean()
        .sort_values(ascending=False)
)
```
Para cruzar com dados do INEP (ex.: número de escolas), utilize a chave composta `Codigo_IBGE` + `Ano`.

---

## 6. Contatos
Autor da transformação: **Tiago Ribeiro dos Anjos**  
Contato: tiago.anjos@ufscar.br  
Última atualização: 8 mai 2025


