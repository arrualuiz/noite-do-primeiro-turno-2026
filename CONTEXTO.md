# CONTEXTO — Noite do Primeiro Turno 2026

> Documento de passagem: tudo o que alguém (inclusive eu no futuro) precisa para entender, avaliar e continuar este projeto sem ter acompanhado a noite.
> Repositório: https://github.com/arrualuiz/noite-do-primeiro-turno-2026 · Atualizado em 10/10/2026, com o resultado final do 1º turno e o voto por município (case 21).

## 1. O que é

Projeto pessoal de jornalismo de dados feito **ao vivo**, durante a apuração do 1º turno da eleição presidencial de 04/10/2026. Começou às 18h07 com uma pergunta: o que o placar com 12% das urnas apuradas (Flávio Bolsonaro à frente) realmente dizia? Virou um sistema que:

1. coleta os resultados oficiais do TSE a cada publicação;
2. guarda tudo (JSON originais, CSV, banco SQLite);
3. compara as leituras ao longo da noite (placar, projeção, lotes, estados);
4. registra cada dúvida e a resposta como um **case**.

**Objetivo final:** um site de rolagem (scrollytelling) que conta o desenrolar da noite, com os marcos, palpites, leituras e gráficos, para o site pessoal do autor. Também é material para matérias de dados em R.

**Candidatos principais:** Flávio Bolsonaro (PL, nº 22) e Lula (PT, nº 13). Há outros 10 candidatos, que somam ~8% dos válidos.

## 2. Linha do tempo (marcos)

| Hora | % seções* | Marco |
|---|---|---|
| 18:07 | 12% | Pergunta inicial: Flávio na frente (51,09% x 40,71% no UOL). Dá para concluir algo? |
| 18:13 | 12,45% | Primeiro registro manual (prints UOL/g1) |
| 18:18 | 20,8% | Início da coleta automática do TSE. **Projeção por estado já dava 49,0%: 2º turno** |
| 18:45 | — | Bug descoberto: % de urnas inflado (lido do arquivo errado). Corrigido com os brutos |
| 18:46 | 47,1% | Primeiro lote claramente abaixo do que o Flávio precisava (48,1% recalculado; 48,8% na leitura da hora; precisava de 49,7%) |
| 18:48 | — | Palpite do autor: **Flávio 47% x Lula 42%** |
| 19:14 | 69,8% | TSE volta de 25 min de pausa com lote gigante (20,6 mi de válidos). Flávio abaixo de 50% |
| 19:28 | 73,6% | Descoberta: os estados publicam antes do nacional. Coletor passa a somar as UFs |
| 19:14–20:04 | — | Arquivo nacional do TSE parado por 50 min; sites mostravam placar ~20 p.p. atrasado |
| 20:10 | 87,1% | Revisão das ideias: a projeção erra sistematicamente a favor do Flávio |
| 20:14 | 89,1% | Lote mais favorável ao Lula da noite (52,0% x 41,1%). Linhas não devem se cruzar |
| 20:21 | 90,5% | Flávio 48,03% x Lula 43,99% |
| **20:57** | **97,2%** | **TSE marca a eleição como matematicamente definida (`md = s`): 2º turno Flávio x Lula** |
| 21:32 | 99,4% | Reta final: 46 x 45 já não é possível; final esperado ~47,1 x 45,1 (case 20) |
| 00:38–06:40 | — | Computador suspenso; o coletor retoma ao acordar e grava o resultado final |
| **02:59** | **100%** | **Última seção totalizada. Resultado final: Flávio 47,03% x Lula 45,16%** |

\* % das seções pela soma das UFs (ver §5).

## 3. Principais achados

