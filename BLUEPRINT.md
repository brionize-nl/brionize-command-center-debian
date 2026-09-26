# BLUEPRINT — Citizen Dev & AI Command Center ISO (Debian-based, efficiënte variant)

## Context — lees dit eerst
Dit is de **volledig herziene, efficiënte versie** van dit project. Een
zusterproject (elders) bouwde hetzelfde eindproduct via LFS/BLFS (alles
vanaf broncode compileren) en kwam na ~8 dagen tot een werkend basissysteem
+ desktop + devstack — waardevol als leertraject, maar traag: bijna elke
stap kostte losse compilatie-iteraties (ontbrekende dependencies, verkeerde
configure-vlaggen, enz.), puur omdat alles zelf gebouwd moest worden.

**Deze variant vermijdt dat volledig.** Basis: een bestaande, minimale
Linux-distributie (Debian), pakketten via `apt` (kant-en-klare, geteste
binaries) in plaats van broncode-compilatie. Dit is **geen diefstal** — een
bestaande distributie als basis nemen en aanpassen is standaardpraktijk en
expliciet toegestaan onder open-sourcelicenties (GPL e.d.), zolang aan de
licentievoorwaarden wordt voldaan. Ubuntu (op Debian), Linux Mint (op
Ubuntu), Raspberry Pi OS (op Debian) en Kali Linux (op Debian) zijn allemaal
voorbeelden van precies deze aanpak.

**Doel van dit document:** een AI die dit voor het eerst leest, kan
vrijwel direct autonoom beginnen te bouwen — zo min mogelijk losse
terugvraag-rondes, zo min mogelijk verspilde iteraties.

---

## STAP 0 — VERPLICHT: personalisatie vragen vóór er iets gebouwd wordt

**Dit is de allereerste stap, nog vóór er een regel code of configuratie
geschreven wordt.** Het onderstaande ontwerp (visuele stijl, thema-naam,
kleuren) is **het voorbeeld waarmee het originele project is gebouwd** —
niet een vaste eis. Vraag de persoon die dit uitvoert expliciet:

1. **Visuele stijl/thema.** Het origineel gebruikt een "Command-Center-
   Matrix"-esthetiek geïnspireerd op "The Machine" uit de serie *Person of
   Interest*: diepzwart met neon-groen/cyaan accenten, informatie-dichte
   tegels (FUI — Fictional User Interface — stijl: netwerkgrafieken,
   scrollende databalken). Dit is een **voorbeeld, geen verplichting** —
   de gebruiker kan net zo goed een heel andere stijl kiezen (bijvoorbeeld
   een Star Wars-thema, een minimalistische lichte stijl, of iets anders).
   Vraag concreet: kleurenschema, referentie-esthetiek, en of er specifieke
   iconografie/typografie bij hoort.
2. **Welke AI-webapps/PWA's** standaard als voorbeeld toegevoegd moeten
   worden (het origineel gebruikte Claude, ChatGPT, Mistral, Gemini) — dit
   hoeft niet hardcoded, zie "PWA/webapps" hieronder, maar de gebruiker mag
   hier alvast een voorkeur voor aangeven.
3. **Devstack-voorkeuren** — de standaardlijst hieronder (Node, Bun,
   Python, PostgreSQL, n8n, Tailscale, cloudflared, gh, PM2, Supabase CLI)
   is het origineel; vraag of dit moet worden aangepast.
4. **Repo-zichtbaarheid** (publiek/privé) en of er al een doel-apparaat is
   (oude pc specificaties, RAM) — relevant voor de hardware-bewuste
   tegel-limiet (zie verderop).

**Pas na deze antwoorden** BLUEPRINT.md/PROGRESS.md bijwerken met de
gekozen personalisatie, en dan pas beginnen met bouwen.

---

## Doel (kernproduct, ongeacht gekozen thema)
Een universele, geautomatiseerd gebouwde installer-ISO. Boot vanaf USB op
willekeurige (oudere) hardware en installeert zichzelf permanent op de
interne schijf: een 24/7 "Citizen Developer & AI Command Center" — een
lichtgewicht werkplek voor iemand die van idee tot eindproduct bouwt met AI,
automatisering (n8n) en developer-tooling, en het liefst op oude hardware
zonder (goede) GPU.

