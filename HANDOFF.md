# HANDOFF

## Voor de AI die dit oppakt (bv. Mistral) — lees dit eerst, volledig

Dit is een **eerlijke, volledige overdracht** van een softwareproject in de
allereerste opzetfase. Er is nog geen regel code geschreven. De opdracht:

1. **Lees eerst BLUEPRINT.md en PROGRESS.md, helemaal, vóór je iets bouwt.**
   BLUEPRINT.md bevat het volledige productdoel, de gekozen architectuur en
   de onderbouwing daarvoor. PROGRESS.md bevat de actuele status (op dit
   moment: niets gebouwd) en de voorgestelde eerste stap.
2. **Daarna mag je zelfstandig (autopilot) doorwerken** binnen wat in
   BLUEPRINT.md is vastgelegd — je hoeft niet voor elke implementatiestap
   terug te vragen. Wél teruggeven aan de mens (Brionize) bij:
   - een echt productbeslispunt waarvoor meerdere geldige richtingen
     bestaan en de keuze het eindproduct wezenlijk bepaalt (bijvoorbeeld:
     welke specifieke Debian-versie, een architectuurkeuze die niet al in
     BLUEPRINT.md staat);
   - iets dat geld kost of een externe, blijvende verplichting aangaat;
   - een actie die naar buiten toe publiceert/verzendt namens Brionize;
   - een destructieve of moeilijk terug te draaien actie;
   - een nieuwe privacy-, security- of rechtengrens.
   Voor gewone implementatiedetails, scripts schrijven, bugs oplossen,
   commits maken: gewoon doorgaan, geen toestemming per stap nodig.

## Niet-onderhandelbare regels, ongeacht welke AI dit uitvoert
- **Nooit hardcoded secrets, wachtwoorden, API-keys of persoonlijke gegevens
  in de repository** — ook niet tijdelijk, ook niet in een voorbeeldwaarde
  die op een echte lijkt. Alleen `.env.example` met lege placeholders; echte
  waarden komen pas op de doelmachine, via de first-boot-wizard.
- **Evidence before done:** claim nooit dat een build werkt, een ISO
  bootbaar is, of een stap "klaar" is zonder dat daadwerkelijk getest en
  bewezen (bijvoorbeeld: een succesvolle CI-run, een daadwerkelijk gebouwde
  ISO). Eén grondige verificatie is beter dan tien halve.
- **Officiële bron eerst.** Gebruik voor elk onderdeel (Debian Live Manual,
  pakketdocumentatie, Calamares-documentatie) de officiële, actuele
  documentatie — niet uit het geheugen gokken over versies/commando's die
  kunnen zijn veranderd.
- **Documenteer terwijl je werkt.** Werk PROGRESS.md continu bij (wat is
  gedaan, wat werkte niet en waarom, wat is de volgende stap) en houd
  BLUEPRINT.md actueel als architectuurkeuzes concreter worden. Dit is geen
  eenmalige overdracht — het moet voor een volgende sessie (van welke AI
  dan ook) opnieuw leesbaar en bruikbaar zijn zonder te hoeven gokken.
- **Reproduceerbaar en projectgebonden.** Deze map staat los van elk ander
  project (waaronder het LFS/BLFS-zusterproject) — geen code, secrets of
  state daaruit hergebruiken zonder dat expliciet te vermelden.

## Context die je moet kennen maar niet hoeft te beheren
Er bestaat een **parallel, ander bouwtraject** voor hetzelfde eindproduct,
via een volledig andere aanpak (Linux From Scratch, alles vanaf broncode
compileren) — dat project is elders en heeft al aantoonbare voortgang. Dit
hier is een bewust apart, sneller alternatief, geen vervolg of afhankelijke
stap daarvan. Je hoeft dat andere project niet te lezen of te raadplegen om
hier te kunnen beginnen — alle benodigde context staat in BLUEPRINT.md.

## Eerstvolgende stap
Zie PROGRESS.md — begin met de git-repo, raadpleeg de officiële Debian Live
Manual, en bouw eerst een minimaal kaal basissysteem als eerste bewijs
vóór je verder gaat naar de desktop-laag.
