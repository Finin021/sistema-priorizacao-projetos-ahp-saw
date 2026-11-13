<div align="center">

<h1>Sistema de Apoio à Decisão para Priorização de Projetos</h1>
<h3>baseado nos métodos AHP e SAW</h3>

<p>
<img src="https://img.shields.io/badge/Python-3.10%2B-3776AB?style=for-the-badge&logo=python&logoColor=white" alt="Python">
<img src="https://img.shields.io/badge/SQLite-003B57?style=for-the-badge&logo=sqlite&logoColor=white" alt="SQLite">
<img src="https://img.shields.io/badge/pandas-150458?style=for-the-badge&logo=pandas&logoColor=white" alt="pandas">
<img src="https://img.shields.io/badge/NumPy-013243?style=for-the-badge&logo=numpy&logoColor=white" alt="NumPy">
<img src="https://img.shields.io/badge/Matplotlib-11557C?style=for-the-badge" alt="Matplotlib">
<img src="https://img.shields.io/badge/openpyxl-217346?style=for-the-badge&logo=microsoftexcel&logoColor=white" alt="openpyxl">
</p>

<p><b>Trabalho de Conclusão de Curso · Engenharia da Computação · Universidade Veiga de Almeida</b><br>
Victor Guilherme Moreira Pinto · Orientador: Prof. Vitor Amadeu</p>

<p>
<a href="#visão-geral">Visão geral</a> ·
<a href="#resultados">Resultados</a> ·
<a href="#critérios-e-pesos">Critérios</a> ·
<a href="#banco-de-dados">Banco de dados</a> ·
<a href="#como-executar">Como executar</a> ·
<a href="#validação">Validação</a> ·
<a href="#limitações">Limitações</a>
</p>

<br>

<table>
<tr align="center">
<td width="160"><h2>4.000</h2>projetos processados</td>
<td width="160"><h2>5</h2>critérios no AHP</td>
<td width="160"><h2>0,0074</h2>razão de consistência</td>
<td width="160"><h2>6</h2>tabelas no SQLite</td>
</tr>
</table>

</div>

<br>

> [!WARNING]
> Os dados utilizados são **simulados**. Os resultados demonstram o funcionamento do método e **não representam projetos reais**.

## Visão geral

O sistema **ordena projetos por necessidade de atenção ao risco**:

- os **pesos** dos critérios vêm do método **AHP** (*Analytic Hierarchy Process*), com verificação de consistência;
- a **pontuação** de cada projeto vem da **soma ponderada** (**SAW**, *Simple Additive Weighting*);
- cada **execução** fica registrada em um banco **SQLite**, o que permite rastrear quais julgamentos geraram quais pesos e qual *ranking*.

<div align="center">

<table>
<tr align="center">
<td><b>1 · Dados</b><br>Kaggle<br>4.000 projetos</td>
<td>➜</td>
<td><b>2 · Tratamento</b><br>5 critérios</td>
<td>➜</td>
<td><b>3 · AHP</b><br>pesos · CI · CR</td>
<td>➜</td>
<td><b>4 · Normalização</b><br>x/máx · mín/x</td>
<td>➜</td>
<td><b>5 · SAW</b><br>pontuação<br>ranking</td>
</tr>
<tr align="center">
<td colspan="9">▼</td>
</tr>
<tr align="center">
<td colspan="3"><b>Saídas</b><br>SQLite · CSV · Excel · gráficos · log</td>
<td colspan="3"></td>
<td colspan="3"><b>Validação</b><br>testes · cenários · sensibilidade</td>
</tr>
</table>

</div>

<br>

Uma pontuação maior indica **maior necessidade de atenção ao risco**. Ela não indica um projeto melhor nem mais rentável.

## Resultados

<div align="center">

