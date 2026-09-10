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
3. **De void (oksel) ís de entry-zone, maar pas na een reclaim-bevestiging**
   — zodra de impuls een void achterlaat, is die zone waar je uiteindelijk
   instapt (long bij een mark-up-void, short bij een mark-down-void), als
   een limit order. (Een eerdere versie van dit document zei dat dit
   *direct* gebeurt, zonder te wachten op een reactie — dat bleek zelf
   weer een onterechte versimpeling: cursusmateriaal over module 2 laat
   zien dat de entry pas gebeurt "als de prijs teruggeeft tot in de void
   **en daar een reactie laat zien** — een korte terugval die weer wordt
   opgevangen".) Concreet: wacht tot de prijs minstens **3 candles op rij**
   volledig — open én close — aan de goede kant van de oksel sluit (boven
   voor een mark-up-void, onder voor een mark-down-void) vóórdat je de
   setup als bevestigd beschouwt en de entry meldt. Eén candle die dat niet
   doet: geen bevestiging, geen melding (nog) — wacht op een nieuw signaal.
   De precieze entry-lijn zit aan de kant van de oksel die het **verst**
   van de impuls-richting af ligt: bij een mark-up-void (long) de
   **onderkant** van de oksel (het diepste punt van de void), bij een
   mark-down-void (short) de **bovenkant**.

## Eén meldingsmoment per oksel: zodra hij bevestigd is

Er is precies één signaalmoment per oksel: de oksel ontstaat → de bot
houdt 'm bij als "wachtend op bevestiging" → zodra de prijs 3 candles op
rij de reclaim-eis haalt (zie hierboven) → de bot analyseert HTF, MTF en
LTF → de bot checkt de confirmaties → er rolt één complete setup uit
(entry in de oksel, SL, TP, risico). Dit gebeurt op **3m** (in beide
richtingen, long én short, geen voorkeur), zodra de bevestiging rond is —
niet meteen bij het ontstaan van de oksel zelf (zie punt 3 hierboven), en
ook niet pas nadat de prijs 'm daarna nóg een keer raakt. Een oksel die
de reclaim-bevestiging niet haalt (bijv. een candle sluit terug binnen de
oksel vóór de 3 zijn volgemaakt) wordt niet gemeld — die kans is dan
vervallen. Elke bevestigde oksel wordt maar **één keer** zo gemeld. Geen
impuls/void gevonden, of nooit bevestigd: geen setup — dit is de enige
harde voorwaarde.

## Meerdere actieve oksels tegelijk

Dit gaat over **bevestigde** oksels (zie hierboven) — die zijn en blijven
een geldige entry-kans totdat ze **daadwerkelijk geretest worden** — pas
dan is zo'n oksel "uitgespeeld" en niet meer relevant. Een oksel die nog
in de wachtfase zit (nog niet 3 candles bevestigd) staat hier nog niet
tussen — die is nog geen gemelde setup, zie hierboven. Dat betekent:

- De bot kijkt bij elke check niet alleen naar de allerlaatste candles,
  maar scant een stuk verder terug in de 3m-prijsactie om **alle nog
  niet-geretete, bevestigde oksels** in kaart te brengen, niet alleen de
  nieuwste.
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

- **Entry**: aan de verste rand van de oksel (zie hierboven — bij een
  mark-up-void de onderkant, bij een mark-down-void de bovenkant), als
  limit order, pas ná de reclaim-bevestiging. Niet ergens middenin of aan
  het einde van de volledige impuls-afstand.
- **Stop-loss**: **0,25% vanaf entry** — dit staat letterlijk zo op de
  cursus-chart (module 3: "SL = 0,25%") en is dus geen losse
  vuistregel meer maar het uitgangspunt. Wijk hiervan af (bijv. krapper)
  als de chart een dichterbij gelegen, logische plek laat zien op basis
  van prijsactie — support/demand-zones, liquiditeitszones, of volume —
  zie ook de Supply/Demand/Liquiditeit-module: SL net buiten de zone die
  de these ongeldig zou maken. Ga niet ver**der** dan 0,25%, ook al lijkt
  een structureel niveau verder weg te liggen.
  → De exacte "1.1.2"-naam is nog steeds niet hard bevestigd (mogelijk een
    andere verhouding dan de hieronder genoemde TP's), maar de losse
    SL/TP-percentages zelf staan wel letterlijk in het cursusmateriaal.
- **Take-profit**: twee niveaus, als R-multiples van de SL-afstand —
  **TP1 = 1x SL (0,25%), TP2 = 2x SL (0,50%)**, letterlijk zo benoemd in
  module 3. De cursus toont daarnaast vage "30/40%"/"40/20%"-percentages
  bij het gedeeltelijk afbouwen van de positie op die targets, maar die
  konden zonder audio niet betrouwbaar worden vastgesteld — verdeel voor
  nu in gelijke delen (bijv. 50/50) tenzij je zelf een duidelijkere
  verdeling ziet. Overweeg limit vs. market afhankelijk van de gewenste
  winstmaximalisatie (dit onderdeel — "Winst maximaliseren", Module 3 —
  kon inhoudelijk niet uit de schermafbeeldingen worden herleid).

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
