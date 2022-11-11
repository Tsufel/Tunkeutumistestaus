# A
## 0 SELECT basics
Tehtävät olivat suhteellisen suoraviivaisia joten en niihin sen tarkemmin paneudu. Vaikka ovatkin kyllä hyvää kertausta
![image](https://user-images.githubusercontent.com/71498717/201321779-d361bcb7-1681-4225-9534-0db67eb2de6c.png)  
![image](https://user-images.githubusercontent.com/71498717/201321977-bbc77216-16c7-498e-9145-f0ef26cd9b0c.png)  
![image](https://user-images.githubusercontent.com/71498717/201322083-199b921c-741c-42bc-a41d-fa422452c0c1.png)  

## 2 SELECT from World kohdat 1-5
Valittiin taulusta tietyt kohdat mitkä haluttiin näyttää eli ei kaikkia  
![image](https://user-images.githubusercontent.com/71498717/201322619-5466360e-92b6-46e0-bb8c-a50d3476f7e7.png)  
Seuraavassa filteröitiin koon perusteella näkymään ne maat joissa on yli 200 miljoonaa asukasta  
![image](https://user-images.githubusercontent.com/71498717/201323066-7059d820-2575-4d95-9fc3-4c59dea28960.png)  
Seuraavassa piti jakaa gdp väkiluvulla ja palauttaa se tieto  
![image](https://user-images.githubusercontent.com/71498717/201323801-89ff2fbe-fbff-4c10-aa67-fc6b35aaa662.png)  
Seuraavassa jaettiin väkiluku miljoonalla, jotta saatiin väkiluku näkymään miljoonissa  
![image](https://user-images.githubusercontent.com/71498717/201324459-b9ffb40b-17f0-483a-b980-74310dfd1180.png)  
Viimeisessä valittiin tietyt maat nimen perusteella  
![image](https://user-images.githubusercontent.com/71498717/201325663-5fc87e67-d7f6-4ae7-a002-2f276187e803.png)  



# B Tee paikallinen tunnus DVWA Damn Vulnerable Web App -ohjelmaan
Ensin piti etisä metasploitable2 weppipalvelin joka löytyi, kun navigoi selaimella suotaan metasploitablen IP-osoitteeseen joka minulta löytyy ```192.168.252.3```  
![image](https://user-images.githubusercontent.com/71498717/201331029-f58f14e4-f967-4758-b035-d50dd1e3167a.png)  

Kirjaudutaan sisään annetuilla tunnuksilla  
![image](https://user-images.githubusercontent.com/71498717/201332391-228925e3-7aaf-450f-bde1-b49cec77c7a3.png)  

Ja käydään vaihtamassa DVWA Security low tasolle, jotta tehtävät olisi helpompia  

![image](https://user-images.githubusercontent.com/71498717/201332554-2ee707a5-c639-4e0b-900b-34ae334c8b5c.png)  


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
