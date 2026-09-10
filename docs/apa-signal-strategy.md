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

1. **Impuls** — een **overtuigende**, abrupte koersbeweging (mark up =
   breakout richting boven, mark down = breakdown richting beneden), die
   een fase van Accumulatie (long-opbouw) of Distributie (short-opbouw)
   beëindigt. "Overtuigend" wil zeggen: een duidelijke, stevige beweging
   met waarschijnlijk merkbaar verhoogd volume t.o.v. de candles ervoor —
   geen marginale of twijfelachtige beweging. Twijfel je of een impuls
   overtuigend genoeg is: dan is het geen impuls, geen oksel, geen setup.
2. **Backtest-zone (void/oksel)** — **niet** de volledige afstand die de
   impuls aflegt, maar een **smal prijsgebied vlak bij de oorsprong** van
   de impuls: de laatste 1-3 consolidatie-/opbouwcandles vlak vóórdat de
   sterke beweging losbarst. Bij een mark-down (short) zit dit dus
   **bovenaan**, vlak vóór de breakdown; bij een mark-up (long) zit dit
   **onderaan**, vlak vóór de breakout. Beeld: de impuls is een arm die
   omhoog/omlaag schiet, de oksel is het smalle plekje waar die arm aan
   het lichaam vastzit — niet de arm zelf. Een void die de hele
   impuls-afstand beslaat is dus fout; te breed.
3. **De void (oksel) ís de entry-zone** — zodra de impuls een void
   achterlaat, is die zone zelf waar je instapt (long bij een
   mark-up-void, short bij een mark-down-void) — als een limit order in
   de oksel, niet pas na een latere "reactie" of tweede bevestiging. (Dit
   is een correctie op een eerdere versie van dit document die een
   "wacht op duidelijke reactie"-regel bevatte — dat was nooit uit de
   cursus-audio bevestigd en was een onterechte verstrenging.) De precieze
   entry-lijn zit aan de kant van de oksel die het **verst** van de
   impuls-richting af ligt: bij een mark-up-void (long) de **onderkant**
   van de oksel (het diepste punt van de void), bij een mark-down-void
   (short) de **bovenkant**.

## Eén meldingsmoment per oksel: zodra hij ontstaat

Er is precies één signaalmoment per oksel, zoals in de cursus: de oksel
ontstaat → de bot analyseert HTF, MTF en LTF → de bot checkt de
confirmaties → er rolt één complete setup uit (entry in de oksel, SL,
TP, risico). Dit gebeurt direct zodra de void gezien wordt op **3m** (in
beide richtingen, long én short, geen voorkeur) — niet pas nadat de
prijs 'm al opnieuw geraakt heeft. Elke oksel wordt maar **één keer** zo
gemeld. Geen impuls/void gevonden: geen setup — dit is de enige harde
voorwaarde.

## Meerdere actieve oksels tegelijk

Een oksel is en blijft een geldige entry-kans totdat hij **daadwerkelijk
geretest wordt** — pas dan is hij "uitgespeeld" en niet meer relevant.
Dat betekent:

- De bot kijkt bij elke check niet alleen naar de allerlaatste candles,
  maar scant een stuk verder terug in de 3m-prijsactie om **alle nog
  niet-geretete oksels** in kaart te brengen, niet alleen de nieuwste.
- Er kunnen dus **meerdere setups tegelijk actief** zijn (bijv. een
  long-oksel van een half uur geleden én een short-oksel van gisteren,
  allebei nog ongeretest).
- Een oksel die inmiddels wél geretest is (ongeacht of jij 'm gepakt
  hebt) is uitgespeeld — niet meer signaleren, uit de actieve lijst
  halen.
- Elke nog-actieve oksel wordt maar **één keer** gemeld (zie hierboven);
  bij volgende checks alleen opnieuw melden als hij inmiddels uitgespeeld
  is (optioneel, ter info) of als er een nieuwe bijkomt.

## Confirmaties — informatief, geen poort

Zodra er een setup is (zie hierboven), worden onderstaande confirmaties
gecheckt en **altijd gerapporteerd**, ongeacht of ze aanwezig zijn — ze
blokkeren een setup niet, ze bepalen alleen het gerapporteerde risico.
Rapporteer elk met ✅ (bevestigt), ⚠️ (onduidelijk/zwak), of ❌
(bevestigt niet/tegengesteld):

- **HTF-trend (Daily)** — is de richting van de setup in lijn met de
  trend en eerdere impulsen/voids op Daily (afgelopen weken/maanden)?
- **MTF (15m)** — bevestigt de 15m-trend/-prijsactie dezelfde richting?
- **Range-positie (MTF/HTF)** — zit je gunstig binnen de recente range
  (bijv. niet bovenin de range voor een long, niet onderin voor een
  short)? Bovenin de range long / onderin short = ❌.
- **VWAP** — ligt de retest aan de juiste kant van VWAP voor de richting
  van de trade?
- **Volume Profile / POC** — valt de void samen met een low-volume node,
  en ligt het Point of Control niet middenin de void (zou de impuls-these
  tegenspreken)?
- **VPSV (session volume)** — bevestigt het sessievolume de impuls-richting?
- **Supply/Demand & liquiditeit** — ligt de void in of nabij een
  supply/demand-zone of liquiditeitszone?

Hoe meer ✅, hoe lager het gerapporteerde risico. Veel ❌ (met name bij
HTF/VWAP/Range-positie): markeer als hoog risico, ook al klopt het
3m-patroon zelf.

## Entry / SL / TP — "1.1.2-regel" (best-inschatting, te verfijnen)

- **Entry**: in de oksel zelf — het smalle basisgebied bij de oorsprong
  van de impuls (zie hierboven), als een limit order. Niet ergens
  middenin of aan het einde van de volledige impuls-afstand, en niet pas
  na een latere reactie.
- **Stop-loss**: **0,25% vanaf entry** als uitgangspunt. Wijk hiervan af
  (bijv. 0,20% of 0,30%, of verder) als de chart een logischere,
  duidelijkere plek laat zien op basis van prijsactie — support/
  demand-zones, liquiditeitszones, of volume — zie ook de
  Supply/Demand/Liquiditeit-module: SL net buiten de zone die de these
  ongeldig zou maken. Prijsactie is leidend boven het percentage.
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
- Confirmaties: elk van HTF-trend, MTF, Range-positie, VWAP, Volume
  Profile/POC, VPSV, Supply/Demand met ✅/⚠️/❌ (zie Confirmaties
  hierboven), ook als de meeste ❌ zijn
- Risico-classificatie (laag/gemiddeld/hoog) op basis van bovenstaande
- Expliciete disclaimer: signalering, geen advies

## Begrippenlijst (referentie)

Accumulatie, Distributie, Impuls, Backtest/void, HTF/LTF, Pullback,
Rejection, Retracement, Reversal, Swing Low/High, Volume Node, VWAP,
Volume Profile, POC, VPSV — zie cursusmateriaal voor volledige lijst (23
termen).
