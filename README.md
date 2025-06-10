# MCIS_Kandidatoppgave

Oppgaven består av å lage en Go server med Postgres database. 

Vi deler opp i to deler. En grunnleggende oppgave og videre arbeid for dem som har tid til mer. 

Grunnleggende oppgave: 
- Last inn data i DB
- Rengjør data
- Lage http endepunkter
- Legg til en 'audit logg' i DB.

Krevende oppgave:
- Utvide datastruktur
- Utvide funksjonalitet

## Besvarelse
Lever hele besvarelsen som repo i Github. 

Det er selvsagt tillatt å bruke LLM i besvarelsen, men vi leter etter forståelse av løsningen. 
Derfor er det fint å bruke commits for å dele opp oppgaven slik at du viser løsningen stegvis. 

Når/hvis du bruker psql så legg inn i README hvilken type kommando og hvorfor, men kun de som har betydning. 
Hvis du endrer flere datatyper så holder det å beskrive arbeidet av en avn dem, og heller gi punktliste over resterende. 

**Siden dette er kandidatoppgave, så husk at:**
- Det er ingen forventning om auth, api dokumentasjon, perfekt kode, pene commits, m.m. 
- Forklar med ord hvis du ønsker å gjøre noe som tar for mye tid.
- Løsningen må kunne kjøres fra klient perspektiv, men curl kommandoer holder.
- Bonus om du har tester som kjører endepunktene for deg og demonstrerer at alt fungerer. 

## Grunnleggende oppgave

Les igjennom begge delene slik at du vet hva som bør gjøres hvor hvis du ønsker å gjøre den krevende delen av oppgaven. 

Oppgavene har hint til ønsket løsning, men det er ikke krav. Bruk tiden fornuftig, og fokuser på andre ting om du sitter fast. 

### Data til db

Data kommer fra imdb datasettet, et begrenset åpent datasett: 
https://developer.imdb.com/non-commercial-datasets/

Vi har fjernet noen tabeller og kuttet ned på antall rader til noe mer håndterbart. 

Tabellene som er igjen er:
- name_basics
- title_principals
- title_ratings
- title_basics

Data er vedlagt i to formater. Det skal være samme data i begge. 

#### tsv
Det er tsv filer i 'subset.tsv.gz' som kan brukes om du ønsker å laste inn data manuelt. 
Dette er mer for backup om du skulle ha problemer med sql import.

#### sql
Vi lastet inn data manuelt og tok en pg_dump. Dette ligger i 'imdb.sql'. 
'pg_dump' er kresen på versjoner, så det er lurt å bruke samme versjon. 
Vi brukte docker container med postgres 17.5. 


### Rengjør data

Datatypene er langt fra ideelle. Gjør de endringene du mener er fornuftige i psql. 

Beskriv hva du gjør i besvarelsen med kommando og begrunnelse. 

Hint: 
- De fleste verdiene er begrenset. Bruk derfor mindre datatyper.
- Mange av verdiene er ren TEXT. Hva er problemet med dette og hva er alternativer?
- Noen av verdiene er TEXT, som igjen er arrays. Er det et problem, og hva kan gjøres anderledes? 

### Lage endepunkter

Ønsker minst disse endepunktene:
- Liste med filmer
- Legge til ny film

Hint: 
- Søke etter filmer
  - Årstall
  - Navn
  - Skuespillere
- Legge til nye skuespillere på nye filmer.

### Audit logg
Lag en logg i postgres som viser hva som skjer, og når. 
Prøv å hold dette mest mulig på postgres siden av oppgaven. 

Hint: 
- Bruk migreringsfiler i Go hvis du kan.
- Logg enkelt når noe leses eller endres i title_basics tabellen. 
- To kolonner holder. Change og timestamp. Change kan være "Read"/"Write". 

## Krevende oppgave

**Siden den første delen av oppgaven er relativt enkel, men fremdeles krever en del tid, så kan du trygt gjøre det fort og galt om du ønsker å fokusere på denne delen.**

En del forbedringer kan gjøres i psql først. 
- Normalisering av tabeller
- "Linking" tabell for genres/knownFor/primaryProfession (Associative entity) **Kan bli tidkrevende**. 
- Primary keys
- ForeignKeys/Constraints, men obs på at datasettet kan ha ugyldige verdier og mangler. Bare fjern eller ignorer om det skulle gi problemer. 

Bruk migreringsfiler i Go for å utvide systemet stegvis når du er klar. Tenk på up/down, slik at det i teorien skal være mulig å migrere både opp og ned. 



### Datastruktur
