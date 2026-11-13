# Sistema de Apoio à Decisão para Priorização de Projetos baseado nos Métodos AHP e SAW

Protótipo desenvolvido como Trabalho de Conclusão de Curso de **Engenharia da Computação** (Universidade Veiga de Almeida), na área de **Banco de Dados e Sistemas de Informação**.

O sistema ordena projetos por **necessidade de atenção ao risco**. Os pesos dos critérios são obtidos pelo método **AHP** (*Analytic Hierarchy Process*) e a pontuação de cada projeto é calculada pela **soma ponderada** (**SAW**, *Simple Additive Weighting*). As execuções são gravadas em um banco **SQLite**.

- **Autor:** Victor Guilherme Moreira Pinto
- **Orientador:** Prof. Vitor Amadeu
- **Linguagem:** Python 3

> **Aviso:** os dados utilizados são **simulados**. Os resultados demonstram o funcionamento do método e **não representam projetos reais**.

## O que o sistema faz

1. Lê o arquivo CSV e confere a estrutura (4.000 registros e 51 colunas).
2. Valida e prepara os cinco campos usados como critérios.
3. Monta a matriz 5×5 do AHP a partir do arquivo de julgamentos.
4. Calcula os pesos (autovetor principal), λmax, CI e CR. **Se CR > 0,10, a execução é interrompida.**
5. Normaliza os critérios conforme o sentido de cada um.
6. Calcula a pontuação SAW e gera o *ranking*.
7. Executa testes automatizados, cenários controlados, análise de sensibilidade e cenários de pesos.
8. Grava os resultados em SQLite, CSV, Excel, gráficos e *log*.

## Critérios

| Código | Campo | Interpretação adotada | Sentido | Normalização |
|---|---|---|---|---|
| C1 | `Complexity_Score` | Complexidade do projeto | Maior = mais risco | x/máx |
| C2 | `Schedule_Pressure` | Pressão sobre o cronograma | Maior = mais risco | x/máx |
| C3 | `Historical_Risk_Incidents` | Incidentes históricos de risco | Maior = mais risco | x/máx |
| C4 | `Resource_Availability` | Disponibilidade de recursos | Menor = mais risco | mín/x |
| C5 | `Vendor_Reliability_Score` | Confiabilidade dos fornecedores | Menor = mais risco | mín/x |

A documentação da base não detalha o significado e a escala desses campos. A interpretação acima é uma **hipótese do autor**.

## Resultado da execução principal

| Item | Valor |
|---|---|
| Projetos processados | 4.000 |
| λmax | 5,0331 |
| CI | 0,0083 |
| CR | 0,0074 (julgamentos consistentes, limite de 0,10) |

| Critério | Peso AHP |
|---|---|
| C1 – Complexidade | 37,60% |
| C2 – Pressão sobre o cronograma | 21,47% |
| C3 – Incidentes históricos | 21,47% |
| C4 – Disponibilidade de recursos | 12,09% |
| C5 – Confiabilidade dos fornecedores | 7,38% |

Os julgamentos do AHP foram feitos pelo autor, em caráter experimental, e estão justificados em `documentacao/julgamentos_ahp.json`. Para testar outros pesos, edite esse arquivo e execute o programa novamente.

## Estrutura do repositório

```text
sistema-priorizacao-projetos-ahp-saw/
├── README.md
├── requirements.txt
├── .gitignore
├── src/
│   ├── main.py                    # orquestra todas as etapas
│   ├── config.py                  # caminhos, critérios, sentidos e valores de RI
│   ├── carregar_dados.py          # leitura do CSV e verificação da estrutura
│   ├── tratamento_dados.py        # validação dos campos e estatísticas
│   ├── ahp.py                     # matriz, pesos, λmax, CI e CR
│   ├── saw.py                     # normalização, pontuação e ranking
│   ├── database.py                # esquema SQLite e gravação das execuções
│   ├── validacao.py               # testes, sensibilidade, cenários e influência efetiva
│   └── cenarios_controlados.py    # projetos sintéticos com resultado esperado
├── dados/
│   ├── project_risk_raw_dataset.csv
│   └── FONTE_DOS_DADOS.md
├── documentacao/
│   └── julgamentos_ahp.json
└── resultados/                    # gerado pelo programa
    ├── ranking_saw.csv
    ├── resultados.xlsx
    ├── pesos_vs_influencia.png
    ├── top20_contribuicoes.png
    ├── distribuicao_pontuacao.png
    └── execucao.log
```

O banco `resultados/priorizacao.db` também é gerado a cada execução e não é versionado.

## Como executar

Requisitos: Python 3.10 ou superior.

```bash
# 1. Clonar o repositório
git clone https://github.com/Finin021/sistema-priorizacao-projetos-ahp-saw.git
cd sistema-priorizacao-projetos-ahp-saw

# 2. Criar e ativar um ambiente virtual (opcional, recomendado)
python -m venv .venv
.venv\Scripts\activate          # Windows
# source .venv/bin/activate     # Linux/Mac

# 3. Instalar as dependências
pip install -r requirements.txt

# 4. Executar
python src/main.py
```

