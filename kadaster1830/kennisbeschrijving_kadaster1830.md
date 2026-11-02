# Kennisbeschrijving geografie kadaster 1830

*Cultuur-Historische Vereniging Oud Lisse · versie 1.0 · 2 november 2026*

## 1. Definitie

De geografie kadaster 1830 is een georefereerde QGIS-dataset [aanvullen: herkomst van de geometrie en waar de gegevens raadpleegbaar zijn] die de kadastrale indeling van het ambacht Lisse vastlegt, zoals die voortkomt uit de oorspronkelijke kadastrale opmeting (uitgevoerd in de periode 1812–1818, formeel bekend als het kadaster van 1830).

Binnen het project vormt deze dataset het geografische anker waarop de terugwaartse reconstructie van percelen uit morgenboeken en transportregisters wordt gebaseerd: de strategie start bij 1830, omdat dat de meest complete en betrouwbare geografische laag is, en werkt vandaaruit terug in de tijd.

## 2. Opbouw van de dataset

### 2.1 Lagen

De dataset bestaat uit vier georefereerde lagen in QGIS:

- Percelen: de kadastrale percelen als polygonen

- Wegen: het wegennetwerk als lijnen

- Water: waterlopen en waterlichamen als lijnen of polygonen

- Kadastrale lijnen: de grenslijnen van de kadastrale percelen

De lagen wegen en water corresponderen rechtstreeks met de topografische objecten die in transportakten als belending worden genoemd (zie de kennisbeschrijving transportregisters, sectie 4.4).

### 2.2 Attributen van de percelenlaag

De percelenlaag bevat de volgende attributen. De eerste zes zijn kadastrale basisattributen; de overige zijn projectspecifieke toevoegingen.

| **Attribuut**    | **Type** | **Omschrijving**                                                                                                                                                                           |
|------------------|----------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| perceelnummer    | tekst    | Kadastrale sectie + volgnummer                                                                                                                                                             |
| voornaam         | tekst    | Voornaam eigenaar                                                                                                                                                                          |
| achternaam       | tekst    | Achternaam eigenaar                                                                                                                                                                        |
| woonplaats       | tekst    | Woonplaats eigenaar                                                                                                                                                                        |
| soortVanEigendom | tekst    | Bijv. weiland, bouwland, erf, woeste grond                                                                                                                                                 |
| oppervlak        | getal    | In m²                                                                                                                                                                                      |
| benamingRijnland | tekst    | Naam die Rijnland aan het gebied toekende; voor alle percelen gevuld. Biedt ingang voor gebieden als de Bedijkte Lisserpoel en de Poelpolder die niet via soortVanEigendom herkenbaar zijn |
| polder           | tekst    | Wijk/poldernaam (zie sectie 5.2); projectspecifiek                                                                                                                                         |
| polder_zekerheid | tekst    | hoog / laag / onbekend (zie sectie 5.3); projectspecifiek                                                                                                                                  |
| WG_bron          | tekst    | Reconstructiebron voor Woeste-grond-familie (zie sectie 6); projectspecifiek                                                                                                               |
| WG_zekerheid     | tekst    | hoog / middel / laag (zie sectie 6); projectspecifiek                                                                                                                                      |

## 3. Koppeling met morgenboeken en transportregisters

### 3.1 Koppeling als resultaat van een gebruikersbeslissing

De koppeling tussen een kadasterperceel van 1830 en een perceel zoals beschreven in een morgenboek of transportregister komt niet tot stand als het directe resultaat van een geautomatiseerd matching-algoritme, maar als het resultaat van een door de gebruiker bevestigde beslissing in Kavel.

Kavel genereert op basis van beschikbare kenmerken (eigenaarsnaam, oppervlak, polder, belendingen) een of meer voorstellen voor de ligging van een bronperceel, elk met een betrouwbaarheidsscore. De gebruiker beoordeelt deze voorstellen en accepteert er een als de vastgestelde ligging. Indien de keuze afwijkt van het voorstel met de hoogste betrouwbaarheidsscore, wordt een motivatie vastgelegd.

### 3.2 Positiezekerheid

Bij acceptatie kent de gebruiker een positiezekerheid toe: de mate van vertrouwen die de gebruiker zelf heeft in de toegekende ligging. Dit is uitdrukkelijk geen algoritmische matching-score maar een menselijk oordeel dat ook rekening kan houden met kennis die het algoritme niet ziet.

> **➶ Implicatie voor database:** *Modelleer de koppeling als entiteit PerceelKoppeling met velden: bron_perceel_referentie, kadaster_perceelnummer, positiezekerheid (gebruiker), algoritmische_score (systeem), en motivatie (verplicht bij afwijking van het hoogst scorende voorstel).*

