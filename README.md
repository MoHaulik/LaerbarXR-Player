# LærbarXR — Player

Afspiller 360°-simulationer i WebXR, styret med blikket. Åbn siden i browseren på headsettet, indlæs den `.zip`, Builder har eksporteret, og gå ind i VR. Ingen controllere, ingen app, intet at installere.

LærbarXR består af tre repos:

- [LaerbarXR-Builder](https://github.com/MoHaulik/LaerbarXR-Builder): byg en simulation og eksportér den som `.zip`
- **LaerbarXR-Player** (dette repo): afspil den på Quest 3 eller i en browser
- [LaerbarXR-Dashboard](https://github.com/MoHaulik/LaerbarXR-Dashboard): start og følg klassen live, saml resultater

Udgivet på https://mohaulik.github.io/LaerbarXR-Player/ (GitHub Pages fra `main`). Målenheden er Meta Quest 3.

## Sådan bruges den

1. Åbn siden på headsettet og indlæs simulationens `.zip`.
2. Vælg headsetnummer og hold første gang. Det huskes på enheden.
3. Tryk **Start**. Er der forbindelse til relayet, venter headsettet på lærerens Start i Dashboardet; ellers kører det alene.
4. **Run on this screen (no headset)** kører samme simulation i et almindeligt browservindue, hvor musen erstatter hovedet. Det bruges til afprøvning og i Builders preview.

## Hvordan det virker

- **Valg med blikket:** Kig på et svar, til ringen er fyldt (standard 2,0 s; kan sættes pr. scene og pr. simulation). Svar kan først vælges kort efter, de vises, og et blik der allerede hviler på et svar, skal væk og tilbage igen.
- **Tidslinje:** Scenens ur går kun fremad. Videoen styrer det, mens den spiller; hænger videoen, overtager uret. Tidslinjen starter først, når videoen har vist sin første frame.
- **Layout:** Tekst, billeder og spørgsmål placeres på hele kuglen ud fra Builders X/Y. Svar lægges i et gitter (op til 20). Afslut og Play-knappen har faste pladser, og spørgsmål foran eleven løftes eller krympes, så de aldrig dækker dem.
- **Afslut** sidder lavt i synsfeltet med længere kigge-tid. Den kan fjernes for hele simulationen i Builder.
- **↻ Se igen** starter scenens video forfra.
- **Flervalg** ("Vælg alle rigtige"): blikket krydser svar af, Bekræft sender sættet, og facit vises i videoen.
- **Inaktivitet:** Har simulationen `idleTimeoutSec`, afsluttes et gennemløb, hvor headsettet ikke har bevæget sig mere end 1,5° / 3 cm i så lang tid. Det markeres som udelukket. Det gælder kun efter lærerens Start og aldrig i flad preview.

## Resultater

Når et gennemløb slutter, skrives en `*_eval.json` med headset, enheds-id, hold, simulation, tider og alle hændelser (`choice_made`, `auto_advance`, `replay` m.fl.). `end_reason` er `completed`, `exit`, `instructor`, `inactive` eller `interrupted`.

Resultatet sendes til relayet, så det dukker op i Dashboardet med det samme. Det downloades også og gemmes på enheden (de seneste 25), så det kan hentes igen fra startskærmen, hvis download eller netværk svigter.

Under gennemløbet sender headsettet heartbeats med scene, status og første svar pr. spørgsmål, så Dashboardet kan vise live-tal.

## Relay

Player bruger samme cloud-relay som Dashboardet (`RELAY_BASE` i `index.html`). Et Start frigiver kun et headset, der allerede venter, og derefter hører headsettet til det Dashboard, der sendte Start. Se Dashboard-repoets README for detaljer og for den lokale `relay.py`.

## Status

Afprøvet i desktop-browser via flad preview, hele vejen gennem relayet til Dashboardet. **Ikke testet på et rigtigt Quest 3 endnu.** Det, der mangler at blive bekræftet på headset, er selve VR-visningen, følelsen af blik-valg og kigge-tid, afspilning af store 360°-videoer på enheden, lyd i headsettets højttalere og grænserne for inaktivitet.

## Afhængigheder

Ligger i `vendor/`; siden henter intet fra et CDN og virker uden internet.

| Fil | Version | Licens |
| --- | --- | --- |
| `vendor/three.min.js` | three.js r150 | MIT |
| `vendor/jszip.min.js` | JSZip 3.10.1 | MIT / GPLv3 |

three.js indlæses som klassisk script (UMD-build), ikke som ES-modul.
