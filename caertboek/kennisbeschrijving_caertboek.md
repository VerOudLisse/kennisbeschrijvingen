# Kennisbeschrijving Caertboek (historische eigendomskaarten)

*Cultuur-Historische Vereniging Oud Lisse · versie 1.0 · 28 oktober 2026*

## 1. Definitie

Een Caertboek is een historische kaartatlas waarin een instelling (bijvoorbeeld een abdij, klooster of weeshuis) de ligging en omvang van haar grondbezit liet vastleggen, doorgaans met een directe geometrische weergave van de percelen op kaart — in tegenstelling tot de tekstuele beschrijvingen van morgenboeken en transportregisters.

Voor het project is in eerste instantie het Caertboek van de Abdij Leeuwenhorst beschikbaar, met als referentiejaar 1625 (NA toegang 4.VTH P, inv.nr. 138). De abdij bezat meerdere eeuwen een aanzienlijk grondbezit in Lisse. Een vergelijkbare bron is de kaart met bezittingen van het Weeshuis Leiden (ELO toegang 0519, inv.nr. 584).

> **➶ Bijzondere status van deze bron:** *Het Caertboek is de enige bron in het project die vóór 1830 al een directe geometrie biedt. Dit maakt het een tweede, onafhankelijk ruimtelijk ankerpunt naast het kadaster van 1830, midden in de periode waarin de meeste transportregisters lopen (1577–1811).*

## 2. Verwerking en huidige status

De kaarten worden gegeorefereerd en de percelen worden overgebracht naar een kaartlaag in QGIS. Dit is een lopend proces; de dataset is daarmee zowel bron als product van het project.

## 3. Inhoud en attributen

### 3.1 Twee categorieën percelen

Het Caertboek bevat niet uitsluitend de percelen van de eigen instelling (in dit geval de abdij), maar toont ook de directe omgeving: de namen van belendende eigenaren worden op of bij de kaart vermeld. Dit levert twee categorieën percelen op die verschillend moeten worden behandeld:

- Eigen percelen (abdij-percelen): volledig beschreven met eigenaar, gebruiker, oppervlak, veldnaam en geometrie.

- Belendende percelen: alleen de naam van de eigenaar is bekend, als contextuele aantekening bij de perceelgrens; deze percelen hebben niet dezelfde volledigheid aan attributen als de eigen percelen.

> **➶ Implicatie voor database:** *Modelleer eigen percelen en belendende percelen niet identiek. Een belendend perceel is in de regel onvolledig (geen oppervlak, geen veldnaam) en mag niet worden behandeld alsof het dezelfde attribuutdekking heeft als een eigen perceel.*

### 3.2 Belendingsattributen

Voor de percelen wordt een attribuutstructuur gehanteerd die direct vergelijkbaar is met de belendingen uit transportakten:

- belendingEigenaar: combinatie van windrichting en eigenaarsnaam, bijvoorbeeld "Z-De nazaten van Jacob Pieterse Keijser"

- belendingObject: combinatie van windrichting en objectnaam (weg, water)

- Beide attributen kunnen meerdere waarden bevatten, gescheiden door een puntkomma (;)

Een tekstattribuut ontbreekt in twee gevallen: wanneer een perceel grenst aan een ander eigen (abdij-)perceel, of wanneer het grenst aan water of een weg zonder vermelde naam. Dit is geen omissie in de bron, maar een logische leegte.

> **➶ Implicatie voor database:** *Gebruik hetzelfde attribuutformat (windrichting-naam, gescheiden door ;) als gemeenschappelijk format tussen deze bron en de uit transportakten geëxtraheerde belendingen, om kruisvalidatie te vereenvoudigen. Een lege belending-waarde wordt geïnterpreteerd als "grenst aan eigen perceel of naamloos object", niet als ontbrekende data.*

### 3.3 Veldnamen

Percelen kunnen een veldnaam bevatten, bijvoorbeeld "Biesecamp". Veldnamen op het Caertboek zijn, net als bij morgenboeken en transportregisters, een waardevolle maar niet universeel aanwezige sleutel voor identificatie (zie de kennisbeschrijving morgenboeken, sectie 5.7).

> **➶ Implicatie voor database:** *Behandel veldnamen uit het Caertboek op dezelfde wijze als bij andere bronnen: als ondersteunende, niet als primaire sleutel. Een veldnaam die op het Caertboek én in een morgenboek of transportakte voorkomt voor hetzelfde gebied, verhoogt de betrouwbaarheidsscore van een koppeling aanzienlijk.*

