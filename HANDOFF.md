# HANDOFF

## Voor de AI die dit oppakt (bv. Mistral, of een andere Claude-sessie) — lees dit eerst, volledig

Dit is een **eerlijke, volledige overdracht** van een softwareproject in de
allereerste opzetfase. Er is nog geen regel code geschreven. Doel: zo min
mogelijk verspilde stappen — dit document is bewust volledig genoeg om
direct te kunnen beginnen.

### Stap 1 — VERPLICHT eerst: personalisatie uitvragen
**Voordat je ook maar één bestand aanmaakt of één commando uitvoert**, lees
BLUEPRINT.md volledig, en stel de persoon die dit aan je gegeven heeft de
vragen uit BLUEPRINT.md's sectie "STAP 0 — VERPLICHT: personalisatie vragen
vóór er iets gebouwd wordt". Kort samengevat: welk visueel thema/stijl
(het origineel gebruikt een "Person of Interest / The Machine"-esthetiek,
maar dat is een voorbeeld — het kan net zo goed Star Wars zijn, of iets
heel anders), welke devstack-onderdelen, welke AI-webapps als voorbeeld,
en repo-zichtbaarheid/doelhardware.

**Pas ná die antwoorden** ga je verder: werk BLUEPRINT.md en PROGRESS.md
bij met de gekozen personalisatie, en begin dan pas met bouwen.

### Stap 2 — daarna: zelfstandig (autopilot) doorwerken
Je hoeft daarna niet voor elke implementatiestap terug te vragen. Wél
teruggeven aan de mens bij:
- een echt productbeslispunt met meerdere geldige richtingen die het
  eindproduct wezenlijk bepalen (en dat niet al door Stap 1 is afgevangen);
- iets dat geld kost of een externe, blijvende verplichting aangaat;
- een actie die naar buiten toe publiceert/verzendt namens de gebruiker;
- een destructieve of moeilijk terug te draaien actie;
- een nieuwe privacy-, security- of rechtengrens.
Voor gewone implementatiedetails, scripts schrijven, bugs oplossen,
commits maken: gewoon doorgaan.

## Toegang en werkwijze die je nodig hebt om dit zelfstandig te bouwen
Dit is geen chatgesprek waarin je alleen tekst/code voorstelt — je hebt
**daadwerkelijke uitvoeringstoegang** nodig om dit autonoom te bouwen:

- **Terminal/CLI-toegang** (zoals Claude Code, of een gelijkwaardige agent
  met shell-toegang) — je moet zelf commando's kunnen uitvoeren, niet alleen
  code kunnen voorstellen die de mens los moet plakken/uitvoeren.
- **Git, geïnstalleerd en werkend.**
- **GitHub CLI (`gh`) of gelijkwaardig, geauthenticeerd** op het GitHub-
  account van de gebruiker — nodig om zelf een repository aan te maken,
  te pushen, en (via GitHub Actions of een gelijkwaardige CI-dienst) builds
  te starten en de uitkomst te controleren.
- Als een van deze ontbreekt: **vraag de gebruiker dit eerst te regelen**
  (bijvoorbeeld: "log in met `gh auth login`") vóórdat je verdergaat — geef
  dit niet stilzwijgend op en probeer niet te bouwen zonder deze toegang.

**Concrete werkwijze, gebaseerd op wat in het LFS/BLFS-zusterproject
bewezen werkte:**
1. Maak een lokale projectmap en initialiseer een git-repository
   (`git init`), met een lokale (niet-globale) commit-identiteit op naam
   van de gebruiker.
2. Maak zelf, via `gh repo create`, een **publieke** GitHub-repository aan
   (tenzij de gebruiker bij Stap 0/1 uitdrukkelijk privé koos) — publiek
   geeft onbeperkte, gratis CI-minuten tijdens de bouwfase; leg deze keuze
   uit aan de gebruiker in plaats van hem stilzwijgend te maken als hij er
   niet naar gevraagd is.
3. Schrijf de `live-build`-configuratie/scripts lokaal, commit, push.
4. **Bouw en test uitsluitend via CI (GitHub Actions of gelijkwaardig)**,
   nooit lokaal op de machine van de gebruiker — zie de
   Efficiëntie-discipline hieronder voor de reden (dit voorkomt dat een
   zware ISO-build de eigen computer van de gebruiker vastzet, wat in het
   zusterproject een keer echt fout ging).
