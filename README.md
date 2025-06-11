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
- To kolonner holder. Change og timestamp. Change kan være "Insert", "Delete", "Update".
- Du trenger ikke å støtte alle typer operasjoner. Det holder å demonstrere at du har kontroll på hvordan det kan gjøres. 

## Krevende oppgave

**Siden den første delen av oppgaven er relativt enkel, men fremdeles krever en del tid, så kan du trygt gjøre det fort og galt om du ønsker å fokusere på denne delen.**

En del forbedringer kan gjøres i psql først. 
- Normalisering av tabeller
- "Linking" tabell for genres/knownFor/primaryProfession (Associative entity) **Kan bli tidkrevende, så det holder med en av dem**. 
- Primary keys
- ForeignKeys/Constraints, men obs på at datasettet kan ha ugyldige verdier og mangler. Bare fjern eller ignorer om det skulle gi problemer. 

Bruk migreringsfiler i Go for å utvide systemet stegvis når du er klar. Tenk på up/down, slik at det i teorien skal være mulig å migrere både opp og ned. 



### Utvidelse

Prøv å hold deg til migreringsfiler i videre arbeid, og tenkt på at data ikke skal gå tapt i migreringer til utvidet funksjonalitet. 

Utvidelsen går ut på å legge til personer, venner, i systemet, slik at folk kan finne en god film å se på sammen, kommentere på filmer, rate osv. 

Legg til de endepunktene du mener er nødvendig, men bonus om du har med update og delete. 

#### Felles interesse

Legg til tabeller: 
- Personer, nok med kallenavn.
- Favoritt sjangre, en tabell som knytter personer mot hvilke sjangre de ønsker å se
- Egen tabell for sjangre om du ikke gjorde de i første del.

Basert på dette, finn ut de sjangrene gruppen har til felles. 

#### Updates vs. reads

Lat som om vi har masse trafikk, og vi får ny data hver fredag kl. 04:00. 
Altså, veldig få updates, men veldig mye lesing. 

Hva kan vi gjøre for å spare ressurser generelt sett?

Kan du lage en løsning som gir oss billig lesing av de høyest rangerte filmene gitt at en spesifikk skuespiller er med i filmen?

#### SQL functions
Kan du lage en funksjon i DB som tar inn en tittel og returnerer skuespillerene som er mest kjent for den tittelen?


#### Filmkveld
En "filmkveld" består av 1 film, og 1 eller flere personer. 
Filmkvelden har støtte for at personer legger til en kommentar på filmen, og en rating. Dette er knyttet til filmkvelden, slik at det er mulig å rate samme film flere ganger på forskjellige filmkvelder. 
Når noen rater filmen lavere enn 4 så legges filmen **automatisk** til i en ny tabell(bad_movies) sammen med kommentaren den fikk. 


## Tips til Go

Husk på: 
- Trygg forbindelse mellom server og DB.
- Håndtering av nullverdier
- Feilhåndtering
- Fornuftig logg
- Gode structs