<table>
<thead>
<tr><th align="left">Item</th><th align="center">Valor</th></tr>
</thead>
<tbody>
<tr><td>Projetos processados</td><td align="center"><b>4.000</b></td></tr>
<tr><td>λmax</td><td align="center">5,0331</td></tr>
<tr><td>CI (Índice de Consistência)</td><td align="center">0,0083</td></tr>
<tr><td>CR (Razão de Consistência)</td><td align="center"><b>0,0074</b> · consistente (limite de 0,10)</td></tr>
</tbody>
</table>

<br>

<table>
<thead>
<tr><th align="center">Posição</th><th align="left">Projeto</th><th align="center">Pontuação</th></tr>
</thead>
<tbody>
<tr><td align="center">1º</td><td>PROJ_3003</td><td align="center">0,6687</td></tr>
<tr><td align="center">2º</td><td>PROJ_2001</td><td align="center">0,6322</td></tr>
<tr><td align="center">3º</td><td>PROJ_1837</td><td align="center">0,6296</td></tr>
</tbody>
</table>

</div>

### Peso nominal × influência efetiva

<p align="center">
  <img src="resultados/pesos_vs_influencia.png" alt="Pesos do AHP e influência efetiva de cada critério" width="820">
</p>

O critério C1 tem peso de 37,6% e responde por cerca de 47,7% da variação da pontuação. O C5 tem peso de 7,4% e influência de cerca de 2%. **O peso do AHP não corresponde, necessariamente, à influência efetiva no resultado**: ela também depende da dispersão dos dados e da normalização.

<details>
<summary><b>Mais gráficos</b></summary>

<p align="center">
  <img src="resultados/top20_contribuicoes.png" alt="Contribuição de cada critério nos 20 primeiros projetos" width="820">
</p>

<p align="center">
  <img src="resultados/distribuicao_pontuacao.png" alt="Distribuição da pontuação SAW" width="820">
</p>

</details>

## Critérios e pesos

<div align="center">

<table>
<thead>
<tr>
<th align="center">Código</th>
<th align="left">Campo</th>
<th align="left">Interpretação adotada</th>
<th align="left">Sentido</th>
<th align="center">Normalização</th>
<th align="center">Peso AHP</th>
</tr>
</thead>
<tbody>
<tr><td align="center">C1</td><td><code>Complexity_Score</code></td><td>Complexidade do projeto</td><td>Maior = mais risco</td><td align="center">x/máx</td><td align="center"><b>37,60%</b></td></tr>
<tr><td align="center">C2</td><td><code>Schedule_Pressure</code></td><td>Pressão sobre o cronograma</td><td>Maior = mais risco</td><td align="center">x/máx</td><td align="center"><b>21,47%</b></td></tr>
<tr><td align="center">C3</td><td><code>Historical_Risk_Incidents</code></td><td>Incidentes históricos de risco</td><td>Maior = mais risco</td><td align="center">x/máx</td><td align="center"><b>21,47%</b></td></tr>
<tr><td align="center">C4</td><td><code>Resource_Availability</code></td><td>Disponibilidade de recursos</td><td>Menor = mais risco</td><td align="center">mín/x</td><td align="center"><b>12,09%</b></td></tr>
<tr><td align="center">C5</td><td><code>Vendor_Reliability_Score</code></td><td>Confiabilidade dos fornecedores</td><td>Menor = mais risco</td><td align="center">mín/x</td><td align="center"><b>7,38%</b></td></tr>
</tbody>
</table>

</div>

<br>

> [!NOTE]
> A documentação da base não detalha o significado e a escala desses campos. A interpretação acima é uma **hipótese do autor**.

### Fórmulas

**Pesos (AHP):** autovetor principal da matriz de comparação *A*, com os pesos somando 1.

```math
A\,w = \lambda_{\max}\,w, \qquad \sum_{i=1}^{n} w_i = 1
```

```math
CI = \frac{\lambda_{\max} - n}{n - 1}, \qquad CR = \frac{CI}{RI} \qquad (RI = 1{,}12 \text{ para } n = 5)
```

**Normalização:** nos critérios em que maior valor significa mais risco (C1, C2 e C3) e nos critérios em que menor valor significa mais risco (C4 e C5), respectivamente:

```math
r_{ij} = \frac{x_{ij}}{\max_j(x_{ij})} \qquad\qquad r_{ij} = \frac{\min_j(x_{ij})}{x_{ij}}
```

**Pontuação (SAW):**

```math
S_j = \sum_{i=1}^{n} w_i \cdot r_{ij}
```

Os projetos são ordenados da maior para a menor pontuação e, em caso de empate, pelo identificador.

### Julgamentos do AHP

Os julgamentos foram feitos pelo autor, em caráter experimental e sem consulta a especialistas. Cada um está justificado em <a href="documentacao/julgamentos_ahp.json"><code>documentacao/julgamentos_ahp.json</code></a>. Para testar outros pesos, edite esse arquivo e execute o programa novamente. Se o CR passar de 0,10, a execução é interrompida.

## Banco de dados

O arquivo `resultados/priorizacao.db` é um banco relacional **SQLite** com seis tabelas.

<div align="center">

<table>
<thead>
<tr><th align="left">Tabela</th><th align="left">O que armazena</th></tr>
</thead>
<tbody>
<tr><td><code>projetos</code></td><td>Identificador de cada projeto e os valores dos cinco campos utilizados</td></tr>
<tr><td><code>criterios</code></td><td>Código, campo, nome e sentido de preferência de cada critério</td></tr>
<tr><td><code>execucoes</code></td><td>Cada execução, com data e hora, número de projetos, λmax, CI, CR, situação de consistência e versão dos pesos</td></tr>
<tr><td><code>comparacoes_ahp</code></td><td>Comparações par a par usadas em cada execução</td></tr>
<tr><td><code>pesos</code></td><td>Peso de cada critério em cada execução</td></tr>
<tr><td><code>resultados</code></td><td>Posição, pontuação e valores usados por cada projeto em cada execução</td></tr>
</tbody>
</table>

</div>

### Relacionamentos

<div align="center">

<table>
<thead>
<tr><th align="left">Tabela de origem (1)</th><th align="center">Relação</th><th align="left">Tabela relacionada (N)</th><th align="left">Campo de ligação</th></tr>
</thead>
<tbody>
<tr><td><code>projetos</code></td><td align="center">1 ➜ N</td><td><code>resultados</code></td><td><code>project_id</code></td></tr>
<tr><td><code>execucoes</code></td><td align="center">1 ➜ N</td><td><code>resultados</code></td><td><code>execucao_id</code></td></tr>
<tr><td><code>execucoes</code></td><td align="center">1 ➜ N</td><td><code>pesos</code></td><td><code>execucao_id</code></td></tr>
<tr><td><code>execucoes</code></td><td align="center">1 ➜ N</td><td><code>comparacoes_ahp</code></td><td><code>execucao_id</code></td></tr>
<tr><td><code>criterios</code></td><td align="center">1 ➜ N</td><td><code>pesos</code></td><td><code>criterio</code></td></tr>
<tr><td><code>criterios</code></td><td align="center">1 ➜ N</td><td><code>comparacoes_ahp</code></td><td><code>criterio_a</code> e <code>criterio_b</code></td></tr>
</tbody>
</table>

</div>

<br>

As tabelas `comparacoes_ahp`, `pesos` e `resultados` usam o campo `execucao_id` para identificar a execução a que pertencem. Isso preserva a rastreabilidade: **dados → comparações → pesos → execução → resultado**.

<details>
<summary><b>Colunas de cada tabela</b></summary>

<br>

<div align="center">

<table>
<thead>
<tr><th align="left">Tabela</th><th align="left">Colunas</th></tr>
</thead>
<tbody>
<tr><td><code>projetos</code></td><td><b>project_id</b> (PK), complexity_score, schedule_pressure, historical_risk_incidents, resource_availability, vendor_reliability_score</td></tr>
<tr><td><code>criterios</code></td><td><b>codigo</b> (PK), campo, nome, sentido</td></tr>
<tr><td><code>execucoes</code></td><td><b>id</b> (PK), data_hora, n_projetos, lambda_max, ci, cr, consistente, observacao</td></tr>
<tr><td><code>comparacoes_ahp</code></td><td>execucao_id (FK), criterio_a, criterio_b, valor</td></tr>
<tr><td><code>pesos</code></td><td>execucao_id (FK), criterio, peso</td></tr>
<tr><td><code>resultados</code></td><td>execucao_id (FK), project_id, ranking, pontuacao, valores_usados</td></tr>
</tbody>
</table>

