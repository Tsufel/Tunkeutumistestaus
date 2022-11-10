# A
# B
# C
# D

# X
## OWASP 10 2017
### A1 Injection
Haavoittuvuuksia löytyy yleensä SQL,LDAP,XPath,NoSQL ja XML ja muutamasta muusta.
Injektiot tehdään syöttämällä modifioitu parametri, minkä johdosta hyökkääjälle palautuu ylimääräistä dataa, tai hyökkääjä voi poistaa tauluja tai muokata niitä.
Esim. SQL tapauksessa hyökkääjä tulee SQL kyselyn ulkopuolelle käyttämällä ```'``` ja tekemällä toisen kyselyn perään tai käyttämällä ```' OR '1'=1´```.

### A2 Broken Authentication
Käytetään brute force tekniikkaa syöttämällä listoja tunnetuista salasanoista, jotta päästäisiin sisälle
Sovelluksen istuntoa ei keskeytetä tietyn ajan jälkeen ja sitä käytetään hyväksi, esim. julkisella koneella.
### A3 Sensitive Data Exposure
Salaista dataa ei ole salattu vaan se välitetään selkokielisenä. Hyökkääjä joka toimii väliäkätenä voi ottaa kaiken tiedon ylös.
Tietokanta käyttää automaattista salauksen purkua jolloin SQL injectiolla saa salatun datan selkokielisenä.
### A7 Cross Site Scripting
Hyökkääjä kaappaa käyttäjän cookien lähettämällä sen omalle sivulleen. Tämä on mahdollista, jos käyttäjän selaimessa on scripti, jonka voi saada esim painamalla, jotain linkkiä.
##

# Y