1. **O placar parcial não é o resultado:** a ordem de apuração regional (Sul primeiro, Nordeste depois) favoreceu o Flávio no começo. 51,1% às 18:18 → 47,03% no resultado final.
2. **A projeção por estado antecipou o 2º turno** 2h39 antes da confirmação oficial (20:57). Às 18:18, ela errou o resultado final do Flávio por 1,98 p.p.; o placar da mesma hora, por 4,08.
3. **Mas a projeção erra para o lado do Flávio:** dentro de cada estado, o voto apurado por último é mais favorável ao Lula (SP: margem prevista +15,4, real +9,8). No fim, o erro acumulado desde 19:14 chegou a ~1,9 milhão de votos.
4. **O que importa é o lote, não o acumulado:** a virada de tendência apareceu nos lotes (18:46) antes do placar (19:14).
5. **Saldo x percentual:** até 19:14, os lotes davam saldo de votos ao Flávio e, ao mesmo tempo, derrubavam o percentual dele. Depois de 19:14, os lotes passaram a dar saldo ao Lula.
6. **O TSE publica os estados antes do nacional:** somar as UFs dá um placar mais atual que o dos sites (g1/UOL mostram o arquivo nacional).
7. **Estimativa da diferença final** (case 12): entre 1,5 e 3 p.p. a favor do Flávio. **Real: +1,87 p.p.** O método mais preciso foi o mais simples, aplicar a reta final de 2022 (+1,50); as projeções com dados de 2026 erraram todas a favor do Flávio.
8. **Resultado final do 1º turno** (100% das seções, 05/10 02:59): **Flávio Bolsonaro 47,03%** (56.104.503) x **Lula 45,16%** (53.879.538); Cury 2,89%, Renan Santos 2,24%, Caiado 2,18%, Zema 0,27%. Abstenção de **21,08%**, a maior de um 1º turno desde 2002. Da coleta das 19:14 até o fim, o saldo real do Lula foi de 3,26 mi, contra 1,41 mi esperados pela projeção por estado.
9. **O mapa do voto e o mapa da soja** (case 21, com o resultado por município): no país, onde a soja ocupa mais de 15% do território, o Flávio abriu +3,41 mi. Mas, comparados aos municípios sem soja do mesmo estado e porte, os de mais soja votaram só +1,7 a +2,9 p.p. mais no PL (no Sul, menos). No oeste baiano, só Luís Eduardo Magalhães deu vitória ao Flávio; São Desidério e Formosa do Rio Preto, as maiores áreas de soja do Nordeste, deram ~60% ao Lula. No Norte, a parcela de moradores nascidos no Sul (Rondônia) separa o voto mais que a soja. De 2022 para 2026, os municípios com soja se moveram mais para o PL que os outros.

Detalhes, números e método de cada um em `cases/` (21 cases).

## 4. Arquitetura

```
TSE (JSON) ─► coleta/coletar.py (a cada 3 min) ─► dados/brutos/<AAAAMMDD-HHMMSS>/  (fonte da verdade, só local)
                     │
                     ├─► dados/snapshots.csv, estados.csv, candidatos.csv   (resumos)
                     ├─► analise/banco.py    ─► dados/apuracao.sqlite      (banco, formato longo)
                     ├─► analise/exportar.py ─► dados/export/*.csv + site/dados.js
                     ├─► painel/painel.py    ─► painel/painel.html         (painel local)
                     └─► coleta/prints.js    ─► prints do g1 em cada pasta bruta

Visões (leem os dados e comparam):
  analise/analisar.py   tendência e projeção no terminal
  analise/variacao.py   estado a estado entre duas leituras; saldo esperado do que falta
  analise/revisao.py    confere as ideias (cases) contra a última coleta
  analise/municipios.py voto por município (TSE, dados abertos) x soja, área e origem dos moradores (IBGE)
  coleta/status.py      saúde da coleta
  R/01-explorar-apuracao.R   exemplo em R (dplyr + ggplot2)
```

Python usa só a biblioteca padrão. Node + Puppeteer + Chrome só para os prints.

## 5. Fonte dos dados e cuidados

- **TSE:** `https://resultados.tse.jus.br/oficial/ele2026/6257/dados/<uf>/<uf>-c0001-e006257-u.json`
  - `6257` = Presidente 1º turno; **`6258` = Presidente 2º turno** (25/10/2026). Lista em `.../oficial/comum/config/ele-c.json`.
  - `<uf>` = `br`, `ac` … `to`, `zz` (exterior). Campos: `s` (seções), `e` (eleitorado, comparecimento, abstenção), `v` (válidos, brancos, nulos), `carg[0].agr[].par[].cand[]` (votos por candidato: `n` número, `vap` votos, `pvapn` %).