### 3.3 Eén-op-meer en meer-op-één relaties

Omdat percelen in de loop der tijd gesplitst of samengevoegd kunnen zijn, is de koppeling niet noodzakelijk één-op-één. Sta toe dat PerceelKoppeling een meer-op-meer relatie vastlegt tussen bronpercelen en kadasterpercelen.

## 4. Topologie

### 4.1 Topologie bestaat nu alleen als geometrie

De kadastrale lijnenlaag legt vast wáár de perceelgrenzen liggen, maar niet welke percelen aan elkaar grenzen, over welke lengte, of in welke richting. Deze relationele informatie moet nog uit de geometrie worden afgeleid.

> **➶ Implicatie voor verwerking:** *Genereer de perceeltopologie als afzonderlijke GIS-bewerking in QGIS (buurschapsanalyse), die per perceel de aangrenzende percelen, de gedeelde grenslengte en de globale windrichting van die grens oplevert.*

### 4.2 Gebruik van de topologie

De afgeleide topologie dient als validatiemiddel voor de belendingsgraaf uit transportakten: een belending die stelt dat perceel A aan de westzijde grenst aan perceel B, moet overeenkomen met de kadastrale topologie — mits beide percelen al gekoppeld zijn. Afwijkingen wijzen op een foutieve koppeling, een perceelwijziging, of een leesfout in de transcriptie.

## 5. Poldergeometrie

### 5.1 Uitgangspunt: afgeleid uit de percelenlaag

De poldergeometrie wordt niet als zelfstandige polygonenlaag gemodelleerd, maar als afgeleid resultaat van de percelenlaag via een Dissolve-bewerking op het attribuut polder. Dit houdt de poldergeometrie consistent met de onderliggende percelen.

### 5.2 Werkwijze

- Voeg aan de percelenlaag het attribuut polder toe (tekstveld).

- Genereer de poldergrens via een Dissolve-bewerking: alle percelen met dezelfde polderwaarde worden samengevoegd tot één polygoon.

- De poldergeometrie wijzigt automatisch mee bij herziening van het polder-attribuut.

### 5.3 Onzekere grenzen

Een deel van de poldergrenzen berust op aannames die tijdens het project worden getoetst. Het attribuut polder_zekerheid (hoog / laag / onbekend) per perceel markeert de rafelrand. De poldergeometrie kan in twee varianten worden getoond: een harde kernpolygoon (alleen hoge zekerheid) en een ruimere polygoon inclusief de onzekere percelen.

> **➶ Implicatie voor Kavel:** *Behandel de poldergrens als grof filter bij het zoeken naar kandidaat-locaties, niet als harde uitsluitingsgrens. Doorzoek voor percelen op de rafelrand ook de direct aangrenzende polders, met lagere prioriteit.*

## 6. Reconstructie van de Woeste-grond-familie

### 6.1 Achtergrond en terminologie

De schrikkeljaarmorgenboeken dekken niet het gehele grondgebied van het ambacht Lisse. Een van de gebieden die buiten de dekking vallen, is de Woeste grond. Daartoe behoren ook gebieden die in historische bronnen onder andere benamingen voorkomen:

- Wildernis — een synoniem voor Woeste grond; de term komt niet voor in het kadaster van 1830 maar wel in historische bronnen.

- Berkhouterduintje — een afzonderlijk gebied dat op gelijke wijze als Woeste grond wordt behandeld.

Samen worden deze gebieden in dit document aangeduid als de Woeste-grond-familie. Voor de afbakening ervan zijn twee bronnen beschikbaar:

- Het kadaster van 1830, waar soortVanEigendom voor een deel van de percelen de waarde "Woeste grond" heeft.

- Een kaart uit 1615 [aanvullen: archief en inventarisnummer] die de omvang van de Woeste grond in die periode nauwkeurig aangeeft.

Geen van beide bronnen geeft direct de situatie in 1544, maar samen bieden zij een best-guess reconstructie. De onzekerheid wordt expliciet vastgelegd per perceel via de attributen WG_bron en WG_zekerheid.

> **➶ Naamgeving:** *De attributen dragen geen jaarsuffix zoals WG-1544, omdat de reconstructie een best-guess is van de situatie rond 1544, niet een geattesteerde toestand. Het referentiejaar staat in de metadata van de reconstructie, niet in de attribuutnaam.*

### 6.2 Attribuutstructuur

