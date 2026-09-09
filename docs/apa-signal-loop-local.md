# APA-signalering draaien via TradingView (lokaal)

Deze aanpak draait **alleen lokaal**, in een Claude Code-sessie op je Mac —
niet in een cloud-sessie. Reden: de `tradingview` MCP-server praat via het
Chrome DevTools Protocol met de TradingView Desktop-app die lokaal op je
Mac draait (poort 9222). Een cloud-sessie kan die app niet bereiken.

## Vereisten (zie ook de eerdere setup)

- TradingView Desktop gestart via `~/tradingview-mcp/scripts/launch_tv_debug_mac.sh`
  (debug-poort 9222 open), ingelogd, met het MEXC-symbool en de timeframe
  open die je wilt monitoren.
- `tradingview` MCP-server geregistreerd op user scope (`claude mcp list`
  om te checken).
- Voeg VWAP en eventueel een Volume Profile-indicator handmatig toe aan de
  chart in TradingView zelf — de MCP-server leest bestaande indicatoren,
  maar voegt er niet automatisch relevante toe. Volume Profile/POC zijn
  geen directe tools; die moet je als Pine-indicator op de chart hebben
  staan zodat ze via `data_get_pine_lines` / `data_get_pine_boxes` /
  `data_get_study_values` uitleesbaar zijn.

## Beschikbare TradingView-MCP tools (relevant voor detectie)

- `chart_get_state` — huidig symbool, timeframe, actieve indicatoren
- `quote_get` — laatste prijs/OHLC/volume
- `data_get_ohlcv` — prijsbalken (voor impuls/void-detectie)
- `data_get_study_values` — waarden van indicatoren (o.a. VWAP)
- `data_get_pine_lines` / `data_get_pine_labels` / `data_get_pine_boxes` —
  handmatig getekende/indicator-zones (support/resistance, volume-profile
  zones, liquiditeitszones — als ze als Pine-object op de chart staan)
- `chart_set_symbol` / `chart_set_timeframe` — wisselen van markt/HTF-LTF
- `capture_screenshot` — visuele controle van een gesignaleerde setup

Let op: dit zijn ongedocumenteerde interne TradingView-API's via de
Electron debug-interface — kunnen breken bij een TradingView-update.

## Draaien via de `/loop` skill

Start lokaal een Claude Code-sessie in deze repo en gebruik de `loop`
skill met onderstaande prompt (pas het symbool/timeframe aan):

```
/loop 10m (0) Meld eerst het huidige tijdstip via het Bash-commando
date '+%H:%M' en meld "Check om <tijd> — volgende check rond <tijd+10m>".
Check daarna de TradingView-chart op een APA-signaal volgens
docs/apa-signal-strategy.md in deze repo. Stappen: (1) onthoud via
chart_get_state het huidige symbool en timeframe (hier schakel je aan
het eind naar terug). (2) chart_set_timeframe naar 3. Gebruik
data_get_ohlcv om te zoeken naar een setup in BEIDE richtingen (long én
short, geen voorkeur): een recente impuls, een bijbehorende void/
backtest-zone, en of de prijs nu die zone opnieuw test. Vind je dit
niet: er is geen setup, ga direct naar stap 4 (log + geen melding). (3)
Is er wél een setup: bepaal entry/SL/TP op basis van deze 3m-data. Check
dan ALLE confirmaties en beoordeel elk met ✅ (bevestigt), ⚠️ (onduidelijk/
zwak) of ❌ (bevestigt niet) — dit blokkeert de setup niet, het is puur
voor de risicoclassificatie: VWAP en Volume Profile/POC en VPSV en
Supply/Demand-liquiditeitszones via data_get_study_values/
data_get_pine_lines/data_get_pine_boxes op de 3m-chart; daarna kort
chart_set_timeframe naar 15 voor de MTF-trend, en naar D (Daily) voor de
HTF-trend en eerdere impulsen/voids van de afgelopen weken/maanden
(gebruik data_get_ohlcv op beide). (4) chart_set_timeframe terug naar
het timeframe uit stap 1, ook als er geen setup is. Log daarna ALTIJD
(elke check, ook bij geen setup) een regel in logs/apa-signal-log.csv
via een Bash-commando in de vorm echo "<timestamp iso>,<richting: long/
short/geen>,<htf: ✅/⚠️/❌/n.v.t.>,<mtf: ✅/⚠️/❌/n.v.t.>,<vwap: ✅/⚠️/❌/
n.v.t.>,<volume_profile: ✅/⚠️/❌/n.v.t.>,<vpsv: ✅/⚠️/❌/n.v.t.>,<supply_
demand: ✅/⚠️/❌/n.v.t.>,<signaal: ja/nee>" >> logs/apa-signal-log.csv
(n.v.t. bij geen setup; vul de echte waardes in, geen spaties in de
velden). Is er een setup (ongeacht hoeveel confirmaties ✅ zijn): stuur
DIRECT een macOS-melding met een Bash-commando in de vorm osascript -e
'display notification "<symbool> <richting>, entry <entry>, SL <sl>, TP
<tp>, R:R <rr>, risico: <laag/gemiddeld/hoog>" with title "APA-signaal"
sound name "Glass"' (vul de echte waardes in), en meld daarna dezelfde
info hier in de chat: symbool, timeframe (3m), richting, entry/SL/TP,
R:R, de confirmaties met hun ✅/⚠️/❌ (bijv. "Confirmaties: VWAP ✅
(79.058), HTF-trend ⚠️, Volume Profile/POC ❌, VPSV ❌, Supply/Demand ❌"),
en een risicoclassificatie — met de disclaimer dat dit signalering is,
geen advies. Geen setup: meld kort "geen signaal", GEEN melding sturen.
```