- **Cuidados aprendidos:**
  - Não misture arquivos: o `br-e006257-ab.json` (andamento) fica **à frente** do `-u.json` (votos). Número e denominador sempre do mesmo arquivo.
  - O arquivo **nacional** (`br`) atrasa em relação aos estados. O placar nacional deste projeto é a **soma das 27 UFs + exterior**.
  - O TSE às vezes para de publicar por 20–50 min. Não é falha do coletor.
- **UOL:** curva de evolução de 2026 e de 2022 (`stc.eleicoes2026.uol.com/...`), usada só no gráfico comparativo. A página do UOL bloqueia navegador automatizado (HTTP 403); não contornamos.
- **g1:** prints do placar e do mapa (evidência visual).

## 6. Métodos

| Medida | Definição |
|---|---|
| % seções | Σ seções apuradas ÷ Σ seções totais (soma das UFs) |
| Fração apurada da UF | eleitorado das seções apuradas ÷ eleitorado da UF |
| Válidos finais estimados | válidos ÷ fração apurada |
| **Projeção por estado** | Σ_UF (votos do candidato ÷ fração apurada) ÷ Σ_UF (válidos ÷ fração apurada) |
| Projeção corrigida | o que falta em cada UF × margem dos votos que entraram depois das 19:14 |
| Quanto precisa | (alvo × válidos finais − votos atuais) ÷ (válidos finais − válidos atuais) |
| Lote | diferença entre duas coletas consecutivas (votos novos de cada candidato) |
| Saldo do lote | novos do Flávio − novos do Lula |
| Saldo esperado do que falta | válidos que faltam na UF × (% Flávio − % Lula) na UF |

**Limitações:** a projeção ignora as diferenças dentro do estado (capital x interior). Uma versão melhor usaria município ou zona eleitoral. Nada aqui é previsão oficial.

## 7. Dicionário de dados (`dados/export/`, CSV UTF-8 com BOM, decimal com ponto)

| Arquivo | Grão | Colunas principais |
|---|---|---|
| `coletas.csv` | coleta | `coleta_id` (AAAAMMDD-HHMMSS), `horario`, `versao_tse_br` |
| `ufs.csv` | UF | `uf`, `nome`, `regiao` |
| `candidatos.csv` | candidato | `numero`, `nome`, `partido` |
| `apuracao.csv` | coleta × abrangência | `secoes_total/apuradas/pct`, `eleitorado`, `eleitorado_apurado`, `comparecimento`, `abstencao`, `votos_total`, `validos`, `brancos`, `nulos` (inclui `BR` = arquivo nacional oficial) |
| `votos.csv` | coleta × UF × candidato | `horario`, `uf`, `nome_uf`, `regiao`, `numero`, `candidato`, `partido`, `votos`, `pct_validos` |
| `nacional.csv` | coleta | `secoes_pct`, `validos`, `votos_flavio/lula`, `pct_flavio/lula`, `dif_votos`, `dif_pp`, `proj_flavio/lula` |
| `municipios.csv` | município (5.571, sem o exterior) | `ibge`, `tse` (códigos), `uf`, `regiao`, `municipio`, `validos/flavio/lula_2026`, `margem_2026_pp` (PL − PT), `validos/bolsonaro/lula_2022`, `margem_2022_pp`, `area_km2`, `soja_ha_2025`, `pct_soja_territorio`, `populacao_2022`, `nascidos_sul_2022`, `pct_nascidos_sul` (gerado por `analise/municipios.py`, 1º turno) |
| `lotes.csv` | par de coletas × abrangência | `de`, `ate`, `abrangencia` (`BR` = soma), `validos_novos`, `novos_flavio/lula/outros`, `saldo_flavio_menos_lula`, `pct_lote_flavio/lula`, `margem_acumulada_antes_pp` |

