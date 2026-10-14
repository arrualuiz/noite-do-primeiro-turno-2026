# RETOMAR — seguir a partir daqui

> Ponto de parada: **10/10/2026. Case 21 escrito** (o mapa do voto x o mapa da soja, com o resultado por município), commitado localmente e ainda **sem push nem publicação no site**, aguardando revisão do texto.
> 1º turno encerrado (100% das seções às 02:59): **Flávio 47,03% x Lula 45,16%**; 2º turno em 25/10. Coletor parado; site no ar desde 06/10.
> Para o contexto completo do projeto, leia [`CONTEXTO.md`](CONTEXTO.md). Este arquivo diz só **onde paramos e o que falta**.

## Como o coletor funciona (para o 2º turno)

No 1º turno, o coletor ficou ligado em segundo plano a noite toda (não depende do navegador nem do editor aberto):

```bash
python3 -u coleta/coletar.py --a-cada 180 --print --git --parar-no-fim   # PID em dados/coleta.pid
```

- Coleta o TSE a cada 3 min, atualiza CSVs, banco, exportação, painel, `site/dados.js` e prints.
- **Commita e envia ao GitHub sozinho** a cada 30 min (`Dados: coleta automática…`).
- **Desliga sozinho** quando o TSE finalizar a totalização (`tf = s`) ou após 1 h sem novidade com ≥ 99,9%, com um commit final.
- Para se o computador desligar ou suspender. Para conferir: `python3 coleta/status.py` e `tail dados/coleta.log`.
- Para parar à mão: `kill $(cat dados/coleta.pid)`.

## Checklist ao voltar

1. [x] Coleta encerrada com 100% (o PC suspendeu de 00:38 a 06:40; o coletor gravou o final ao acordar).
2. [x] **Mover o projeto para `~/dev/projetos/xx-noite-do-primeiro-turno-2026`** (só com o coletor parado): `bash mover-para-dev.sh --teste` e depois `bash mover-para-dev.sh`. O script (local, fora do git) também leva junto as anotações do assistente de código. Depois, abra a pasta nova no editor e ajuste o número `xx`.
3. [x] **Fechar os desfechos dos cases** (`cases/*.md`, seção "Desfecho"), com o resultado final:
   - 01, 03: placar e projeção das 18:18 x final
   - 05, 06, 13: quanto o Flávio precisava; palpites 47 x 42 e 47 x 44 x final
   - 08, 09, 11: saldo real de BA e SP; viés da projeção no fim
   - 12, 15: qual método de estimativa da diferença final chegou mais perto
   - 16, 17, 18: 1º turno registrado; completar depois do 2º turno (25/10)
4. [x] `python3 analise/revisao.py` dá os números para os desfechos.
5. [x] Atualizar a linha do tempo do `CONTEXTO.md` com o resultado final.
6. [x] Revisar o texto do último passo do site (`site/index.html`, `#passo-final`; mexido nos commits de site de 07/10).
7. [x] Commit + push; site publicado em docs/ (GitHub Pages) em 06–07/10.
8. [ ] **Case 21:** revisar o texto (`cases/21-mapa-do-voto-e-da-soja.md`). Depois: `python3 site/publicar.py`, trocar "20 análises" por "21" na linha do site no `README.md`, commit e push.

## Ideias pendentes (próximas etapas)

- **1º turno de 2018** (a comparação mais direta com 2026): reconstruir a curva pelos boletins de urna do TSE (hora de recebimento de cada seção). Validar primeiro em 2022, contra a curva do UOL. Ver o case 14.
- **Projeção por município**, para corrigir o viés "voto tardio mais favorável ao Lula dentro do estado" (case 11). O resultado final por município já está em `dados/export/municipios.csv` (case 21); falta a ordem de chegada das seções.
- **Case 21, próximos passos:** refazer com o 2º turno; testar a origem dos moradores com outra medida (o Censo só conta quem nasceu no Sul, não os filhos de migrantes); incluir pecuária (PPM do IBGE) além da soja.
- **2º turno, 25/10/2026**: código TSE **6258**. Antes, separar a pasta de dados por turno e trocar `1turno` → `2turno` nas URLs do UOL em `coleta/coletar.py`. Referências já guardadas: 2º turno de 2018 (`dados/historico/2018-t2-g1-minuto.csv`) e de 2022 (`dados/uol-historico-2022-t2.json`, local).
- **Pesquisas de 2º turno** e apoios de Cury, Caiado e Renan Santos, para testar os cenários do case 17.
- **Matéria em R**: instalar o R (`sudo apt install r-base r-cran-tidyverse r-cran-rsqlite r-cran-dbi`), rodar `R/01-explorar-apuracao.R` (ainda não testado) e virar um relatório Quarto.

## Para mostrar (entrevista)

- **Site:** abrir `site/index.html` no navegador (funciona sem internet, exceto as bibliotecas D3/marked do CDN). Tem a história em rolagem, os prints de cada marco e as 20 análises.
- **Painel:** `painel/painel.html`, com todos os gráficos da noite.
- **GitHub:** https://github.com/arrualuiz/noite-do-primeiro-turno-2026. O histórico de commits mostra a construção passo a passo. Os prints não estão lá (são telas de terceiros); o site no GitHub abre sem eles.

## Para retomar

1. Ler este arquivo e o `CONTEXTO.md`.
2. Rodar `python3 coleta/status.py` para ver em que pé está.
3. Fechar os desfechos dos cases com o resultado final.

O padrão continua o mesmo: cada análise vira um case em `cases/`, e cada etapa um commit com push.
