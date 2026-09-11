# Coleta e Preparação de Dados em Saúde — Zika (SP) + IBGE

Projeto de **aquisição e preparação de dados** a partir de duas fontes de naturezas diferente, um arquivo público de saúde e uma API oficial com foco no que acontece **antes** da análise: coletar, inspecionar, limpar, documentar e integrar dados brutos até que se possa confiar neles.

> A ideia central que guia o projeto: **a qualidade do dado começa na coleta e não no pré-processamento.**

Este trabalho nasceu como atividade da disciplina de Aquisição e Preparação de Dados (Ciência de Dados — FMU), mas foi conduzido como um pequeno projeto de engenharia de dados, com decisões justificadas em cada etapa.

---

## O que este projeto demonstra

- Coleta de dados a partir de **arquivo local** (CSV exportado do DATASUS/TabNet) e de **API pública** (IBGE Localidades).
- Diagnóstico e tratamento de problemas reais de qualidade vindos da fonte: codificação (Latin-1 vs UTF-8), separador de colunas, linhas de cabeçalho/rodapé, valores agregados misturados ao detalhe e campos não atômicos.
- Leitura e "achatamento" de **JSON aninhado** retornado por API.
- **Padronização de chave** entre bases (código de município IBGE de 7 dígitos vs 6 dígitos do DATASUS) e integração das fontes por *join*.
- Documentação do **ciclo de vida do dado** (fonte, metadados, retenção) para cada fonte.

---

## Fontes de dados

| Fonte | Origem | Método de coleta | Formato original |
|---|---|---|---|
| Casos de Zika em SP (2026) | Ministério da Saúde / DATASUS — SINAN Net, via TabNet | Exportação manual em CSV | CSV (Latin-1, separador `;`) |
| Municípios de SP | IBGE — API de Localidades | Requisição HTTP (`requests`) | JSON aninhado |

Ambas são bases públicas e de acesso livre.

---

## Etapas do trabalho

**1. Estação A — Arquivo local (Zika/DATASUS).** O CSV do TabNet não é uma tabela limpa, e sim um relatório com a tabela no meio. Foram tratados: codificação Latin-1, separador `;`, 4 linhas de título e 22 de rodapé, uma linha de "Total" agregada aos dados (removida por regra, não por posição) e um campo que unia código IBGE e nome do município (dividido em duas colunas). Resultado salvo em UTF-8, separado por vírgula.

**2. Estação B — API (IBGE).** A resposta JSON (lista de 645 municípios, com camadas geográficas aninhadas) foi reduzida ao essencial , o código e nome. O código do IBGE (7 dígitos, com dígito verificador) foi padronizado para o formato de 6 dígitos usado pelo DATASUS, e convertido para texto, para permitir o cruzamento.

**3. Integração (etapa extra).** As duas tabelas foram cruzadas pelo código do município via *join* interno. O resultado retornou exatamente as 55 linhas esperadas, validando a padronização: todos os códigos encontraram correspondência. O cruzamento evidenciou por que a junção foi feita por código, e não por nome (grafias como "ARACATUBA" e "Araçatuba" não casariam por texto).

---

## Arquivos do repositório

| Arquivo | Descrição |
|---|---|
| `notebook.ipynb` | Notebook consolidado, com o código e a narração de cada etapa |
| `zika_sp_2026_bruto.csv` | Dado bruto original do TabNet, preservado como fonte da verdade |
| `zika_limpo.csv` | Tabela de Zika após tratamento |
| `municipios_ibge_sp.csv` | Tabela de referência do IBGE, padronizada |
| `relatorio.docx` | Relatório descritivo do processo e das decisões |

---

## Ferramentas

`Python` · `Pandas` · `requests` · `Jupyter` · `DATASUS/SINAN` · `API IBGE`

---

## Autor

**Kaike Martins Pereira** — Estudante de Ciência de Dados (FMU São Paulo).

[LinkedIn](https://linkedin.com/in/kaikemarttins) · [GitHub](https://github.com/kaike-martins)