| **Attribuut** | **Waarde**             | **Betekenis**                                                                                      |
|---------------|------------------------|----------------------------------------------------------------------------------------------------|
| WG_bron       | kadaster1830           | Perceel is in 1830 als Woeste grond geregistreerd (soortVanEigendom)                               |
|               | kaart1615              | Perceel is op basis van de kaart van 1615 aangemerkt, maar was in 1830 al omgezet naar iets anders |
|               | kadaster1830+kaart1615 | Beide bronnen bevestigen het perceel                                                               |
|               | reconstructie          | Perceel toegevoegd op basis van redenering (bijv. ingesloten ligging)                              |
|               | (leeg)                 | Niet van toepassing                                                                                |
| WG_zekerheid  | hoog                   | Beide bronnen bevestigen (kadaster1830+kaart1615)                                                  |
|               | middel                 | Één bron bevestigt (kadaster1830 óf kaart1615 alleen)                                              |
|               | laag                   | Reconstructie op basis van redenering                                                              |
|               | (leeg)                 | Niet van toepassing                                                                                |

### 6.3 Werkwijze in QGIS

**Stap 1 — Attributen toevoegen**

Voeg de attributen WG_bron en WG_zekerheid toe aan de percelenlaag van 1830 als tekstvelden.

**Stap 2 — Kadaster 1830 verwerken**

Selecteer alle percelen waarbij soortVanEigendom = 'Woeste grond'. Vul in via de veldcalculator:

- WG_bron = 'kadaster1830'

- WG_zekerheid = 'middel'

**Stap 3 — Kaart van 1615 verwerken**

Georefereer de kaart van 1615 en breng de contour van het Woeste-grond-gebied over als tijdelijke polygoonlaag. Selecteer percelen die ruimtelijk overlappen met die contour:

- Percelen zonder WG_bron-waarde: WG_bron = 'kaart1615', WG_zekerheid = 'middel'.

- Percelen met WG_bron = 'kadaster1830': update naar WG_bron = 'kadaster1830+kaart1615', WG_zekerheid = 'hoog'.

- Herhaal stap 3 voor het Berkhouterduintje op basis van de relevante historische kaart of contour.

**Stap 4 — Reconstructie ingesloten percelen**

Beoordeel visueel of percelen ingesloten liggen tussen reeds aangeduide percelen zonder zelf een waarde te hebben. Voeg die handmatig toe:

- WG_bron = 'reconstructie'

- WG_zekerheid = 'laag'

### 6.4 Gebruik in Kavel

De Woeste-grond-reconstructie maakt het mogelijk de gebieden die buiten de morgenboekdekking vallen, geografisch af te bakenen. In de Kavel worden deze gebieden getoond als zone zonder morgenboekdekking. Percelen met een gevulde WG_bron-waarde fungeren als negatief filter: bronpercelen uit morgenboeken worden niet op deze percelen gelokaliseerd, tenzij de gebruiker dit expliciet overschrijft.

> **➶ Aanbeveling:** *Genereer de buitengrens van de Woeste-grond-reconstructie via een Dissolve-bewerking op alle percelen met een gevulde WG_bron-waarde, analoog aan de poldergeometrie.*

## 7. Gebieden buiten de morgenboekdekking via benamingRijnland

Naast de Woeste-grond-familie vallen ook de Bedijkte Lisserpoel en de Poelpolder buiten de dekking van de schrikkeljaarmorgenboeken. Anders dan de Woeste-grond-familie zijn deze gebieden niet herkenbaar via soortVanEigendom, maar via het attribuut benamingRijnland, dat voor alle percelen is gevuld met de naam die Rijnland aan het gebied toekende.

De geografische afbakening van deze gebieden verloopt daarmee via een selectie op benamingRijnland in plaats van op soortVanEigendom, maar het principe is hetzelfde: de geselecteerde percelen worden gemarkeerd als buiten de morgenboekdekking en worden in Kavel als negatief filter gebruikt.

> **➶ Aandachtspunt:** *Let op het naamgevingsrisico rondom de Poelpolder: in de morgenboeken verwijst "Poelpolder" naar de strook land langs de Heereweg, niet naar de via benamingRijnland herkenbare Bedijkte Lisserpoel. Zie ook de kennisbeschrijving morgenboeken, sectie 4.3.*
>
> **➶ Vervolgstap:** *Stel een lijst op van de waarden die benamingRijnland aanneemt voor de Bedijkte Lisserpoel en de Poelpolder, zodat de selectie in QGIS eenduidig kan worden uitgevoerd.*

## 8. Betrouwbaarheid en beperkingen

- De kadastrale opmeting dateert feitelijk uit 1812–1818; de naamgeving "kadaster 1830" is een projectconventie.