### 3.4 Eigenaarsaanduidingen: "nazaten van"

Het voorbeeld "de nazaten van Jacob Pieterse Keijser" toont een eigenaarsaanduiding die verwijst naar erfgenamen zonder dat de individuele erfgenamen bij naam worden genoemd. Dit patroon kan ook in morgenboeken en transportregisters voorkomen en wijst op een overleden oorspronkelijke eigenaar wiens boedel (nog) niet individueel is verdeeld of geregistreerd.

> **➶ Implicatie voor database:** *Registreer "nazaten van \[naam\]" als een herkenbaar eigenaarspatroon, gekoppeld aan de oorspronkelijke naam, zodat deze percelen vindbaar blijven bij zoekacties op de oorspronkelijke eigenaar.*

## 4. Rol binnen de projectstrategie

### 4.1 Tweede ruimtelijk ankerpunt

Tot nu toe vormde het kadaster van 1830 het enige vaste geometrische ankerpunt in de strategie (zie de kennisbeschrijving geografie kadaster 1830). Het Caertboek voegt daar een tweede, onafhankelijk ankerpunt aan toe, gedateerd op 1625 — ruim twee eeuwen eerder, en veel dichter bij het begin van de onderzoeksperiode (1544).

Dit verandert de reconstructiestrategie wezenlijk: in plaats van uitsluitend terug te werken vanaf 1830, kan voor het gebied dat het Caertboek bestrijkt ook gewerkt worden vanuit 1625, in twee richtingen: voorwaarts richting 1830 (ter validatie van de terugwaartse reconstructie) en achterwaarts richting 1544 (met een veel kortere afstand te overbruggen dan vanaf 1830).

> **➶ Aanbeveling:** *Behandel het Caertboek niet als aanvullende attribuutbron bij percelen die al via 1830 zijn gelokaliseerd, maar als zelfstandig startpunt voor reconstructie binnen het gebied dat het bestrijkt. Voor percelen in dat gebied kan de Kavel twee onafhankelijke reconstructieroutes aanbieden (vanuit 1830 en vanuit 1625) en de resultaten onderling vergelijken.*

### 4.2 Validatie van de belendingsgraaf

Omdat het Caertboek voor 1625 al een geometrie-belendingkoppeling biedt die normaal pas vanaf 1830 beschikbaar is, kan het worden gebruikt om de belendingsgraaf uit transportakten uit de periode rond 1625 rechtstreeks te valideren — zonder eerst de hele keten via 1830 terug te hoeven leggen.

### 4.3 Meerdere institutionele bronnen

Het Caertboek van Leeuwenhorst is in potentie een van meerdere vergelijkbare institutionele kaartbronnen (zie ook de kaart van het Weeshuis Leiden). Iedere institutionele bron heeft een eigen referentiejaar en een eigen beperkt territoriaal bereik (uitsluitend het bezit van die instelling plus de directe omgeving).

> **➶ Implicatie voor database:** *Modelleer iedere institutionele kaartbron als afzonderlijke BronKaart-entiteit met een eigen referentiejaar en eigenaarsinstelling, zodat meerdere Caertboeken naast elkaar kunnen bestaan zonder dat hun gegevens worden vermengd. Percelen verwijzen naar de BronKaart waaruit zij afkomstig zijn.*

## 5. Betrouwbaarheid en beperkingen

- Het territoriale bereik is beperkt tot het bezit van de instelling en de directe omgeving; het Caertboek geeft geen dekkend beeld van heel Lisse.

- Belendende percelen zijn onvolledig beschreven (alleen eigenaarsnaam, geen oppervlak of veldnaam in de regel).

- Het referentiejaar (1625 voor Leeuwenhorst) is een momentopname; eigendomswisselingen voor of na dat jaar zijn niet in deze bron zichtbaar.

- Georeferentie van een historische kaart introduceert per definitie een zekere ruimtelijke onnauwkeurigheid, afhankelijk van de kwaliteit van de oorspronkelijke kaart en het aantal beschikbare paspunten.

- "Nazaten van"-aanduidingen leveren geen individuele identificatie van erfgenamen op.

## 6. Relatie tot andere bronnen

