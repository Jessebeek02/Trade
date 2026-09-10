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
- **SL op structuur, niet op een vast percentage**: de SL staat op het
  laatste pivot-punt (bovenkant lokale range voor short, onderkant voor
  long — het "structurele" niveau, zoals je zelf op de chart aanwees),
  met `slFallbackPercent` (standaard 0,25%) als terugvaloptie wanneer er
  geen bruikbaar pivot-punt is. De SL-afstand verschilt dus per trade,
  maar is begrensd op maximaal `maxSlPercent` (standaard 1,0%) — ligt het
  pivot-punt verder weg dan dat, dan wordt de SL afgekapt tot dat
  maximum.
- **Take-profit schaalt mee met die SL-afstand**: drie niveaus, als
  R-multiples van de (variabele) SL-afstand — standaard TP1 = 1x, TP2 =
  2x, TP3 = 4x, waarvan resp. 40%/40%/20% van de positie sluit. Alle zes
  waardes los instelbaar via de Inputs, in plaats van de eerstvolgende
  liquiditeitszone/swing-high/low uit de cursus. De drie TP's delen
  dezelfde SL: raakt de prijs de SL voordat (een deel van) de TP's
  geraakt zijn, sluit het resterende deel van de positie daar.
- **Breakeven na TP1**: zodra TP1 geraakt is, schuift de SL van het
  resterende deel (TP2+TP3) naar de entry-prijs — die trade kan vanaf dan
  geen verlies meer worden, hooguit quitte spelen als de rest ook op
  entry sluit.
- **Filters om minder, sterkere setups te krijgen** (dit wijkt af van de
  live-bot, die blokkeert bewust niets):
  - Impuls-drempel en volume-eis staan standaard op **2,5x** (i.p.v.
    1,8x) — minder maar overtuigender impulsen.
  - **HTF- en MTF-trend zijn een harde vereiste** geworden (moeten
    allebei kloppen) — geen tegen-de-trend-trades meer. Dit zijn dus geen
    "confirmaties" meer die je kunt uitzetten via `minConfirmations`.
  - De overige **5** confirmaties (Range-positie, VWAP, POC, VPSV,
    Supply/Demand) blijven optioneel: `minConfirmations` (standaard 3)
    bepaalt hoeveel daarvan minstens moeten kloppen. Zet 'm op 0 voor
    alleen de HTF/MTF-poort, zonder verdere eisen. **Let op:** zet 'm niet
    op het maximum (5) — dan moeten alle vijf tegelijk kloppen, wat
    zelden gebeurt.
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