- Bij veelvoorkomende namen kan aanvullende identificatie van eigenaren nodig zijn.

- soortVanEigendom is een momentopname uit 1830 en kan afwijken van het gebruik in eerdere perioden.

- De koppeling met morgenboek- en transportregisterpercelen is mensafhankelijk en komt geleidelijk tot stand.

- De Woeste-grond-reconstructie is een best-guess; WG_bron en WG_zekerheid maken de onzekerheid per perceel expliciet.

- De term Wildernis is een synoniem voor Woeste grond en komt niet als zodanig voor in het kadaster van 1830; herkenning in historische bronnen vereist kennis van dit synoniem.

## 9. Relevantie voor AI en lokalisering van percelen

### 9.1 Als ruimtelijk eindpunt van de reconstructie

De geografie van 1830 is het vaste eindpunt waarnaar alle terugwaartse reconstructie toewerkt. Voor ieder ouder bronperceel is het uiteindelijke doel een koppeling aan één of meer kadastrale percelen van 1830 via de entiteit PerceelKoppeling.

### 9.2 Topologie als validatiemiddel

De afgeleide topologie is het primaire middel om de belendingsgraaf uit transportakten te valideren. Consistentie verhoogt de betrouwbaarheidsscore; inconsistentie signaleert een conflict dat nader onderzoek vereist.

### 9.3 Negatieve filters

Twee soorten percelen fungeren als negatief filter voor morgenboek-lokalisering:

- Percelen met een gevulde WG_bron-waarde (Woeste-grond-familie): buiten morgenboekdekking via soortVanEigendom en/of kaart 1615.

- Percelen met een relevante benamingRijnland-waarde (Bedijkte Lisserpoel, Poelpolder): buiten morgenboekdekking via het Rijnlandse gebiedsattribuut.

In beide gevallen worden bronpercelen uit morgenboeken niet op deze percelen gelokaliseerd, tenzij de gebruiker dit expliciet overschrijft.

### 9.4 Entiteiten

| **Entiteit**           | **Kenmerken**                                                                                                            | **Toelichting**                                                     |
|------------------------|--------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------|
| KadasterPerceel1830    | perceelnummer, geometrie, oppervlak, soortVanEigendom, benamingRijnland, polder, polder_zekerheid, WG_bron, WG_zekerheid | Basisrecord; geometrie is bronhoudend                               |
| Eigenaar1830           | voornaam, achternaam, woonplaats                                                                                         | Gekoppeld aan KadasterPerceel1830                                   |
| TopologieRelatie       | perceel A, perceel B, gedeelde grenslengte, windrichting                                                                 | Afgeleid via GIS-bewerking                                          |
| PerceelKoppeling       | bron_perceel_referentie, kadaster_perceelnummer, positiezekerheid, algoritmische_score, motivatie                        | Meer-op-meer; resultaat van gebruikersbeslissing in Kavel       |
| Polder1830             | naam (Dissolve op attribuut polder)                                                                                      | Gegenereerd, niet getekend                                          |
| WoesteGrondZone        | geometrie (Dissolve op percelen met gevulde WG_bron)                                                                     | Negatief filter; omvat Woeste grond, Wildernis en Berkhouterduintje |
| BuitenDekking_Rijnland | geometrie (Dissolve op relevante benamingRijnland-waarden)                                                               | Negatief filter voor Bedijkte Lisserpoel en Poelpolder              |

## 10. Samenvatting

De geografie kadaster 1830 is de georefereerde QGIS-dataset die het ruimtelijke anker vormt voor het hele reconstructieproces. Zij bestaat uit vier lagen (percelen, wegen, water, kadastrale lijnen), met een uitgebreid attribuutpakket dat zowel kadastrale basisgegevens als projectspecifieke toevoegingen omvat.

De gebieden buiten de morgenboekdekking zijn onderverdeeld in twee groepen met een eigen identificatiemethode:

- De Woeste-grond-familie (Woeste grond, Wildernis als synoniem, en Berkhouterduintje): herkenbaar via soortVanEigendom en/of de kaart van 1615; gereconstrueerd via de attributen WG_bron en WG_zekerheid per perceel, samengevat in de entiteit WoesteGrondZone.

- De Bedijkte Lisserpoel en de Poelpolder: herkenbaar via benamingRijnland; afgebakend via selectie op dat attribuut en samengevat in de entiteit BuitenDekking_Rijnland.

Beide groepen fungeren als negatief filter in Kavel: bronpercelen uit morgenboeken worden niet op deze percelen gelokaliseerd, tenzij de gebruiker dit expliciet overschrijft.