## Architectuur
- **Basis: Debian (stable), minimaal geïnstalleerd** via `apt` — brede
  hardware-ondersteuning out-of-the-box (Debian's standaardkernel), geen
  eigen kernelconfig nodig.
- **Build-tool: `live-build`** (officieel Debian-gereedschap voor
  aangepaste live/installer-ISO's — de "officiële route", goed
  gedocumenteerd, voorspelbaar).
- **Installer: Calamares** — distributie-onafhankelijk grafisch
  installatieprogramma, gebruikt door bestaande respins (KDE neon,
  Manjaro, EndeavourOS). Verzorgt partitioneren/formatteren/bootloader
  naar de doelschijf — geen eigen installer-logica nodig.
- **Type ISO:** volledige installer-naar-schijf, geen live-boot-only
  (stick-hitte/slijtage bij 24/7-gebruik, en permanente installatie is
  sowieso het doel).
- **Software-only rendering (geen GPU-vendor-drivers)** — Debian's
  standaard Mesa-pakket met `llvmpipe` als fallback werkt hier prima via
  `apt`; geen eigen Mesa-compilatie nodig zoals bij het LFS-traject. Houdt
  de "draait op oude hardware zonder goede videokaart"-eis overeind, en is
  hier vrijwel gratis (gewoon een pakketkeuze, geen bouwwerk).
- Geen Docker nodig als bouwsandbox — `live-build` draait native op een
  Debian/Ubuntu-basis (bv. rechtstreeks op een GitHub Actions
  `ubuntu-latest`-runner).

## Bouwfasen (efficiënte volgorde — elke fase is een klein, valideerbaar blok)
1. **Basissysteem** — `live-build`-configuratie: Debian-basis, kernel,
   bootloader, netwerk, live-boot-mechanisme. Eerste concrete bewijs: een
   ISO die boot tot een kale command-line.
2. **Desktop-laag** — XFCE via `apt` (bv. `task-xfce-desktop` of een
   minimalere losse pakketselectie), 3 werkbladen (Command Center / AI
   Matrix / Dev Studio, hotkeys Super+1/2/3, vrij uitbreidbaar door de
   gebruiker), Conky (live systeem-HUD: CPU/RAM/opslag/Tailscale-status).
3. **Visuele laag (thema-afhankelijk van Stap 0)** — GTK-thema (kleur,
   dichtheid), eigen icoonthema (bestaande open-source icoonset
   automatisch herkleurd naar het gekozen palet — geen handwerk per
   icoon), xfwm4-vensterdecoratie waar redelijk aan te passen.
4. **Tegel-manager (eigen software — het hart van dit project):**
   - Tegelvakken zijn standaard leeg/onzichtbaar. Rechtermuisknop op het
     bureaublad maakt een vak zichtbaar en laat de gebruiker kiezen wat
     erin komt: een draaiend app-venster, een PWA-snelkoppeling, of een
     data-widget.
   - Tegels tonen **altijd de echte, live app** (verkleind/gepositioneerd
     via een vensterbeheer-tool zoals `devilspie2`/`wmctrl`) — geen
     nagemaakte thumbnails. Ook klein blijft het dus echt bewegend/actueel.
   - **Organisch opbouwen ("constellaties")**, geen vooraf-plannen nodig:
     de gebruiker koppelt tegels live aan een "actieve sessie" (bv. Claude
     → blijkt GitHub + Cloudflare + Supabase nodig te hebben → allemaal
     gekoppeld terwijl je werkt); die combinatie wordt onthouden voor
     hergebruik. Geen AI die raadt — het systeem leert van wat de
     gebruiker zelf deed.
   - **Klik-op-tegel-animatie:** een verbindingslijn wordt getekend naar
     het gekoppelde icoon/tegel, gevolgd door een inzoom-transitie die de
     app daadwerkelijk opent. Gebouwd met webtechniek (HTML/CSS/SVG),
     gerenderd via de browser-engine (zie Fase 5) — geen aparte native
     rendering-stack. Bewust **geen constant live "frosted glass"-
     vervagingseffect** (te zwaar voor software-only rendering op oude
     hardware) — een lichte, statische transparantie/gradient benadert
     het gewenste gevoel goedkoop, gereserveerd voor dit ene moment.
   - **Content-tegels** in de gekozen esthetiek (bv. bij "The Machine"-
     stijl: netwerk-/relatiegrafiek-tegel, dicht-tekst-scrollende
     data-tegel) — aanvullend op de Conky-HUD-tegels.
   - **Hardware-bewuste, doorlopende tegel-limiet:** first-boot-wizard
     detecteert RAM en stelt een initieel maximum aantal "live" tegels in;
     de tegel-manager bewaakt daarna doorlopend het actuele
     geheugengebruik en waarschuwt/remt af vóór het systeem vastloopt.
     Tegels op een niet-zichtbaar werkblad pauzeren (rendering/updates
     bevriezen) zodra je wisselt, "leven op" bij terugkeer.
   - **Multi-monitor:** standaard "werkblad per scherm" bij meerdere
     monitoren (bestaande XFCE/X11-functionaliteit — geen eigen
     ontwikkeling), met een simpele instelling om in plaats daarvan één
     werkblad uit te breiden over alle schermen (nooit spiegelen). De
     tegel-limiet houdt rekening met "hoeveel werkbladen zijn tegelijk
     zichtbaar" (bij multi-monitor kan niks gepauzeerd worden — alles is
     zichtbaar tegelijk).
