# Fullskjerms slideshow for Fully Kiosk

En statisk nettside uten biblioteker, byggesteg, menyer, knapper eller synlig tekst. CSS og JavaScript ligger i index.html. Svart bakgrunn, sentrerte bilder med object-fit: contain, 1,5 sekunders kryssfade og bildebytte hvert 20. sekund. Ved langsom lasting eller bildefeil kan byttet bli forsinket; det forrige bildet beholdes til neste er lastet. I selve overgangen overlapper to bilder kortvarig.

Siden fyller tilgjengelig skjermflate, inkludert 1340 × 800 i liggende modus. Ulikt bildeformat gir svarte felt uten beskjæring eller forvrengning. Fullskjerm uten Android-systemlinjer må aktiveres i Fully.

## 1. Legg inn dine bilder

images.json er konfigurert med de 10 bildene i images-mappen, sortert etter filnavn. Siden er klar til publisering. Du kan endre rekkefølgen ved å flytte oppføringene i listen.

Legg JPG-, PNG- eller WebP-filer i images-mappen og erstatt innholdet i images.json med for eksempel:

```json
[
  "images/fjell.jpg",
  "images/familie.jpg",
  "images/ferie.webp"
]
```

Eksemplene over er filnavn du selv må laste opp. Bruk gjerne enkle filnavn uten mellomrom. Store og små bokstaver må stemme nøyaktig. Bruk relative stier uten innledende /, slik at siden også virker under /repo-navn/. Listen bestemmer rekkefølgen og gjentas i en løkke. Ett bilde i listen blir stående, men lastes på nytt for å oppdage endret innhold.

## 2. Publiser med GitHub Pages

1. Opprett et GitHub-repo, for eksempel `tablet-slideshow`. Et offentlig repo er den enkleste løsningen. Bildene blir offentlig tilgjengelige; bruk bare bilder som kan publiseres.
2. Last opp innholdet i denne slideshow-mappen til roten av repoet: index.html, images.json, .nojekyll og images-mappen med bildene. Ikke legg selve slideshow-mappen som et ekstra nivå. README.md er valgfri.
3. Commit filene til main.
4. Åpne **Settings → Pages** i repoet.
5. Under **Build and deployment**, velg **Deploy from a branch**.
6. Velg **main** og **/ (root)**, og trykk **Save**.
7. Vent til publiseringen er ferdig. Pages viser den faktiske nettadressen, normalt `https://BRUKERNAVN.github.io/tablet-slideshow/`.
8. Åpne adressen og kontroller at bildene vises.

GitHubs veiledning: https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site

## 3. Bruk i Fully Kiosk Browser

Bruk Pages-adressen direkte som **Screensaver URL**. I nyere Fully-versjoner kan feltet ligge under **Screensaver Playlist**: legg inn nettsiden som eneste URL-element. Ikke bruk GitHubs repo-adresse, images.json eller en raw.githubusercontent.com-adresse.

Aktiver screensaver og velg ønsket inaktivitetstid. Sett orienteringen til liggende, aktiver fullskjerm og skjul status-/navigasjonslinjer. La JavaScript være aktivert, og sett eventuell automatisk omlasting av nettsiden slik at den ikke stadig starter lysbildefremvisningen på nytt. Screensaver er en Fully PLUS-funksjon. Menynavn kan variere mellom versjoner.

Fully-dokumentasjon: https://www.fully-kiosk.com/en/

## 4. Legg til, fjern eller erstatt bilder senere

- **Legg til:** Last opp filen til images/ og legg til for eksempel `"images/nytt-bilde.jpg"` i images.json. Commit endringene, helst samlet.
- **Fjern:** Slett bildets linje fra images.json og commit. Bildefilen kan også slettes fra repoet.
- **Endre rekkefølge:** Flytt linjene i JSON-listen.
- **Erstatt:** Last opp en ny fil med samme navn. Cache-busting gjør at den hentes på nytt ved senere visning. Et nytt filnavn er også mulig.
- **Tøm:** Sett images.json til `[]`; skjermen blir svart når listen er oppdatert.

JSON bruker doble anførselstegn og komma mellom elementer, men ikke etter siste element. En ugyldig JSON-fil eller nettverksfeil gjør at siden beholder siste gyldige liste. Manglende/ødelagte bilder hoppes over; hvis ingen kan lastes, beholdes sist viste bilde. Feil skrives bare til nettleserkonsollen.

Etter at GitHub har publisert endringene, hentes listen innen normalt 60 sekunder mens siden kjører. Den brukes ved neste bildebytte, normalt innen ytterligere 20 sekunder pluss lastetid. Mange nye bilder vises etter tur, ikke alle umiddelbart. Pauset/bakgrunnskjørt WebView kan bruke lengre tid.

## Cache og nettverk

images.json hentes med `cache: 'no-store'` og en unik `_fresh`-parameter. Hver bildeforespørsel får også en unik parameter, slik at også erstattede bilder under samme navn hentes på nytt. Ingen service worker brukes. Dette krever ingen manuell tømming av Fully-cachen for bildeendringer, men kan ikke gjøre en GitHub-publisering tilgjengelig før den er ferdig.

Bildene lastes på nytt ved hver visning. Komprimer derfor gjerne bildene til passende størrelse, for eksempel omtrent 1600–2000 piksler på lengste side. Ett bilde på 500 kB hvert 20. sekund gir omtrent 90 MB per time. Løsningen er laget for nettbruk: ved nettbrudd beholdes siste viste bilde, men det er ingen komplett frakoblet bildecache. Endring av selve HTML/JS-koden krever at siden lastes på nytt; ved behov kan Screensaver URL få `?v=2` for en ny kodeversjon.

## Hvorfor ikke vanlige OneDrive-delingslenker?

Vanlige delingslenker peker til OneDrives delings-/forhåndsvisningsflyt, ikke nødvendigvis direkte til bildefilen som et HTML-img-element trenger. Tilgang kan også avhenge av innlogging og delingsinnstillinger. Microsoft har eksplisitt beskrevet at delingslenker som åpner forhåndsvisning, ikke kan brukes som HTML-ressurser:

https://techcommunity.microsoft.com/blog/onedriveblog/sharing-links-will-open-in-modern-previewers/136553

OneDrive tilbyr en egen Embed-funksjon, men denne må ikke forveksles med en vanlig delingslenke eller en stabil direkte bildeadresse:

https://support.microsoft.com/en-us/onedrive/how-to-add-an-embedded-document-or-picture-into-a-webpage

Microsoft Graph kan hente direkte nedlastingsadresser, men Microsoft sier at disse er forhåndsautoriserte og kan utløpe i løpet av minutter. Å lagre slike midlertidige adresser i images.json er derfor uegnet for en screensaver som skal gå over tid:

https://learn.microsoft.com/en-us/graph/api/driveitem-get-content?view=graph-rest-1.0

Enkelte direkte OneDrive-adresser kan fungere i bestemte tilfeller, men denne løsningen baserer seg ikke på lenkeomskriving eller midlertidige nedlastingsadresser. Last i stedet ned de ønskede bildene fra OneDrive og legg dem i repoets images/-mappe. Da har siden og bildene samme vert og trenger ingen OneDrive-innlogging eller tokenfornyelse.

## Lokal kontroll

Bruk en lokal HTTP-server hvis du vil teste før publisering, for eksempel `python -m http.server 8080` fra denne mappen, og åpne http://localhost:8080/. Å dobbeltklikke index.html via file:// er ikke en pålitelig test, fordi nettleseren kan blokkere fetch av images.json.

