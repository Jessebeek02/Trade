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
- **Entry pas ná bevestiging, niet direct bij het ontstaan van de oksel**:
  zodra een oksel + confirmaties kloppen, wordt er nog geen order
  geplaatst. Pas als de prijs `reclaimCandles` (standaard 3) candles op
  rij volledig — open én close — boven de **bovenkant** van de oksel
  sluit (long) / onder de **onderkant** sluit (short), wordt er een limit
  order geplaatst. De entry-prijs is dan de **andere kant** van de oksel:
  voor long de onderkant (het diepste punt van de void — de entry-lijn
  zit onderaan de void), voor short de bovenkant. Bevroren op het moment
  van het signaal, verschuift niet mee met de reclaim-candles. SL/TP
  worden wel pas ná bevestiging berekend, met de op dat moment actuele
  pivot. Eén candle die niet aan de eis voldoet annuleert de wachtende
  setup helemaal (geen nieuwe poging totdat er een nieuw signaal komt).
  **Let op:** omdat de entry nu op de verste rand van de oksel ligt (ná
  de impuls én de reclaim-candles, die de prijs juist verder weg duwen),
  moet de prijs relatief diep terugzakken voordat de order gevuld wordt —
  `maxBarsWaitFill` (standaard 400, was 80) geeft daar bewust ruimte
  voor. Een te korte wachttijd gaf hier "0 trades" bij het testen: de
  order verviel steeds voordat zo'n diepe terugval kans kreeg.
- **SL op structuur, begrensd op 0,25%**: de SL staat op het laatste
  pivot-punt (bovenkant lokale range voor short, onderkant voor long —
  zoals je zelf op de chart aanwees), met `slFallbackPercent` (standaard
  0,25%) als terugvaloptie wanneer er geen bruikbaar pivot-punt is. De
  SL-afstand is begrensd op maximaal `maxSlPercent` (standaard **0,25%**
  — overeenkomstig de letterlijke "SL = 0,25%" uit module 3, Scalpen &
  LTF-entry, van de cursus-naslagdocumenten) — ligt het pivot-punt
  verder weg dan dat, dan wordt de SL afgekapt tot dat maximum. Ligt een
  pivot-punt dichterbij, dan wordt die (krappere) SL gebruikt.
- **Take-profit schaalt mee met die SL-afstand**: twee niveaus, als
  R-multiples van de (variabele) SL-afstand — standaard **TP1 = 1x, TP2
  = 2x**, waarvan elk 50% van de positie sluit — overeenkomstig de
  letterlijke "TP1 = 0,25% (1:1) / TP2 = 0,50% (1:2)" uit dezelfde
  cursus-module (was eerder 2,5x, teruggezet naar 2x). De cursus toonde
  ook vage "30/40%"/"40/20%"-percentages bij de partiële afbouw, maar die
  konden zonder audio niet betrouwbaar worden herleid — 50/50 blijft hier
  een eigen, simpele keuze. Was eerst drie niveaus (1x/2x/4x, 40/40/20%),
  maar met kleine targets en 3 losse exit-orders per trade woog de
  commissie (fee per fill op MEXC) te zwaar mee t.o.v. de winst — vandaar
  terug naar twee niveaus (minder fill-momenten). Alle vier waardes los
  instelbaar via de Inputs. De twee TP's delen dezelfde SL: raakt de
  prijs de SL voordat (een deel van) de TP's geraakt zijn, sluit het
  resterende deel van de positie daar.
- **Breakeven na TP1**: zodra TP1 geraakt is, schuift de SL van het
  resterende deel (TP2) naar de entry-prijs — die trade kan vanaf dan
  geen verlies meer worden, hooguit quitte spelen als de rest ook op
  entry sluit.
- **Risk-based positiegrootte**: elke trade riskeert een vast percentage
  van het kapitaal (`riskPercent`, standaard 1%) — de ordergrootte wordt
  teruggerekend uit de entry/SL-afstand (risicobedrag / afstand), zoals
  DoopieCash's eigen "Positie Grootte Calculator" (module 3) dit ook
  rekent. Was eerst een vast percentage van het kapitaal als ordergrootte
  (`percent_of_equity`, los van de SL-afstand) — dat maakte het
  daadwerkelijke risico per trade ongelijk: een krappe SL riskeerde in
  R-termen onbedoeld meer dan een ruime SL, en de profit factor/R-cijfers
  van eerdere tests zijn dus niet 1-op-1 vergelijkbaar met deze versie.