5. Controleer de daadwerkelijke CI-uitkomst (niet aannemen dat iets werkt)
   vóórdat je verder gaat naar de volgende stap of dit als "klaar"
   documenteert.

## Re-entry-protocol — als een NIEUWE sessie dit oppakt
Dit project kan op elk moment door een andere sessie, een andere AI, of
dezelfde AI na een lange onderbreking worden hervat. **Bouw dan nooit
blind verder op wat je "denkt te herinneren".** Bij elke hervatting, vóór
de eerste wijzigende actie:

1. Lees dit HANDOFF.md volledig, opnieuw — ook als je denkt dit project al
   te kennen.
2. Lees PROGRESS.md voor de laatst vastgelegde status en eerstvolgende
   stap.
3. Lees BLUEPRINT.md voor de actuele architectuur- en personalisatiekeuzes
   (deze kunnen zijn gewijzigd sinds een eerdere sessie).
4. Controleer de **daadwerkelijke, actuele staat**, niet wat de documenten
   beweren: `git log` / `git status` voor de laatste commits en of de
   werkmap schoon is, en de status van de laatste CI-run (geslaagd,
   gefaald, of nog bezig) vóórdat je verdergaat.
5. Pas als dat allemaal overeenstemt: ga verder vanaf de eerstvolgende stap
   die uit die controle blijkt — niet vanaf een aanname.

Als PROGRESS.md/BLUEPRINT.md het niet eens lijken te zijn met wat je in de
repo/CI aantreft: benoem dat conflict expliciet aan de gebruiker in plaats
van te gokken welke waarheid klopt.

## Niet-onderhandelbare regels, ongeacht welke AI dit uitvoert
- **Nooit hardcoded secrets, wachtwoorden, API-keys of persoonlijke
  gegevens in de repository** — ook niet tijdelijk. Alleen `.env.example`
  met lege placeholders; echte waarden komen pas op de doelmachine, via de
  first-boot-wizard.
- **Evidence before done:** claim nooit dat een build werkt of een ISO
  bootbaar is zonder dat daadwerkelijk getest en bewezen (bijvoorbeeld: een
  succesvolle CI-run, een daadwerkelijk gebouwde ISO).
- **Officiële bron eerst.** Debian Live Manual, Calamares-documentatie,
  pakketdocumentatie — actueel en officieel, niet uit het geheugen gokken
  over commando's/versies die kunnen zijn veranderd.
- **Efficiëntie-discipline uit BLUEPRINT.md volgen** (audit eerst, niet
  één-fix-per-push; checkpoints vanaf het begin; nooit lokaal bouwen op de
  machine van de gebruiker, altijd via CI; brede workflow-trigger-paden).
- **Documenteer terwijl je werkt.** PROGRESS.md continu bijwerken (wat is
  gedaan, wat werkte niet en waarom, volgende stap); BLUEPRINT.md actueel
  houden als keuzes concreter worden.
- **Reproduceerbaar en projectgebonden.** Deze map staat los van elk ander
  project (waaronder het LFS/BLFS-zusterproject) — geen code, secrets of
  state daaruit hergebruiken zonder dat expliciet te vermelden.

## Context die je moet kennen maar niet hoeft te beheren
Er bestaat een **parallel, ander bouwtraject** voor hetzelfde eindproduct,
via Linux From Scratch (alles vanaf broncode compileren) — dat project
bestaat elders en heeft al een werkend basissysteem + desktop + devstack,
maar kostte veel tijd juist door het zelf-compileren. Deze Debian-variant
is bewust sneller opgezet. Je hoeft dat andere project niet te lezen om
hier te kunnen beginnen — alle benodigde context staat in BLUEPRINT.md.

## Eerstvolgende stap
1. Stel de Stap 0/1-vragen (personalisatie).
2. Verwerk de antwoorden in BLUEPRINT.md/PROGRESS.md.
3. Begin met de `live-build`-basisconfiguratie (BLUEPRINT.md, Bouwfase 1) en
   valideer die als eerste, kleine, bewezen stap vóór je verder gaat.