Dit her-checkt elke 10 minuten (pas het interval aan naar smaak — bedenk
dat elke check meerdere tool-calls kost en dus meetelt voor je 5-uurs
gebruikslimiet) zolang de sessie open staat en TradingView met
debug-poort draait.

## Knelpunt vinden: logs/apa-signal-log.csv

Elke check (ook zonder setup) voegt een regel toe: timestamp, richting
(long/short/geen), en per confirmatie (HTF, MTF, VWAP, Volume Profile,
VPSV, Supply/Demand) een ✅/⚠️/❌/n.v.t., plus of het uiteindelijk een
signaal werd. Na een tijdje draaien kun je hier (of lokaal) vragen: "Lees
logs/apa-signal-log.csv en vat samen hoe vaak er een setup was, en welke
confirmaties het vaakst ❌ zijn" — dat laat zien hoe vaak het patroon
zelf voorkomt en welke confirmatie het minst vaak meewerkt.

**Let op bij het updaten:** het kolomformaat van dit bestand is
gewijzigd (van de oude HTF/MTF/LTF/VWAP-poort-kolommen naar de nieuwe
✅/⚠️/❌-confirmatiekolommen). Als je lokale `screen`-sessie al rijen aan
het oude bestand heeft toegevoegd, geeft `git pull` mogelijk een
conflict omdat het lokale bestand afwijkt van wat er nu in de repo staat.
Zet in dat geval eerst je lokale rijen apart (`cp logs/apa-signal-log.csv
logs/apa-signal-log-oud.csv`), doe dan `git checkout -- logs/apa-signal-log.csv`
om je lokale wijzigingen te laten vallen, en pull daarna opnieuw.

## Op de achtergrond draaien (screen)

`screen` zit al op macOS (geen installatie nodig). Zo blijft de sessie
draaien ook als je het Terminal-venster sluit — zolang je Mac aan blijft
en niet in slaap gaat, en TradingView met debug-poort blijft draaien.

Start een nieuwe achtergrondsessie:
```bash
screen -S apa-loop
```
Start daarin `claude` en de `/loop`-prompt hierboven zoals normaal. Koppel
de sessie los (laat 'm doordraaien) met **Ctrl+A, dan D**. Je Terminal-
venster kun je nu sluiten.

Weer terugkijken:
```bash
screen -r apa-loop
```
Checken of hij nog draait zonder erin te gaan:
```bash
screen -ls
```
Stoppen: ga terug in de sessie (`screen -r apa-loop`) en typ `exit`, of
gebruik `/loop stop` binnen Claude Code zelf.

## Itereren

De exacte regels in `docs/apa-signal-strategy.md` zijn een
best-inschatting (zie disclaimer bovenin dat bestand). Vergelijk de eerste
paar signalen handmatig met wat je zelf op de chart ziet, en scherp de
regels (met name entry/SL/TP-verhouding) samen aan in dat bestand.