- **Filters om minder, sterkere setups te krijgen** (dit wijkt af van de
  live-bot, die blokkeert bewust niets):
  - Impuls-drempel en volume-eis staan standaard op **2,0x** (i.p.v.
    1,8x) — minder maar overtuigender impulsen. Een 2,5x-versie bleek te
    streng (3 trades in 3,5 week).
  - **HTF- en MTF-trend waren kort een harde vereiste** (moesten allebei
    kloppen), maar dat gaf te weinig trades (6) om iets te kunnen
    concluderen. Ze zijn nu weer gewone, optionele confirmaties, net als
    de andere 5.
  - Alle **7** confirmaties (HTF, MTF, Range-positie, VWAP, POC, VPSV,
    Supply/Demand) tellen mee: `minConfirmations` (standaard 3) bepaalt
    hoeveel daarvan minstens moeten kloppen, ongeacht welke. 4 is ook
    getest, maar gaf minder trades zonder betere kwaliteit — teruggezet
    naar 3. **Let op:** zet 'm niet op het maximum (7) — dan moeten alle
    zeven tegelijk kloppen, wat zelden gebeurt.
  - **Let op bij het interpreteren van resultaten:** met de huidige
    testperiode (3,5 week) en het lage aantal trades per variant
    (100-250) zijn verschillen van bijv. profit factor 1,1 vs 1,5 tussen
    twee parametersets niet per se een echt signaal — dat kan net zo goed
    ruis zijn. Neem kleine PF/winrate-verschillen tussen tests met een
    korrel zout; alleen grote, herhaalde patronen (zoals de aanhoudende
    long/short-asymmetrie) zijn vooralsnog betrouwbaar genoeg om op te
    sturen.
- Slechts één actieve pending oksel per richting tegelijk — de live-bot
  kan meerdere tegelijk bijhouden, dit script (nog) niet.

Dit is dus een **eerste testbare benadering**, geen 1-op-1 kopie van de
cursus. De uitkomst gebruiken we om de regels (en deze script-parameters)
samen te verfijnen.

## Laden in TradingView

1. Open TradingView Desktop, je MEXC BTC/USDT-symbool, timeframe **3m**
   (dit is de LTF waar de strategie op draait — HTF/Daily en MTF/15m
   worden intern opgehaald, daar hoef je de chart niet voor te wisselen).

### Testen op 15m of 1h (bijv. voor meer kalenderhistorie zonder betaald plan)

De HTF/MTF-inputs staan standaard op **"Chart"** (leeg) = automatisch
passend bij de timeframe die je op de chart hebt staan. Je kunt dit
script dus ook direct op de **15m**- of **1h**-chart draaien, zonder verder
iets in te stellen:

| Chart-timeframe (LTF) | MTF (automatisch) | HTF (automatisch) |
| --- | --- | --- |
| 3m  | 15m | Daily |
| 15m | 1h  | Daily |
| 1h  | 4h  | Daily |
| 4h  | Daily | Weekly |

Handig omdat TradingView's gratis plan op hogere timeframes meestal wél
meer kalenderhistorie teruggeeft dan op 3m — zo kun je met een gratis
account toch een periode testen die zowel een bull- als een bear-fase
bevat, om te checken of long en short allebei werken en niet toevallig
scheef staan door een eenzijdige testperiode. Let op: het is dan geen
1-op-1 test van de "echte" 3m-setup — de overige bar-gebaseerde instellingen
(impuls-lookback, oksel-basis, ATR/volume-lengte, POC/pivot-lookback,
max-wacht-bars) blijven native chart-bars en betekenen dus een langere
kalenderperiode per bar op 1h dan op 3m. Zie het als een groffere,
aanvullende robuustheidscheck, niet als vervanging van de 3m-run.
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
