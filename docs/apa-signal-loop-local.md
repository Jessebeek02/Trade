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
/loop 5m Check de TradingView-chart (huidig geopend symbool, MEXC) op een
APA-signaal volgens docs/apa-signal-strategy.md in deze repo. Gebruik
chart_get_state en data_get_ohlcv om de laatste prijsactie op te halen.
Zoek naar: (1) een recente impuls (abrupte mark-up of mark-down), (2) een
bijbehorende void/backtest-zone, (3) of de prijs nu die zone opnieuw
test. Check VWAP via data_get_study_values en eventuele
supply/demand-/liquiditeitszones via data_get_pine_lines/pine_boxes als
confirmatie. Als er een kwalificerende setup is: stuur DIRECT een
macOS-melding met een Bash-commando in de vorm
osascript -e 'display notification "<symbool> <richting>, entry <entry>,
SL <sl>, TP <tp>, R:R <rr>, risico: <laag/gemiddeld/hoog>" with title
"APA-signaal" sound name "Glass"' (vul de echte waardes in), en meld
daarna dezelfde info hier in de chat: symbool, timeframe, richting,
entry/SL/TP, R:R, welke confirmaties aanwezig/afwezig zijn, en een
risicoclassificatie — met de disclaimer dat dit signalering is, geen
advies. Als er geen setup is: meld kort "geen signaal" en stop, GEEN
melding sturen, geen verdere actie nodig.
```

Dit her-checkt elke 5 minuten (pas het interval aan naar smaak) zolang de
sessie open staat en TradingView met debug-poort draait.

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
