# BLUEPRINT — Citizen Dev & AI Command Center ISO (Debian-based variant)

## Context — lees dit eerst
Dit is een **bewust parallel, alternatief bouwtraject** naast een zusterproject
(`~/Projecten/brionize-command-center`) dat hetzelfde eindproduct bouwt via
LFS/BLFS (Linux From Scratch — alles compileren vanaf broncode). Dat traject
werkt en boekt aantoonbare voortgang, maar is traag en arbeidsintensief
(uren per build-iteratie, veel losse afhankelijkheidsbugs).

Dit document beschrijft een **tweede, sneller pad naar hetzelfde product**:
een bestaande, minimale Linux-distributie (Debian) als basis nemen in plaats
van alles vanaf nul te compileren. Dit is **geen diefstal of grijs gebied** —
een bestaande distributie als basis gebruiken en aanpassen is standaardpraktijk
in de Linux-wereld en expliciet toegestaan onder open-sourcelicenties (GPL
e.d.), zolang aan de licentievoorwaarden wordt voldaan (bronvermelding, en
gewijzigde GPL-broncode zelf ook beschikbaar stellen als je die verspreidt —
hier niet aan de orde, wij wijzigen geen pakketbroncode, we configureren en
installeren alleen). Bekende voorbeelden van dezelfde aanpak: Ubuntu (op
Debian), Linux Mint (op Ubuntu), Raspberry Pi OS (op Debian), Kali Linux (op
Debian).

**Deze twee trajecten zijn strikt gescheiden projecten** (eigen map, eigen
git-geschiedenis, geen gedeelde state) — behandel ze niet als hetzelfde
project met twee substappen.

## Doel (identiek aan het LFS/BLFS-zusterproject)
Een universele, geautomatiseerd gebouwde installer-ISO. Boot vanaf USB op
willekeurige (oudere) hardware en installeert zichzelf permanent op de
interne schijf: een 24/7 "Citizen Developer & AI Command Center" — een
lichtgewicht werkplek voor iemand die van idee tot eindproduct bouwt met AI,
automatisering (n8n) en developer-tooling, en het liefst op oude hardware
zonder (goede) GPU.

## Architectuur / Aanpak — dit is het verschil met het zusterproject
- **Basis: Debian (stable), minimaal geïnstalleerd** — geen bloatware,
  gestript tot het hoognodige, maar via `apt` (kant-en-klare binaire
  pakketten) in plaats van broncode-compilatie. Dit levert vanzelf brede
  hardware-ondersteuning (Debian's standaardkernel ondersteunt al een zeer
  breed scala aan hardware) — dezelfde eis als het zusterproject
  ("generieke hardware, geen GPU-afhankelijkheid"), maar zonder dat we zelf
  een kernelconfig hoeven te bouwen.