5. **Devstack & browser-engine** — via `apt`/officiële repositories:
   Node.js (NodeSource), Bun (officieel install-script), Python 3,
   PostgreSQL, SQLite, Supabase CLI (officiële binary), GitHub CLI
   (officiële apt-repo), n8n (npm), Tailscale (officiële apt-repo),
   cloudflared (officiële .deb), PM2 (npm) + systemd-services
   (zelfherstellend — Debian heeft wél systemd, dus dit kan hier native,
   in tegenstelling tot het LFS-traject). Browser-engine voor PWA's: een
   volwaardige browser is hier via `apt` triviaal (Chromium/Firefox-ESR
   staan gewoon in Debian's repositories) — geen eigen WebKitGTK-bouwwerk
   nodig zoals bij het LFS-traject.
6. **PWA's/webapps — generiek, niet hardcoded.** Eén "voeg webapp toe"-
   mechanisme (naam, URL, hotkey) i.p.v. losse, vooraf ingebouwde
   snelkoppelingen. De gebruiker voegt na installatie zelf toe wat die
   wil.
7. **Installer-integratie** — Calamares inbouwen in de live-omgeving,
   configureren voor partitionering/bootloader-installatie naar de
   doelschijf.
8. **`first-boot`-wizard** — draait éénmalig ná installatie: lokale
   gebruikersaanmaak (kan deels al door Calamares gebeuren), `tailscale
   up`, `gh auth login`, optionele API-keys naar `~/.env`, RAM-detectie
   voor de tegel-limiet. Schakelt zichzelf na afloop uit.

## CI-strategie
- Vrijwel alles via `apt`/binaire installs → verwacht **tientallen
  minuten** per volledige ISO-build, niet uren — moet met een eerste
  echte build bevestigd worden, niet aangenomen.
- Repo-zichtbaarheid: zie Stap 0 (vraag het, neem niet aan).
- Als er tóch iets gecompileerd moet worden (zeldzaam verwacht): dezelfde
  discipline als het LFS-zusterproject — officiële bron eerst,
  checksum-verplichte fallback-keten bij dode mirrors, nooit stilzwijgend
  een andere versie accepteren.

## Security / Secrets (grondregel, niet-onderhandelbaar)
- **Nooit hardcoded secrets, accounts of persoonlijke data in de repo.**
- `.env.example` met alleen placeholders; echte waarden uitsluitend via de
  first-boot wizard, lokaal, buiten git (`.gitignore`).
- Geen API-keys of tokens in build-configuratie/CI-workflow-bestanden.

## Efficiëntie-lessen uit het LFS-zusterproject (toepassen, niet herhalen)
- **Audit eerst, dan pas bouwen/pushen** — controleer een volledige
  pakketlijst tegen de officiële documentatie vóórdat er iets naar CI
  gaat, in plaats van één-dependency-per-push-en-wachten.
- **Checkpoints/caching per bouwfase** vanaf het begin (niet pas achteraf
  toevoegen) — elke fase cachet zijn eigen resultaat, zodat een latere
  fase niet alles ervoor hoeft te herhalen.
- **Nooit lokaal op de doelmachine van de gebruiker bouwen** — altijd via
  CI (GitHub Actions of gelijkwaardig), ook al lijkt lokaal sneller voor
  een losse test.
- **Workflow-trigger-paden breed houden** (bv. `scripts/**` i.p.v. elke
  submap los opsommen) — voorkomt dat een wijziging per ongeluk geen
  nieuwe CI-run triggert.
- Verwacht is dat deze Debian-route de meeste van deze lessen sowieso
  grotendeels vermijdt (apt is stabieler dan broncode-compilatie), maar
  blijf ze toepassen waar wél iets gecompileerd wordt.

## Wat nog volledig open staat
- Exacte `live-build`-configuratiestructuur (pakketlijsten, hooks).
- Exacte Calamares-configuratie voor de schijf-/bootloader-eisen.
- Concrete implementatie van de tegel-manager (taal/framework — een lichte
  eigen applicatie, mogelijk Python/GTK met een ingebedde webview voor de
  animaties, of een andere geschikte combinatie; aan de uitvoerder om te
  bepalen op basis van wat het snelst robuust te bouwen is).
- Personalisatie-antwoorden uit Stap 0 (nog niet ingevuld — dit document
  beschrijft het origineel als voorbeeld).

## Beslislog
- **2026-09-26 — Project herzien tot volledige, efficiënte one-shot-
  blueprint.** Alle UX-inzichten uit het originele bouwtraject (tegel-
  manager, organische constellaties, multi-monitor, hardware-bewuste
  tegel-limiet, FUI-content-tegels, generieke PWA-toevoeging) opgenomen.
  Personalisatie (thema/stijl) expliciet losgekoppeld van het mechanisme —
  Stap 0 vraagt dit uit i.p.v. het origineel klakkeloos over te nemen.