As saídas são gravadas na pasta `resultados/`. Cada execução adiciona um novo registro ao banco SQLite. Para começar do zero, apague `resultados/priorizacao.db` antes de executar.

## Banco de dados (SQLite)

| Tabela | O que armazena |
|---|---|
| `projetos` | Identificador de cada projeto e os valores dos cinco campos utilizados |
| `criterios` | Código, campo, nome e sentido de preferência de cada critério |
| `execucoes` | Cada execução, com data e hora, número de projetos, λmax, CI, CR, situação de consistência e versão dos pesos |
| `comparacoes_ahp` | Comparações par a par usadas em cada execução |
| `pesos` | Peso de cada critério em cada execução |
| `resultados` | Posição, pontuação e valores usados por cada projeto em cada execução |

Para consultar o *ranking* da última execução:

```sql
SELECT ranking, project_id, pontuacao
FROM resultados
WHERE execucao_id = (SELECT MAX(id) FROM execucoes)
ORDER BY ranking
LIMIT 10;
```

O banco guarda a execução principal. Os resultados da análise de sensibilidade e dos cenários de pesos são gravados no arquivo Excel e no *log*.

## Validação

- **Testes automatizados:** cálculo manual conferido, direção de preferência, CR ≤ 0,10, pesos positivos que somam 1, ordem do *ranking*, pontuações em (0, 1] e concordância entre autovetor e média das colunas.
- **Cenários controlados:** projetos sintéticos com resultado esperado definido antes da execução (dominância, dominado e conflito entre critérios).
- **Análise de sensibilidade:** 200 simulações com variação de ±20% nos pesos (semente 42), comparadas ao *ranking* base por Spearman e pela retenção dos 100 primeiros.
- **Cenários de pesos:** pesos iguais e cada critério com 50% do peso total.
- **Comparação exploratória:** pontuação contra `Risk_Level`. Como a origem desse campo é desconhecida, não é validação definitiva.

## Limitações

- Os dados são simulados e os campos não têm dicionário oficial detalhado.
- Os julgamentos do AHP foram feitos apenas pelo autor, sem consulta a especialistas.
- A normalização usa os valores extremos do conjunto. Incluir ou excluir projetos pode alterar a pontuação dos demais.
- O peso nominal de um critério não é igual à sua influência efetiva no *ranking* (ver `resultados/pesos_vs_influencia.png`).

## Dados e licença

Base utilizada: **Project management Risk Raw**, de Tarek Adam, disponível no Kaggle em https://www.kaggle.com/datasets/ka66ledata/project-management-risk-raw, distribuída sob a licença **CDLA-Permissive-1.0** (https://cdla.dev/permissive-1-0/).

O arquivo `dados/project_risk_raw_dataset.csv` é a cópia original, sem alterações. O arquivo derivado `dados_preparados.csv`, gerado pelo programa, contém apenas o `Project_ID` e as cinco colunas dos critérios. Mais detalhes em [`dados/FONTE_DOS_DADOS.md`](dados/FONTE_DOS_DADOS.md).

## Referências

- ADAM, Tarek. *Project management Risk Raw*. Kaggle, 2025.
- SAATY, Thomas L. How to make a decision: the analytic hierarchy process. *European Journal of Operational Research*, v. 48, n. 1, p. 9-26, 1990.
- SAATY, Thomas L. Decision making with the analytic hierarchy process. *International Journal of Services Sciences*, v. 1, n. 1, p. 83-98, 2008.
- SPRAGUE, Ralph H. A Framework for the Development of Decision Support Systems. *MIS Quarterly*, v. 4, n. 4, p. 1-26, 1980.
- TRIANTAPHYLLOU, Evangelos. *Multi-criteria decision making methods: a comparative study*. Kluwer, 2000.
- TRIANTAPHYLLOU, Evangelos; SÁNCHEZ, Alfonso. A sensitivity analysis approach for some deterministic multi-criteria decision-making methods. *Decision Sciences*, v. 28, n. 1, p. 151-194, 1997.

A lista completa está no trabalho de conclusão de curso.

## Uso de inteligência artificial

Ferramentas de IA generativa (Microsoft Copilot e Claude) foram usadas como apoio à estruturação do texto, à revisão da redação e ao desenvolvimento e à documentação do código. O autor revisou e validou todo o conteúdo e assume a responsabilidade por ele.

---

## English summary

Decision support system that ranks projects by **risk-attention priority** using **AHP** (criteria weights, with a consistency check, CR ≤ 0.10) and **SAW** (weighted sum), with runs stored in **SQLite**. It uses a **synthetic** public dataset (Kaggle, CDLA-Permissive-1.0), so results demonstrate the method and do not represent real projects.

```bash
pip install -r requirements.txt
python src/main.py
```

Outputs (ranking CSV, Excel workbook, charts, log and SQLite database) are written to `resultados/`.