- **Build-tool: `live-build`** (het officiële Debian-gereedschap voor het
  bouwen van aangepaste live/installer-ISO's — `debian-live` project).
  Dit is de "officiële route" (documentatie, breed gebruikt, voorspelbaar)
  in plaats van zelf een ISO-bouwpad te verzinnen.
- **Installer: Calamares** — een distributie-onafhankelijk, grafisch
  installatieprogramma dat vanuit de live-omgeving het systeem permanent
  naar de interne schijf van de doelmachine installeert (partitioneren,
  formatteren, bootloader plaatsen). Veelgebruikt door bestaande
  "respins"/aangepaste distributies (bv. KDE neon, Manjaro,
  EndeavourOS) — precies ons scenario. Voorkomt dat we zelf een
  installer-mechanisme moeten uitvinden (zoals bij het LFS/BLFS-traject
  wél nodig zou zijn).
- **Type ISO: volledige installer-naar-schijf**, geen live-boot-only
  (zelfde reden als het zusterproject: hitte/slijtage van een
  persistent-USB-stick, 24/7-gebruiksdoel).
- Geen Docker nodig als bouwsandbox — `live-build` draait native op een
  Debian/Ubuntu-basis (bv. rechtstreeks op een GitHub Actions
  `ubuntu-latest`-runner, of desnoods in een lichte container als dat
  praktischer blijkt).

## Bouwfasen
1. **Basissysteem** — `live-build`-configuratie opzetten: Debian-basis,
   pakketlijst voor de kernel/bootloader/netwerk, live-boot-mechanisme.
2. **Desktop-laag** — XFCE (via `apt install task-xfce-desktop` of losse
   XFCE-pakketten, dark theme), 3 standaard werkbladen (Command Center /
   AI Matrix / Dev Studio, hotkeys Super+1/2/3, door gebruiker vrij
   uitbreidbaar), Conky (live systeem-HUD: CPU/RAM/opslag, Tailscale-status,
   logs — puur tekst/grafieken, embedt geen andere vensters), een apart
   window-tiling-mechanisme (bv. `devilspie2`/`wmctrl`) voor live
   app-tegels/PiP-gevoel (bewust gescheiden van Conky, zie hierboven).
3. **Devstack & apps** — via `apt` en officiële install-repositories/scripts:
   Node.js (NodeSource-repo), Bun (officieel install-script), Python 3,
   PostgreSQL, SQLite, Supabase CLI (officiële binary release), GitHub CLI
   (officiële apt-repo), n8n (npm), Tailscale (officiële apt-repo),
   cloudflared (officiële .deb), PM2 (npm) + systemd watchdogs
   (zelfherstellend), PWA-snelkoppelingen voor Claude AI, ChatGPT, Mistral
   AI en Gemini met hotkeys (Super+C/G/M/A).
4. **Installer-integratie** — Calamares inbouwen in de live-omgeving,
   configureren voor partitionering/bootloader-installatie naar de
   doelschijf.
5. **`first-boot`-wizard** — draait éénmalig ná installatie op de doel-pc:
   lokale gebruikersaanmaak (kan deels al door Calamares zelf gebeuren —
   uitzoeken welk deel waar hoort), `tailscale up`, `gh auth login`,
   optionele API-keys (Anthropic/OpenAI/Gemini/Mistral) naar `~/.env`.
   Schakelt zichzelf na afloop zelfstandig uit.

## CI-strategie (verwacht veel lichter dan het LFS/BLFS-traject)
- Omdat vrijwel alles via `apt`/binaire installs gaat (geen
  broncode-compilatie van een compiler/kernel/desktopomgeving), verwachten
  we dat een volledige ISO-build **ruim binnen tientallen minuten** past op
  een standaard GitHub Actions-runner — niet de 1-2+ uur per iteratie die
  het LFS/BLFS-traject nodig had. Dit moet met een eerste echte build
  bevestigd worden, niet aangenomen.
- **Repo: publiek aanraden** (zelfde reden als het zusterproject:
  onbeperkte gratis Actions-minuten tijdens de bouwfase), maar dit is aan
  degene die dit uitvoert om te bevestigen — geen stilzwijgende aanname.
- Als er tóch een aangepast pakket gecompileerd moet worden (zeldzaam
  verwacht in dit traject): dezelfde discipline als het zusterproject —
  officiële bron eerst, checksum-verplichte fallback-keten bij dode
  mirrors, nooit stilzwijgend een andere versie accepteren.

## Security / Secrets (grondregel, niet-onderhandelbaar — zelfde als het zusterproject)
- **Nooit hardcoded secrets, accounts of persoonlijke data in de repo.**
- `.env.example` met alleen placeholders; echte waarden uitsluitend via de
  first-boot wizard op de doel-pc, lokaal, buiten git (`.gitignore`).
- Geen API-keys of tokens in build-configuratie/CI-workflow-bestanden.

## Wat nog volledig open staat
- Exacte `live-build`-configuratiestructuur (pakketlijsten, hooks) nog niet
  uitgewerkt — begin bij de officiële Debian Live Manual.
- Exacte Calamares-configuratie voor onze specifieke schijf-/bootloader-eisen
  nog niet uitgewerkt.
- Relatie/overlap met het first-boot-wizard-ontwerp van het zusterproject
  (kan grotendeels hergebruikt worden qua *idee*, niet qua code — dit is een
  ander besturingssysteem).

## Beslislog
- **2026-09-22 — Project gestart als bewust parallel alternatief.** Reden:
  het LFS/BLFS-zusterproject werkt, maar is traag; deze variant onderzoekt
  of hetzelfde eindproduct sneller/betrouwbaarder via een bestaande
  distributie-basis gebouwd kan worden. Geen vervanging, geen GO om het
  andere traject te stoppen — puur een parallelle verkenning.