</div>

<br>

PK = chave primária · FK = chave estrangeira

</details>

O banco guarda a execução principal. Os resultados da sensibilidade e dos cenários de pesos ficam no Excel e no *log*. O repositório inclui uma cópia do `priorizacao.db` gerada pela execução apresentada no trabalho.

<details>
<summary><b>Consultas SQL de exemplo</b></summary>

```sql
-- 10 primeiros da última execução
SELECT ranking, project_id, pontuacao
FROM resultados
WHERE execucao_id = (SELECT MAX(id) FROM execucoes)
ORDER BY ranking
LIMIT 10;

-- Pesos da última execução
SELECT c.codigo, c.nome, p.peso
FROM pesos p
JOIN criterios c ON c.codigo = p.criterio
WHERE p.execucao_id = (SELECT MAX(id) FROM execucoes)
ORDER BY p.peso DESC;

-- Consistência de cada execução
SELECT id, data_hora, n_projetos, lambda_max, ci, cr, consistente
FROM execucoes;
```

Para abrir o banco, use o **DB Browser for SQLite** ou a extensão *SQLite Viewer* do VS Code.

</details>

## Como executar

Requisitos: Python 3.10 ou superior.

```bash
# 1. Clonar o repositório
git clone https://github.com/Finin021/sistema-priorizacao-projetos-ahp-saw.git
cd sistema-priorizacao-projetos-ahp-saw

# 2. Criar e ativar um ambiente virtual (recomendado)
python -m venv .venv
.venv\Scripts\activate          # Windows
# source .venv/bin/activate     # Linux e Mac

# 3. Instalar as dependências
pip install -r requirements.txt

# 4. Executar
python src/main.py
```

O programa lê `dados/project_risk_raw_dataset.csv` e grava tudo em `resultados/`:

<div align="center">

<table>
<thead>
<tr><th align="left">Saída</th><th align="left">Conteúdo</th></tr>
</thead>
<tbody>
<tr><td><code>ranking_saw.csv</code></td><td>Ranking completo dos 4.000 projetos</td></tr>
<tr><td><code>resultados.xlsx</code></td><td>Matriz AHP, pesos, estatísticas, cenários e influência efetiva</td></tr>
<tr><td><code>priorizacao.db</code></td><td>Banco SQLite com as execuções</td></tr>
<tr><td><code>*.png</code></td><td>Gráficos</td></tr>
<tr><td><code>execucao.log</code></td><td>Registro da execução e dos testes</td></tr>
</tbody>
</table>

</div>

<br>

Cada execução adiciona um registro ao banco. Para começar do zero, apague `resultados/priorizacao.db` antes de executar.

<details>
<summary><b>Estrutura do repositório</b></summary>

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
│   ├── dados_preparados.csv       # derivado: Project_ID e as 5 colunas dos critérios
│   └── FONTE_DOS_DADOS.md
├── documentacao/
│   └── julgamentos_ahp.json       # julgamentos do AHP e justificativas
└── resultados/
    ├── ranking_saw.csv
    ├── resultados.xlsx
    ├── priorizacao.db
    ├── pesos_vs_influencia.png
    ├── top20_contribuicoes.png
    ├── distribuicao_pontuacao.png
    └── execucao.log