Histórico oficial 2002–2022 (TSE, dados abertos): `dados/historico/presidente_turnos.csv` (ano × turno: aptos, comparecimento, abstenção, brancos, nulos, válidos) `presidente_votos.csv` (ano × turno × partido: votos, %) e `presidente_votos_uf.csv` (ano × turno × UF × partido: votos), gerados por `analise/historico_tse.py`; `dados/historico/2018-t2-g1-minuto.csv` (evolução minuto a minuto do 2º turno de 2018).

Outros: `dados/palpites.csv` (palpites registrados), `dados/apuracao.sqlite` (mesmo conteúdo, com as views `v_votos` e `v_nacional_soma`), `notas.md` (diário), `cases/` (análises).

Modelo estrela para BI: `votos` e `apuracao` são fatos; `coletas`, `ufs` e `candidatos` são dimensões (chaves `coleta_id`, `uf`, `numero`).

## 8. Como conectar

URL base dos arquivos no GitHub (repositório público):
`https://raw.githubusercontent.com/arrualuiz/noite-do-primeiro-turno-2026/main/dados/export/`

**R**
```r
library(readr); library(dplyr)
base <- "https://raw.githubusercontent.com/arrualuiz/noite-do-primeiro-turno-2026/main/dados/export/"
votos <- read_csv(paste0(base, "votos.csv"))
# ou o banco local: DBI::dbConnect(RSQLite::SQLite(), "dados/apuracao.sqlite")
```

**Python**
```python
import pandas as pd
base = "https://raw.githubusercontent.com/arrualuiz/noite-do-primeiro-turno-2026/main/dados/export/"
votos = pd.read_csv(base + "votos.csv")
lotes = pd.read_csv(base + "lotes.csv")
# sem pandas: import sqlite3; sqlite3.connect("dados/apuracao.sqlite")
```

**Power BI**: Obter dados → Web (cole a URL de cada CSV) ou Texto/CSV (pasta local `dados/export/`). Na importação, use a localidade **Inglês (EUA)** para o decimal com ponto. Relacione `votos[coleta_id]→coletas`, `votos[uf]→ufs`, `votos[numero]→candidatos`.

**Power Automate**: ação HTTP (GET) na URL raw do CSV → "Analisar CSV" ou gravar numa tabela do Excel/SharePoint. Gatilho por agendamento ou por push no GitHub (conector GitHub).

**Google Sheets**: `=IMPORTDATA("https://raw.githubusercontent.com/arrualuiz/noite-do-primeiro-turno-2026/main/dados/export/nacional.csv")`. Para automatizar, use Apps Script com `UrlFetchApp.fetch(url)` + `Utilities.parseCsv`.

**Outra pessoa**: entregue este arquivo + `cases/` + os CSVs de `dados/export/` (ou o link do repositório).

## 9. Como retomar

```bash
git clone https://github.com/arrualuiz/noite-do-primeiro-turno-2026 && cd noite-do-primeiro-turno-2026
npm install                                   # só para os prints
python3 coleta/coletar.py --a-cada 180 --print --git --parar-no-fim   # 1º turno (6257); --git commita sozinho, --parar-no-fim desliga no fim
python3 coleta/coletar.py --a-cada 180 --print --eleicao 6258   # 2º turno
python3 coleta/status.py · python3 analise/revisao.py · xdg-open painel/painel.html
```
**Pendências para o 2º turno:** separar a pasta de dados por turno (hoje `dados/` é do 1º turno) e trocar as URLs do UOL (`1turno` → `2turno`) em `coleta/coletar.py`.

**Para fechar o 1º turno:** quando a apuração chegar a 100%, preencher o campo "Desfecho" de cada case (placar final x projeções x palpite).

## 10. Perguntas abertas (para avaliar ou estudar)

- Qual método de estimativa da diferença final chegou mais perto (case 12)?
- O viés "voto tardio mais favorável ao Lula dentro do estado" aparece também por município? (dados por município estão nos arquivos `-v.json`/zona do TSE, não coletados aqui)
- Uma projeção que use a tendência dos lotes, e não o acumulado, teria acertado antes?
- Como a reta final de 2026 se compara à de 2022, ponto a ponto?
- No voto por município, a origem dos moradores (migração do Sul) separa o voto da fronteira agrícola melhor que a soja (case 21)? O Censo só conta quem nasceu no Sul, não os filhos de migrantes.
