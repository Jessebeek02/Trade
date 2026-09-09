# APA (Abrupte Prijs Actie) — Signaleringsregels

Gedestilleerd uit het DoopieCash-leerprogramma "Daytraden & Scalpen". Dit is
een **best-inschatting** op basis van de geschreven modulestructuur en
schermafbeeldingen — de exacte kwantitatieve details (met name de precieze
"1.1.2-regel" en confirmatie-drempels) konden niet uit de gesproken video's
gehaald worden (geen bruikbare transcriptie, zie DoopieCash-gesprek). Deze
regels worden dus samen gefinetuned op basis van live resultaten.

**Dit is geen financieel advies.** Het is hulp bij het signaleren van
kansen op de chart volgens een vast patroon, met bijbehorend risico/R:R.

## Kernpatroon

1. **Impuls** — een abrupte, sterke koersbeweging (mark up = breakout richting
   boven, mark down = breakdown richting beneden), die een fase van
   Accumulatie (long-opbouw) of Distributie (short-opbouw) beëindigt.
2. **Backtest-zone (void)** — het "lege gebied" dat de impuls achterlaat:
   een prijsrange waar weinig tot geen handel plaatsvond tijdens de impuls
   zelf (te herkennen aan een gat in het volume-profiel / weinig candles
   met overlap).
3. **Retest = entry-signaal** — wanneer de prijs teruggaat naar die void en
   deze opnieuw test, is dát het potentiële instapmoment (long bij
   terugtest van een mark-up-void, short bij terugtest van een
   mark-down-void).

## LTF (3m) — waar een setup op gebaseerd wordt

De daadwerkelijke impuls/void/retest en het entry-moment worden altijd op
**3m** gezocht, bij elke check, in beide richtingen (long én short) — er
wordt niet vooraf gefilterd op een "toegestane richting". Vind je op 3m
een impuls + bijbehorende void + een retest daarvan: dat ís de setup.
Entry, SL en TP worden op deze 3m-data bepaald. Geen impuls/void/retest
gevonden: geen setup, klaar — dit is de enige harde voorwaarde.

### Twee meldingsmomenten

1. **Void gevormd** (vroeg signaal) — zodra een impuls + void gezien
   wordt, ook al is er nog geen retest: meld direct de zone-grenzen
   (voidLow/voidHigh) en richting, zodat je zelf alvast een limit order
   in die zone kunt zetten in plaats van te wachten op de bevestigde
   retest-melding (die door de 10-min-check-interval een paar minuten
   kan achterlopen op het echte moment). Elke void wordt maar **één
   keer** zo gemeld — niet elke check opnieuw zolang er nog geen retest
   is.
2. **Retest bevestigd** (het bestaande entry-signaal) — zodra de prijs
   de void retest mét een duidelijke reactie (zie Entry/SL/TP hieronder):
   de volledige melding met entry/SL/TP, R:R en confirmaties.

Beide zijn signalering, geen advies — een vroege void-melding is geen
garantie dat de retest ook echt komt of reageert.

## Confirmaties — informatief, geen poort

Zodra er een setup is (zie hierboven), worden onderstaande confirmaties
gecheckt en **altijd gerapporteerd**, ongeacht of ze aanwezig zijn — ze
blokkeren een setup niet, ze bepalen alleen het gerapporteerde risico.
Rapporteer elk met ✅ (bevestigt), ⚠️ (onduidelijk/zwak), of ❌
(bevestigt niet/tegengesteld):

- **HTF-trend (Daily)** — is de richting van de setup in lijn met de
  trend en eerdere impulsen/voids op Daily (afgelopen weken/maanden)?
- **MTF (15m)** — bevestigt de 15m-trend/-prijsactie dezelfde richting?
- **VWAP** — ligt de retest aan de juiste kant van VWAP voor de richting
  van de trade?
- **Volume Profile / POC** — valt de void samen met een low-volume node,
  en ligt het Point of Control niet middenin de void (zou de impuls-these
  tegenspreken)?
- **VPSV (session volume)** — bevestigt het sessievolume de impuls-richting?
- **Supply/Demand & liquiditeit** — ligt de void in of nabij een
  supply/demand-zone of liquiditeitszone?

Hoe meer ✅, hoe lager het gerapporteerde risico. Veel ❌ (met name bij
HTF/VWAP): markeer als hoog risico, ook al klopt het 3m-patroon zelf.

## Entry / SL / TP — "1.1.2-regel" (best-inschatting, te verfijnen)

- **Entry**: bij de eerste duidelijke reactie op de retest van de void
  (niet blind bij de eerste aanraking).
- **Stop-loss**: net voorbij de void, op basis van prijsactie (niet een
  vaste pip/procent-afstand) — zie ook de Supply/Demand/Liquiditeit-module:
  SL net buiten de zone die de these ongeldig zou maken.
  → Voor bevestiging vs. de cursus-video's is de exacte "1.1.2"-verhouding
    (waarschijnlijk een entry/SL/TP-verhouding) nog niet hard bevestigd.
- **Take-profit**: volgende relevante liquiditeitszone of swing high/low;
  overweeg limit vs. market afhankelijk van de gewenste winstmaximalisatie
  (zoals besproken in Module 3).

## Risico-rapportage bij elk signaal

Bij elke gesignaleerde kans, rapporteer:
- Symbool, timeframe (3m), richting (long/short)
- Entry, SL, TP (prijsniveaus)
- Risk:Reward-ratio
- Confirmaties: elk van HTF-trend, MTF, VWAP, Volume Profile/POC, VPSV,
  Supply/Demand met ✅/⚠️/❌ (zie Confirmaties hierboven), ook als de
  meeste ❌ zijn
- Risico-classificatie (laag/gemiddeld/hoog) op basis van bovenstaande
- Expliciete disclaimer: signalering, geen advies

## Begrippenlijst (referentie)

Accumulatie, Distributie, Impuls, Backtest/void, HTF/LTF, Pullback,
Rejection, Retracement, Reversal, Swing Low/High, Volume Node, VWAP,
Volume Profile, POC, VPSV — zie cursusmateriaal voor volledige lijst (23
termen).
