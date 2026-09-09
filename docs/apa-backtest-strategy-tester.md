# APA-strategie backtesten met TradingView's Strategy Tester

`pine/apa-strategy.pine` is een testbare, versimpelde vertaling van
`docs/apa-signal-strategy.md` als TradingView-strategie. Dit gebruikt
TradingView's **eigen** historische data voor jouw MEXC-symbool (jaren
terug, geen 50-candle-limiet zoals bij de Crypto.com-connector) en de
ingebouwde Strategy Tester — geen losse historische data-export nodig.

## Bekende versimpelingen (belangrijk!)

- **Geen Volume Profile / POC** — niet native uit te lezen in Pine.
- **Geen supply/demand- of liquiditeitszones** — zou custom zone-detectie
  vereisen; nu niet meegenomen.
- **Take-profit = vaste R:R-multiple** (instelbaar, standaard 2R) in
  plaats van de eerstvolgende liquiditeitszone/swing-high/low uit de
  cursus.
- Impuls-detectie is een ATR-gebaseerde drempel (instelbaar), geen exacte
  cursus-definitie — die was nooit hard bevestigd (zie
  `docs/apa-signal-strategy.md`).

Dit is dus een **eerste testbare benadering**, geen 1-op-1 kopie van de
cursus. De uitkomst gebruiken we om de regels (en deze script-parameters)
samen te verfijnen.

## Laden in TradingView

1. Open TradingView Desktop, je MEXC BTC/USDT-symbool, timeframe **3m**
   (dit is de LTF waar de strategie op draait — HTF/Daily en MTF/15m
   worden intern opgehaald, daar hoef je de chart niet voor te wisselen).
2. Open de **Pine Editor** (tabblad onderaan het scherm).
3. Nieuw script → plak de inhoud van `pine/apa-strategy.pine`.
4. Klik **"Add to chart"** — de strategie draait nu automatisch terug
   over alle geladen historie op de chart.
5. Open het tabblad **"Strategy Tester"** (naast Pine Editor) voor
   Overview / Performance / List of Trades / Properties.

Hoe verder terug in de tijd de chart geladen is (blijven scrollen naar
links voordat je het script toevoegt), hoe langer de backtest-periode.

## Resultaten uitlezen via de tradingview-mcp

In je lokale Claude Code-sessie (met TradingView-MCP verbonden):
- `capture_screenshot` met region `strategy_tester` — visuele snapshot
  van de Performance/Overview-tabbladen.
- `data_get_pine_tables` — als de strategie tabel-achtige stats toont,
  anders handmatig aflezen via de screenshot.
- `chart_manage_indicator` / instellingen aanpassen kan via de Pine
  Editor zelf (niet via de MCP-tools) — pas een input aan (bijv.
  `rrMultiple`) en klik opnieuw "Add to chart" om te herladen.

Vraag bijvoorbeeld lokaal:
> "Open de Strategy Tester en maak een screenshot van de Performance-tab
> van de APA-strategie, en vat de belangrijkste cijfers samen (winrate,
> totaal aantal trades, profit factor, max drawdown, gemiddelde R:R)."

## Itereren

Vergelijk winrate/R:R per instelling (bijv. `impulseAtrMult`,
`rrMultiple`, `htfEmaLen`) en pas de parameters — en zo nodig
`docs/apa-signal-strategy.md` en de `/loop`-prompt in
`docs/apa-signal-loop-local.md` — in overleg aan op basis van wat de
backtest laat zien.