```

</details>

## Validação

O modelo é avaliado em cinco frentes, e todas rodam a cada execução.

**1. Testes automatizados (7).** Cálculo manual conferido, direção de preferência, CR ≤ 0,10, pesos positivos que somam 1, ordem do *ranking*, pontuações em (0, 1] e concordância entre autovetor e média das colunas.

**2. Cenários controlados.** Projetos sintéticos com resultado esperado definido antes da execução, construídos com os valores extremos da própria base.

<div align="center">

<table>
<thead>
<tr>
<th align="left">Cenário</th>
<th align="left">Construção</th>
<th align="left">Esperado</th>
<th align="left">Obtido</th>
</tr>
</thead>
<tbody>
<tr><td>S1 · Dominância</td><td>Maior risco em todos os critérios</td><td>1º lugar</td><td>1º lugar</td></tr>
<tr><td>S2 · Dominado</td><td>Menor risco em todos os critérios</td><td>Últimas posições</td><td>4.004º de 4.004</td></tr>
<tr><td>S3 × S4 · Conflito</td><td>S3: só a complexidade em maior risco. S4: só recursos e fornecedores em maior risco</td><td>S3 à frente de S4</td><td>S3 = 0,419<br>S4 = 0,256</td></tr>
</tbody>
</table>

</div>

<br>

**3. Análise de sensibilidade.** 200 simulações, com cada peso multiplicado por um fator sorteado entre 0,8 e 1,2 e renormalização para somar 1 (semente 42).

<div align="center">

<table>
<thead>
<tr><th align="left">Medida</th><th align="center">Média</th><th align="center">Mínimo</th></tr>
</thead>
<tbody>
<tr><td>Correlação de Spearman com o ranking base</td><td align="center">0,9966</td><td align="center">0,9864</td></tr>
<tr><td>Projetos do top 100 mantidos</td><td align="center">90,4%</td><td align="center">81%</td></tr>
</tbody>
</table>

</div>

<br>

**4. Cenários de pesos.** Ranking recalculado com pesos iguais e com cada critério assumindo 50% do peso total.

<div align="center">

<table>
<thead>
<tr><th align="left">Cenário</th><th align="center">Spearman vs. base</th><th align="center">Top 100 em comum</th></tr>
</thead>
<tbody>
<tr><td>Pesos iguais</td><td align="center">0,909</td><td align="center">60</td></tr>
<tr><td>C1 com 50%</td><td align="center">0,960</td><td align="center">68</td></tr>
<tr><td>C2 com 50%</td><td align="center">0,740</td><td align="center">13</td></tr>
<tr><td>C3 com 50%</td><td align="center">0,682</td><td align="center">39</td></tr>
<tr><td>C4 com 50%</td><td align="center">0,583</td><td align="center">25</td></tr>
<tr><td>C5 com 50%</td><td align="center">0,843</td><td align="center">40</td></tr>
</tbody>
</table>

</div>

<br>

**5. Comparação exploratória.** A pontuação é comparada ao campo `Risk_Level` da base (Spearman de aproximadamente 0,33). Como a origem desse campo é desconhecida, a comparação é **exploratória e não constitui validação definitiva**.

## Limitações

- Os dados são **simulados** e os campos **não têm dicionário oficial detalhado**; a interpretação dos critérios é uma hipótese do autor.
- Os julgamentos do AHP foram feitos **apenas pelo autor**. Um CR baixo mostra coerência interna, não validade externa.
- A normalização usa os valores extremos do conjunto, então incluir ou excluir projetos pode alterar a pontuação dos demais.
- O peso nominal de um critério **não é igual** à sua influência efetiva no *ranking*.
- Os resultados **não devem ser generalizados** para projetos reais.

**Trabalhos futuros:** validar os julgamentos com gestores, aplicar o modelo a dados reais anonimizados, comparar com outros métodos (como o TOPSIS) e outras normalizações, e desenvolver uma interface web.

## Dados e licença

- **Base:** *Project management Risk Raw*, de Tarek Adam, no <a href="https://www.kaggle.com/datasets/ka66ledata/project-management-risk-raw">Kaggle</a>.
- **Licença dos dados:** <a href="https://cdla.dev/permissive-1-0/">CDLA-Permissive-1.0</a>.
- `dados/project_risk_raw_dataset.csv` é a cópia original, sem alterações.
- `dados/dados_preparados.csv` é um arquivo **derivado**, gerado pelo programa, com o `Project_ID` e as cinco colunas dos critérios.

Mais detalhes em <a href="dados/FONTE_DOS_DADOS.md"><code>dados/FONTE_DOS_DADOS.md</code></a>.

<details>
<summary><b>Referências</b></summary>

- ADAM, Tarek. *Project management Risk Raw*. Kaggle, 2025. Disponível em: https://www.kaggle.com/datasets/ka66ledata/project-management-risk-raw.
- ELBOK, Ghizlane; BERRADO, Abdelaziz. Project Prioritization for Portfolio Selection using MCDA. In: International Conference on Industrial Engineering and Operations Management, 2020. IEOM Society, 2020. Disponível em: http://www.ieomsociety.org/ieom2020/papers/808.pdf.
- SAATY, Thomas L. How to make a decision: the analytic hierarchy process. *European Journal of Operational Research*, v. 48, n. 1, p. 9-26, 1990. DOI: 10.1016/0377-2217(90)90057-I.
- SAATY, Thomas L. Decision making with the analytic hierarchy process. *International Journal of Services Sciences*, v. 1, n. 1, p. 83-98, 2008.
- SPRAGUE, Ralph H. A Framework for the Development of Decision Support Systems. *MIS Quarterly*, v. 4, n. 4, p. 1-26, 1980. DOI: 10.2307/248957.
- TAHERDOOST, Hamed; MADANCHIAN, Mitra. Multi-Criteria Decision Making (MCDM) Methods and Concepts. *Encyclopedia*, v. 3, n. 1, p. 77-87, 2023. Disponível em: https://www.mdpi.com/2673-8392/3/1/6.
- TRIANTAPHYLLOU, Evangelos. *Multi-criteria decision making methods: a comparative study*. Dordrecht: Kluwer Academic Publishers, 2000. (Applied Optimization, v. 44).
- TRIANTAPHYLLOU, Evangelos; SÁNCHEZ, Alfonso. A sensitivity analysis approach for some deterministic multi-criteria decision-making methods. *Decision Sciences*, v. 28, n. 1, p. 151-194, 1997.
- VARGAS, Ricardo Viana. Using the Analytic Hierarchy Process (AHP) to Select and Prioritize Projects in a Portfolio. In: PMI Global Congress, 2010. Disponível em: https://www.pmi.org/learning/library/analytic-hierarchy-process-prioritize-projects-6608.

</details>

## Como citar

```text
PINTO, Victor Guilherme Moreira. Sistema de Apoio à Decisão para Priorização de
Projetos baseado nos Métodos AHP e SAW: código-fonte. [S. l.]: GitHub, 2026.
Disponível em: https://github.com/Finin021/sistema-priorizacao-projetos-ahp-saw.
```

## Uso de inteligência artificial

Durante a elaboração deste Trabalho de Conclusão de Curso, o autor utilizou as ferramentas de Inteligência Artificial Generativa Microsoft Copilot, *powered by GPT-5.6*, desenvolvido pela Microsoft, e Claude Sonnet 5.5, desenvolvido pela Anthropic, exclusivamente como instrumentos de apoio à estruturação do texto, à revisão da redação e ao desenvolvimento e à documentação do código-fonte. Todo o conteúdo gerado ou sugerido foi revisado, validado e editado pelo autor, que assume integral responsabilidade por ele.

---

<div align="center">

<p><b>English summary</b> · Decision support system that ranks projects by risk-attention priority using <b>AHP</b> (weights with a consistency check, CR ≤ 0.10) and <b>SAW</b> (weighted sum), with every run stored in <b>SQLite</b>. It uses a synthetic public dataset (Kaggle, CDLA-Permissive-1.0), so results demonstrate the method and do not represent real projects. Main run: 4,000 projects, CR = 0.0074.</p>

<p><code>pip install -r requirements.txt</code> · <code>python src/main.py</code></p>

</div>