| **Aspect**               | **Morgenboek**        | **Transportregister** | **Kadaster 1830** | **Caertboek**                   |
|--------------------------|-----------------------|-----------------------|-------------------|---------------------------------|
| Geometrie                | Niet aanwezig         | Niet aanwezig         | Aanwezig          | Aanwezig                        |
| Referentiejaar           | Periodiek (4 jr)      | Per akte              | 1812–1818         | 1625 (Leeuwenhorst)             |
| Dekking                  | Geheel ambacht        | Geheel ambacht        | Geheel ambacht    | Bezit instelling + omgeving     |
| Belending - eigenaar     | Niet aanwezig         | Aanwezig              | Impliciet         | Aanwezig (attribuut)            |
| Belending - object       | Niet aanwezig         | Aanwezig              | Impliciet         | Aanwezig (attribuut)            |
| Veldnaam                 | Soms                  | Soms                  | Niet standaard    | Soms                            |
| Volledigheid per perceel | Hoog (eigen percelen) | Hoog                  | Hoog              | Hoog (eigen) / laag (belendend) |

## 7. Relevantie voor AI en lokalisering van percelen

### 7.1 Belendingen als direct te koppelen graafknopen

Omdat de belendingsattributen van het Caertboek al aan een geometrie zijn gekoppeld, kunnen zij zonder tussenstap worden opgenomen in de ruimtelijke graaf die ook voor transportakten wordt opgebouwd (zie de kennisbeschrijving transportregisters, sectie 8.2). Dit maakt het Caertboek een sterk ankerpunt binnen die graaf: knopen met bekende geometrie wegen zwaarder dan knopen waarvan de positie nog moet worden afgeleid.

### 7.2 Matching met transportakten en morgenboeken rond 1625

Voor de periode rond het referentiejaar is matching met transportakten en morgenboeken bijzonder kansrijk: dezelfde combinatie van eigenaarsnaam, veldnaam en belendingen kan worden gebruikt als bij de koppeling met het kadaster van 1830, maar dan met een veel kortere tijdsafstand en dus een hogere verwachte betrouwbaarheid.

> **➶ Aanbeveling:** *Geef bij matching-algoritmen een hogere prioriteit aan vergelijkingen tussen bronnen die dicht bij elkaar in de tijd liggen (bijv. een transportakte uit 1622 vergeleken met het Caertboek van 1625) dan aan vergelijkingen met een grote tijdsafstand (dezelfde akte vergeleken met het kadaster van 1830).*

### 7.3 Entiteiten

| **Entiteit**                        | **Kenmerken**                                                               | **Toelichting**                                                                                     |
|-------------------------------------|-----------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| BronKaart                           | naam (bv. "Caertboek Leeuwenhorst"), eigenaarsinstelling, referentiejaar    | Eén record per institutionele kaartbron                                                             |
| CaertboekPerceel                    | categorie (eigen/belendend), geometrie, veldnaam, oppervlak (indien bekend) | Verwijst naar BronKaart; belendende percelen hebben beperkte attribuutdekking                       |
| Eigenaar                            | naam, type (persoon/institutie/nazaten-van)                                 | Gedeeld met andere kennisbeschrijvingen                                                             |
| belendingEigenaar / belendingObject | windrichting-naam, gescheiden door ;                                        | Zelfde format als bij transportregisters; lege waarde = grenst aan eigen perceel of naamloos object |

## 8. Samenvatting

Het Caertboek is een historische eigendomskaart van een instelling — voor dit project in eerste instantie de Abdij Leeuwenhorst, referentiejaar 1625 — en is de enige bron in het project die vóór 1830 al een directe geometrie biedt. Daarmee fungeert het als tweede, onafhankelijk ruimtelijk ankerpunt naast het kadaster van 1830, veel dichter bij het begin van de onderzoeksperiode.

Kernpunten voor de verwerking:

- Eigen percelen (van de instelling) en belendende percelen moeten verschillend worden gemodelleerd vanwege hun verschil in attribuutdekking.

- De attributen belendingEigenaar en belendingObject (windrichting-naam, gescheiden door ;) zijn direct vergelijkbaar met de belendingen uit transportakten en kunnen in dezelfde ruimtelijke graaf worden opgenomen.

- Veldnamen komen voor en worden net als bij andere bronnen als ondersteunende, niet primaire sleutel behandeld.

- "Nazaten van"-aanduidingen zijn een herkenbaar eigenaarspatroon dat ook in andere bronnen voorkomt.

- Het Caertboek kan dienen als zelfstandig startpunt voor reconstructie binnen het gebied dat het bestrijkt, niet alleen als aanvullend attribuut bij percelen die al via 1830 zijn gelokaliseerd.

- Meerdere institutionele kaartbronnen (bijv. ook het Weeshuis Leiden) worden als afzonderlijke BronKaart-records gemodelleerd, elk met een eigen referentiejaar en territoriaal bereik.

