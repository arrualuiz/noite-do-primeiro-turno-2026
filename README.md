# Noite do Primeiro Turno 2026

O desenrolar da noite do primeiro turno da eleição para Presidente (04/10/2026), contado com dados: coleta automática direto da API pública do TSE, com histórico salvo a cada publicação, projeção por estado e um painel HTML que se atualiza sozinho.

**🔗 Site:** https://arrualuiz.github.io/noite-do-primeiro-turno-2026/ (a noite em rolagem, o painel e as 20 análises)

**Resultado do 1º turno (100% das seções):** Flávio Bolsonaro 47,03% x Lula 45,16%; 2º turno em 25/10/2026.

O projeto foi feito **durante a noite da apuração**. Os CSVs em `dados/` são o registro real do que foi coletado, e o `notas.md` é o diário das observações feitas no caminho.

**Comece por aqui:** [`RETOMAR.md`](RETOMAR.md) diz onde paramos e o que falta; [`CONTEXTO.md`](CONTEXTO.md) tem o projeto inteiro (linha do tempo, achados, métodos, dicionário de dados e como conectar em R, Python, Power BI, Power Automate e Sheets). As 21 análises estão em [`cases/`](cases/) e o site de rolagem em [`site/index.html`](site/index.html).

![Painel da apuração com o resultado final](docs/painel.png)

## O que ele faz

- **Coleta**: a cada 3 min consulta os 29 arquivos de `resultados.tse.jus.br` (27 UFs, exterior e nacional). Grava quando qualquer um muda e guarda os JSON originais de cada coleta.
- **Histórico**: guarda o placar nacional (soma das UFs), o andamento e o placar de cada estado e todos os candidatos em cada UF, em CSV e num banco SQLite.
- **Projeção por estado**: supõe que o que falta de cada estado vota igual ao que já foi apurado nele. Isso corrige o viés da ordem de chegada das urnas: o Sul é apurado antes do Nordeste, o que infla o placar parcial de um lado.
- **"Quanto precisa"**: calcula o % dos válidos restantes que o líder precisa para fechar acima de 50% e vencer no 1º turno.
- **Painel** (`painel/painel.html`): placar, evolução 2026 x 2022, curva da diferença, projeção x placar, saldo de cada atualização (geral e por estado), mapa, andamento por estado e região, e tabela. Funciona no modo claro, no escuro e no celular.
- **Análises** (`cases/`): 20 análises feitas a partir de dúvidas reais durante a noite, cada uma com pergunta, dados completos, gráfico, método, conclusão e o desfecho comparado ao resultado final. A 21ª veio depois, com o resultado por município: o mapa do voto x o mapa da soja.
- **Site** (`site/index.html`): a noite contada em rolagem, com o gráfico avançando a cada marco, os prints do momento e as análises.
- **Histórico**: resultados oficiais de 2002 a 2022 (abstenção e votos por turno e por estado) dos dados abertos do TSE, e a evolução minuto a minuto do 2º turno de 2018.
- **Prints**: a cada coleta, salva prints do placar e do mapa do g1 como evidência visual.

## Estrutura

```
coleta/    coletar.py · prints.js · status.py      → busca os dados e cuida da coleta
analise/   analisar.py · variacao.py · revisao.py · banco.py · exportar.py → compara, monta o banco e exporta
painel/    painel.py · template.html               → gera painel/painel.html
R/         01-explorar-apuracao.R                   → análise em R (dplyr + ggplot2)
cases/     uma análise por dúvida da noite          → base do site final
site/      index.html · estilo.css · dados.js · cases.js → site de rolagem
dados/     CSVs + apuracao.sqlite + export/ (+ brutos locais)
legado/    registrar.py · registrar_estado.py      → registro manual do começo da noite
notas.md   diário da apuração
```

