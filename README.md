# ProKnow-C: Pipeline Automatizado para Revisão Sistemática de Literatura

Implementação computacional do protocolo ProKnow-C (*Knowledge Development Process – Constructivist*) para seleção de portfólio bibliográfico em revisões sistemáticas de literatura.

## Descrição

Este repositório contém scripts em R que automatizam as Fases 1 e 2 do método ProKnow-C, originalmente proposto por Ensslin et al. (2010). O pipeline integra exportações das bases Web of Science e Scopus, realiza deduplicação algorítmica e aplica o funil matemático para seleção de artigos.

### Aplicação demonstrada

O pipeline foi validado em uma RSL sobre **Governo Aberto e Resiliência Organizacional em Consórcios Públicos Intermunicipais**, desenvolvida no Programa de Pós-Graduação em Contabilidade (PPGC/UFPA).

| Fase | Etapa | Resultado |
|------|-------|-----------|
| Importação | Web of Science | 121 registros |
| Importação | Scopus | 135 registros |
| | **Total bruto** | **256 registros** |
| Deduplicação | Duplicatas removidas | 84 registros |
| | **Registros únicos** | **172 registros** |
| Triagem | Alinhados por título | 26 registros |
| Funil | Elite Pareto (80% citações) | 10 artigos |
| Funil | Estado da Arte (≥2024) | 14 artigos |
| Funil | Repescagem por autoria | 0 artigos |
| | **Portfólio pré-leitura** | **24 artigos** |
| Acesso | Artigos inacessíveis | 3 artigos |
| | **Portfólio final** | **21 artigos** |

## Estrutura do repositório

```
.
├── scripts/
│   ├── fase1_triagem.R      # Importação WoS + Scopus, deduplicação, matriz de triagem
│   └── fase2_funil.R        # Funil Pareto / recentes / repescagem
├── docs/
│   ├── Figura1.drawio       # Fluxograma do funil ProKnow-C (editável em app.diagrams.net)
│   ├── exclusoes.md         # Registro de exclusões e justificativas
│   ├── LICENSE-CC-BY.md     # Licença do conteúdo em docs/
│   └── session_info.txt     # Versões de R/pacotes validadas
├── data/
│   ├── raw/                 # Exports brutos (ver seção "Dados")
│   └── processed/           # Planilhas geradas pelos scripts
├── renv.lock                # Ambiente reprodutível (R 4.6.1, 35 pacotes)
├── ProKnow_C_com_Bibliometrix.Rproj
├── CITATION.cff
└── LICENSE                  # MIT (código)
```

## Requisitos

- R ≥ 4.3.0
- Pacotes: dplyr, readxl, writexl, stringr, readr, here

## Instalação e uso

```r
# 1. Clone o repositório
git clone https://github.com/gishorus/proknowc-r.git

# 2. Restaure o ambiente
renv::restore()

# 3. Coloque os exports brutos em data/raw/:
#    - savedrecs.xls (Web of Science)
#    - scopus_export.csv (Scopus)

# 4. Execute o pipeline
source("scripts/fase1_triagem.R")
# Preencha a coluna Alinhamento_Titulo (1/0)
source("scripts/fase2_funil.R")
```

## Dados

Os exports brutos da Web of Science e Scopus não são redistribuídos neste repositório por restrição de licença dos provedores (Clarivate/Elsevier). As pastas `data/raw/` e `data/processed/` estão no `.gitignore`.

Para reproduzir os resultados, é necessário:
1. Acesso institucional às bases (neste projeto, via Portal de Periódicos da Capes/CAFe)
2. Replicar a busca com as strings documentadas no fluxograma (`docs/Figura1.drawio`)

**Parâmetros de busca:**
- Período: 2019–2026
- Data da coleta: 10 de maio de 2026
- Bases: Web of Science (Core Collection) e Scopus

## Deduplicação: critério documentado

A deduplicação usa duas etapas, nessa ordem:

1. **Por DOI normalizado.** Quando o mesmo DOI aparece nas duas bases, mantém-se a versão da Web of Science (critério de desempate arbitrário, mas fixo e auditável).

2. **Por título normalizado**, aplicada sobre o resultado da etapa 1. Necessária porque 3 dos 256 registros brutos tinham o mesmo artigo indexado com DOI ligeiramente diferente entre WoS e Scopus (variação de formatação ou erro de exportação em uma das bases).

## Reprodutibilidade

O pipeline foi validado com os dados reais desta pesquisa em outubro de 2026:
- Fase 1 reproduz 172 registros a partir dos 256 brutos
- Fase 2, aplicada sobre 26 artigos alinhados, reproduz 10 (Elite Pareto) + 14 (Estado da Arte) + 0 (Repescagem) = 24 artigos

Versões de R/pacotes estão em `renv.lock`. Restaure com `renv::restore()`.

## Funil ProKnow-C (resultado desta RSL)

```
256 → 172 (deduplicação)
    → 26 (triagem de títulos)
    → 24 (Elite Pareto + Estado da Arte)
    → 21 (3 artigos inacessíveis)
```

Ver `docs/Figura1.drawio` para o fluxograma completo e `docs/exclusoes.md` para justificativas das exclusões.

## Citação

Ver `CITATION.cff` ou cite como:

```bibtex
@software{soares2026proknowc,
  author = {Soares, Jeancarlos},
  title = {ProKnow-C: Pipeline Automatizado para Revisão Sistemática de Literatura},
  year = {2026},
  url = {https://github.com/gishorus/proknowc-r},
  version = {1.0.0}
}
```

## Referências metodológicas

- Ensslin, L., Ensslin, S. R., Lacerda, R. T. O., & Tasca, J. E. (2010). ProKnow-C, Knowledge Development Process – Constructivist. *Processo técnico com patente de registro pendente junto ao INPI*.

## Licença

- Código (`scripts/`): [MIT License](LICENSE)
- Diagrama e documentação (`docs/`): [CC-BY 4.0](docs/LICENSE-CC-BY.md)
