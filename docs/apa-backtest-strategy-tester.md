# APA-strategie backtesten met TradingView's Strategy Tester

`pine/apa-strategy.pine` is een testbare, versimpelde vertaling van
`docs/apa-signal-strategy.md` als TradingView-strategie. Dit gebruikt
TradingView's **eigen** historische data voor jouw MEXC-symbool (jaren
terug, geen 50-candle-limiet zoals bij de Crypto.com-connector) en de
ingebouwde Strategy Tester — geen losse historische data-export nodig.

## Bekende versimpelingen (belangrijk!)

- **Alle 7 confirmaties zitten erin** (HTF, MTF, Range-positie, VWAP,
  Volume Profile/POC, VPSV, Supply/Demand), maar de laatste drie zijn
  **native Pine-benaderingen**, geen uitlezing van de LuxAlgo-indicatoren
  die de live-bot gebruikt (dat kan een op-zichzelf-staand
  strategy-script niet):
  - Volume Profile/POC: eigen grof geprijsd volume-profiel over
    `pocLookback` bars.
  - VPSV: cumulatief sessievolume-delta sinds sessiestart.
  - Supply/Demand: nabijheid van een recent pivot-high/low.
- **Take-profit schaalt uit over drie niveaus**: standaard 40% van de
  positie sluit op 0,25% vanaf entry, 40% op 0,50%, 20% op 1,00% — alle
  zes waardes (3x percentage, 3x portie) los instelbaar via de Inputs, in
  plaats van de eerstvolgende liquiditeitszone/swing-high/low uit de
  cursus. De drie TP's delen dezelfde SL: raakt de prijs de SL voordat
  (een deel van) de TP's geraakt zijn, sluit het resterende deel van de
  positie daar.
- **Breakeven na TP1**: zodra TP1 (standaard 0,25%) geraakt is, schuift
  de SL van het resterende deel (TP2+TP3) naar de entry-prijs — die trade
  kan vanaf dan geen verlies meer worden, hooguit quitte spelen als de
  rest ook op entry sluit.
- Impuls-detectie is een ATR-gebaseerde drempel + volume-eis
  (instelbaar), geen exacte cursus-definitie — die was nooit hard
  bevestigd (zie `docs/apa-signal-strategy.md`).
- **`minConfirmations`-filter**: standaard worden alleen oksels genomen
  met minstens dit aantal confirmaties (van de 7, standaard 4). Zet 'm op
  0 om — net als de live-bot — alle gevonden oksels te nemen ongeacht
  confirmaties. **Let op:** zet 'm niet op het maximum (7) — dan moeten
  alle confirmaties tegelijk kloppen, wat vrijwel nooit gebeurt en tot
  0 trades leidt (dat overkwam de `4 van 4`-test hiervoor ook al).
- Slechts één actieve pending oksel per richting tegelijk — de live-bot
  kan meerdere tegelijk bijhouden, dit script (nog) niet.

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

**Bij het bijwerken van het script:** verwijder eerst de vorige versie
van de chart (hover over de indicatornaam linksboven op de chart, klik
het kruisje) vóórdat je de nieuwe versie toevoegt. Staan er twee
versies tegelijk actief, dan plaatsen ze allebei trades en kloppen de
Key Stats niet meer — controleer dit als het aantal trades er ineens
onverklaarbaar hoog uitziet.

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