| Arquivo | Função |
|---|---|
| `coleta/coletar.py` | Coleta do TSE e grava os CSVs; também gera o painel, atualiza o banco e tira os prints |
| `coleta/prints.js` | Prints do placar e do mapa do g1 com Puppeteer |
| `coleta/status.py` | Saúde da coleta: processo vivo, intervalo entre coletas, pastas completas |
| `analise/analisar.py` | Análise no terminal: tendência, quanto falta, projeção |
| `analise/variacao.py` | Estado a estado: o que entrou entre duas leituras, tendência do lote e saldo esperado do que falta |
| `analise/revisao.py` | Confere as ideias da noite (os cases) contra a coleta mais recente |
| `analise/graficos.py` | Gera os gráficos SVG dos cases em `cases/graficos/` a partir do banco |
| `analise/exportar.py` | Exporta o banco para `dados/export/*.csv` (R, Python, Power BI…) e `site/dados.js` |
| `analise/historico_tse.py` | Histórico oficial 2002–2022 (abstenção, votos por turno) a partir dos dados abertos do TSE |
| `analise/municipios.py` | Voto por município (2022 e 2026) cruzado com soja, área e origem dos moradores (IBGE) → `dados/export/municipios.csv` (case 21) |
| `analise/banco.py` | Monta o banco SQLite `dados/apuracao.sqlite` a partir dos JSON brutos (incremental) |
| `painel/painel.py` + `painel/template.html` | Gera o `painel/painel.html` a partir dos dados |
| `R/01-explorar-apuracao.R` | Exemplo em R: placar, saldo por lote, por estado e por região |
| `site/index.html` + `site/estilo.css` | Site de rolagem: gráfico fixo que avança com os marcos da noite, prints e as análises |
| `cases/` | Pequenas análises feitas durante a noite, uma por dúvida (ver `cases/README.md`) |
| `legado/` | Registro manual, usado antes da coleta automática |

### Dados

| CSV | Uma linha por… |
|---|---|
| `snapshots.csv` | coleta: horário, % de seções apuradas, % e votos de Flávio e Lula |
| `estados.csv` | coleta × UF: % apurado, eleitorado, % de Flávio e Lula |
| `candidatos.csv` | coleta × abrangência (BR/UF) × candidato: votos e % |
| `palpites.csv` | palpite registrado, com a projeção do mesmo momento |
| `export/*.csv` | tabelas prontas para R, Python, Power BI e planilhas (dicionário no `CONTEXTO.md`) |
| `historico/*.csv` | resultados oficiais 2002–2022 e o 2º turno de 2018 minuto a minuto |

## Como rodar

Python 3 usa só a biblioteca padrão. Node.js e Google Chrome só são necessários para os prints.

```bash
npm install                                  # puppeteer-core (só para os prints)

python3 coleta/coletar.py                    # uma coleta
python3 coleta/coletar.py --a-cada 180 --print  # coleta contínua com prints
python3 coleta/status.py                     # está tudo ok?
python3 analise/analisar.py                  # análise no terminal
python3 analise/variacao.py                  # o que mudou por estado entre as 2 últimas coletas
python3 analise/revisao.py                   # as ideias da noite ainda se confirmam?
python3 analise/variacao.py --todas SP       # evolução de um estado em todas as coletas
python3 analise/municipios.py                # voto por município x soja (case 21)
python3 site/publicar.py                     # monta a versão pública em docs/ (GitHub Pages)
xdg-open painel/painel.html                  # painel (recarrega a cada minuto)
```

Para deixar rodando em segundo plano:

```bash
nohup python3 -u coleta/coletar.py --a-cada 180 --print --git --parar-no-fim >> dados/coleta.log 2>&1 &
echo $! > dados/coleta.pid
kill $(cat dados/coleta.pid)                 # parar
```

Se o Chrome não estiver em `/usr/bin/google-chrome`, defina `CHROME_PATH`.

## Banco de dados e R

`dados/apuracao.sqlite` é atualizado a cada coleta, em formato longo, pronto para `dplyr`/`ggplot2`:

| Tabela / view | Uma linha por… |
|---|---|
| `coletas` | coleta (horário, versão do arquivo nacional do TSE) |
| `apuracao` | coleta × abrangência (BR, UF, ZZ): seções, eleitorado, válidos, brancos, nulos, abstenção |
| `votos` | coleta × abrangência × candidato: votos e % dos válidos |
| `candidatos`, `ufs` | número/nome/partido; UF/nome/região |
| `v_votos` | votos já com horário, região e nome do candidato |
| `v_nacional_soma` | nacional pela soma das UFs (mais atual que o arquivo BR do TSE) |

```r
library(DBI); library(dplyr)
con <- dbConnect(RSQLite::SQLite(), "dados/apuracao.sqlite")
tbl(con, "v_votos") |> filter(abrangencia != "BR", numero %in% c(13, 22)) |> collect()
```

Instalar R no Ubuntu: `sudo apt install r-base r-cran-tidyverse r-cran-rsqlite r-cran-dbi`. Exemplo completo em `R/01-explorar-apuracao.R` (ainda não testado: o R não estava instalado na máquina da coleta).

## Fontes

- **TSE**: `resultados.tse.jus.br/oficial/ele2026/6257/dados/<uf>/<uf>-c0001-e006257-u.json`, com votos por candidato, seções apuradas, eleitorado, brancos, nulos e abstenção. Eleição 6257 = Presidente, 1º turno.
- **TSE, dados abertos** (`cdn.tse.jus.br/estatistica/sead/odsele`): resultados oficiais de 2002 a 2026, por município em 2022 e 2026.
- **IBGE** (SIDRA e API de malhas): área plantada de soja por município (PAM 2025), área territorial e lugar de nascimento dos moradores (Censo 2022).
- **UOL**: curva de evolução da apuração de 2026 e de 2022, usada só nos gráficos comparativos.
- **g1**: prints do placar e do mapa, e o infográfico com a evolução minuto a minuto do 2º turno de 2018.

## Lições da noite

- **O TSE publica dois arquivos que não andam juntos.** O `-ab.json` (andamento) fica alguns minutos **à frente** do `-u.json` (votos). No começo eu lia o % de urnas do primeiro e os votos do segundo, e o % apurado ficava inflado (44% em vez de 36,6%). A correção foi ler tudo do mesmo arquivo. Como os JSON brutos estavam guardados, deu para recalcular o histórico inteiro.
- **O placar parcial não é o resultado.** Com cerca de 20% apurado, o placar dava 51% para o líder, mas a projeção por estado já dava ~49%, porque o Sul estava muito mais apurado que o Nordeste.
- **Os estados andam na frente do nacional.** O TSE publica cada estado antes de consolidar o arquivo nacional, que chegou a ficar 50 minutos parado. Somar as UFs deu um placar até ~20 pontos de apuração à frente do que os sites mostravam.
- **O voto apurado por último é diferente dentro de cada estado.** A projeção por estado errou sempre a favor do mesmo candidato: de 19:14 até o fim, o saldo real do Lula foi de 3,26 milhões de votos, contra 1,41 milhão esperado.
- **O método mais simples acertou mais.** Para a diferença final, aplicar a reta final de 2022 estimou +1,50 p.p.; o real foi +1,87. As projeções com dados de 2026 ficaram entre +2,4 e +3,0.
- **Guardar o bruto compensa.** Todo erro de processamento pôde ser corrigido depois, sem perder dados.

## Abordagem

Este é um exercício de dados, não de opinião: as análises descrevem como a apuração aconteceu, não qual resultado seria melhor. Os candidatos aparecem na ordem do resultado, e as cores seguem as dos partidos (azul para o PL, vermelho para o PT), como nos portais de apuração.

## Limitações

- A projeção assume que o restante de cada estado vota igual ao já apurado nele. Diferenças entre capital e interior dentro do estado não entram na conta, e esse viés foi medido: ~1,9 milhão de votos de 19:14 até o fim.
- Não é previsão oficial. É um exercício de coleta e análise de dados.
