# Orderdagboek: trades journalen

Naast de automatische signaal-log (`logs/apa-signal-log.csv`, wat de bot
zelf bijhoudt) is er `logs/apa-trade-journal.csv` voor de trades die jij
daadwerkelijk neemt en hun uitkomst — het orderdagboek uit de cursus
(Module 4: Trial & Error), hier digitaal bijgehouden.

## Hoe het werkt

Geen speciaal format nodig — vertel het gewoon hier in de chat, in
gewone taal, zodra je een trade plaatst en/of zodra 'm is uitgespeeld.
Bijvoorbeeld:

> "Long gepakt op 79.100, SL 78.850, TP 79.850, net als het
> APA-signaal van 13:40."

> "Die trade is geraakt op TP."

> "Heb 'm handmatig gesloten met klein verlies, voelde niet goed door X."

Ik vul de regel dan aan in `logs/apa-trade-journal.csv` en commit het
naar de repo. Je hoeft niet alles in één bericht te geven — een trade
mag in twee stappen (openen, later de uitkomst) worden bijgewerkt.

## Kolommen

- `datum` — wanneer geopend
- `symbool`, `richting` (long/short)
- `entry`, `sl`, `tp` — de geplande niveaus
- `exit_prijs`, `uitkomst` (tp/sl/handmatig/lopend)
- `r_gerealiseerd` — hoeveel R (in verhouding tot het risico) het
  uiteindelijk werd
- `confirmaties_bij_entry` — welke confirmaties er stonden toen je
  instapte (handig om later te correleren met winrate)
- `notities` — vrije tekst: waarom je 'm nam, hoe het voelde, wat je
  ervan leert

## Waarom dit los staat van logs/apa-signal-log.csv

Het signaal-log is wat de bot ziet en meldt (elke 10 min, geautomatiseerd).
Het orderdagboek is wat jíj daadwerkelijk doet met die signalen — niet
elk signaal wordt een trade, en niet elke trade komt exact overeen met
een bot-signaal. Samen geven ze straks een goed beeld: hoe vaak
signaleert de bot iets, hoe vaak neem je het, en hoe presteert het.

Vraag me op elk moment: "Lees logs/apa-trade-journal.csv en vat de
resultaten tot nu toe samen" (winrate, gemiddelde R, wat vaak misging)
— net als de "4 pilaren" uit Module 4 (risico per trade, R:R, winrate,
aantal trades).
