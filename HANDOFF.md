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
