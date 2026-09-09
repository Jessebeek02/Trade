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
3. **De void (oksel) ís de entry-zone** — zodra de impuls een void
   achterlaat, is die zone zelf waar je instapt (long bij een
   mark-up-void, short bij een mark-down-void) — als een limit order in
   de oksel, niet pas na een latere "reactie" of tweede bevestiging. (Dit
   is een correctie op een eerdere versie van dit document die een
   "wacht op duidelijke reactie"-regel bevatte — dat was nooit uit de
   cursus-audio bevestigd en was een onterechte verstrenging.)

## Eén meldingsmoment: zodra de oksel ontstaat

Er is precies één signaalmoment, zoals in de cursus: de oksel ontstaat →
de bot analyseert HTF, MTF en LTF → de bot checkt de confirmaties → er
rolt één complete setup uit (entry in de oksel, SL, TP, risico). Dit
gebeurt direct zodra de void gezien wordt op **3m** (in beide
richtingen, long én short, geen voorkeur) — niet pas nadat de prijs 'm
al opnieuw geraakt heeft. Elke oksel wordt maar **één keer** zo gemeld,
niet elke check opnieuw zolang dezelfde oksel nog niet geraakt/ongeldig
verklaard is. Geen impuls/void gevonden: geen setup, klaar — dit is de
enige harde voorwaarde.

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

- **Entry**: in de oksel zelf — de rand van de void aan de kant waar de
  prijs vandaan kwam (dus het eerste punt waar de prijs de zone weer zou
  raken), als een limit order. Niet pas na een latere reactie.
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
