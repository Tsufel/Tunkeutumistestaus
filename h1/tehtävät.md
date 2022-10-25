# A Webgoat 8 asennus
Aloitetaan tärkeimmällä eli palomuurin aktivoimisella

```$ sudo ufw enable ```

![image](https://user-images.githubusercontent.com/71498717/197750450-2492456e-3c56-4e98-953b-5b7076f09968.png)

Sitten asennetaan webgoat 8 ja käynnistetään se

```
$ wget https://terokarvinen.com/2020/install-webgoat-web-pentest-practice-target/webgoat-server-8.0.0.M26.jar

$ java -jar webgoat-server-8.0.0.M26.jar
```

![image](https://user-images.githubusercontent.com/71498717/197750806-37a6575c-16a1-4c46-811b-b5dbe38ffb2a.png)


Avataan web selain ja menään osoitteeseen http://localhost:8080/WebGoat/registration ja tehdän rekisteröityminen.

Sitten ollaankin sisällä.

![image](https://user-images.githubusercontent.com/71498717/197752315-87a0dac9-081c-4113-8b7c-5e8e3d5f242e.png)




# B

## HTTP Basics

![image](https://user-images.githubusercontent.com/71498717/197757182-1f346a29-1c55-4bf5-a439-fc379de9e4f7.png)

Ensimmäisen vastaus on POST ja se saadaan tietää kun avataan kehittäjän työkalut painamalla F12 ja tutkimalla network välilehteä

![image](https://user-images.githubusercontent.com/71498717/197757535-3c420d48-56a6-4aaf-97bb-369242e7d60a.png)

Hetki aikaa meni löytää takanumero, mutta kun kerran arvasi väärällä numerolla niin POST komennon pyyntö datasta löytyi taikanumero 86 joka oli oikein

![image](https://user-images.githubusercontent.com/71498717/197759650-0c8fb8ca-daf6-4b93-a3e6-aeca37c22fad.png)

## Developer Tools

Ensimmäisessä kohdassa piti käyttää komentoa 

```webgoat.customjs.phoneHome()```

josta palautui numero -1883361499

![image](https://user-images.githubusercontent.com/71498717/197760891-7f4c9625-900c-47f0-891b-9991b527430d.png)

Toisessa tehtävässä go nappulan painaminen triggeröi POST pyynnön josta löytyy networkNum "35.10974554023596"

![image](https://user-images.githubusercontent.com/71498717/197762713-491cf478-cb70-4bf2-8233-072d73b2a364.png)



# C  bandit 0 - 2
## Taso 0
Otetaan yhteys ssh yhteydellä banditin 2220 porttiin
ssh bandit0@bandit.labs.overthewire.org -p 2220

![image](https://user-images.githubusercontent.com/71498717/197730780-dbd3c272-90a6-4341-8245-371c70c34546.png)

## Taso 1
Tutkitaan mitä tiedostoja kohteesta löytyy ls komennolla. Ja luetaan löydetty "readme" komennolla cat readme. Sieltä löytyi salasana bandit1 tasoon.

![image](https://user-images.githubusercontent.com/71498717/197732208-a59a72bb-a639-4b3d-b139-fe9eb7fe13db.png)

## Taso 2
Otetaan yhteys komennolla
ssh bandit1@bandit.labs.overthewire.org -p 2220

Sieltä löytyi ls komennolla tiedosto "-" joka avattiin komennolla cat ./-

![image](https://user-images.githubusercontent.com/71498717/197733388-f5b12b57-f5c4-4de0-b150-24ad7545aef1.png)

## Taso 3
Otetaan yhteys komennolla
ssh bandit2@bandit.labs.overthewire.org -p 2220

ls komennolla löytyi tiedosto nimeltä "spaces in this filename". Komennolla cat spaces\ in\ this\ filename se aukesi.

![image](https://user-images.githubusercontent.com/71498717/197735018-b60d73a8-be7c-46e2-a2fc-117d9a61e673.png)

## Taso 4
Otetaan yhteys komennolla
ssh bandit3@bandit.labs.overthewire.org -p 2220

Navigoitiin kansion sisään cd inhere komennolla. ls komento näytti tyhjää, niin sitten käytettiin ls -a komentoa, joka näyttää myös piilotetut tiedostot. Komennolla cat./.hidden avattiin uusi salasana

![image](https://user-images.githubusercontent.com/71498717/197735555-596ab630-3d29-4e2e-98b7-d8691e10956c.png)

## Taso 5
Otetaan yhteys komennolla
ssh bandit4@bandit.labs.overthewire.org -p 2220

Navigoitiin kansion sisään cd inhere komennolla. ls komento näytti tyhjää, niin sitten käytettiin ls -a komentoa, joka näyttää myös piilotetut tiedostot. Komennolla cat ./-file00 ei löytynyt mitään joten käydään jokainen tiedosto läpi.

![image](https://user-images.githubusercontent.com/71498717/197736734-f8f7f7a5-aef4-4bf6-897f-e3e0e3595106.png)

## Taso 6
~~Otetaan yhteys komennolla
ssh bandit5cd @bandit.labs.overthewire.org -p 2220~~

~~Navigoitiin kansion sisään cd inhere komennolla. Tiedossa oli, että tiedoston koko on 1033 bytes joten etsittiin sillä koolla komennolla find . -size 1033c. Oikean kokoinen tiedosto löytyi joten navigoitiin sinne komennoilla cd maybehere07 ja cat ./-file2~~




# D 
![image](https://user-images.githubusercontent.com/71498717/197729766-da399083-3d41-4860-872d-d21a85eebeba.png)
Kali linux oli jo asenettuna aikaisemmin niin en käy sen asennusta tarkemmin lävitse
# F
# G

## HTTP Proxies
Asetetaan selain toimimaan proxyn kautta avaamalla OWASP ZAP ja painamalla näppärää nappulaa, joka avaa selaimen

![image](https://user-images.githubusercontent.com/71498717/197764891-46fe9a17-b899-4d8e-8c70-51a156ea680f.png)
![image](https://user-images.githubusercontent.com/71498717/197765003-0121f262-4876-40f5-927d-2a8ed9d345a8.png)

varmistetaan ZAP:in puolelta, että proxy toimii

![image](https://user-images.githubusercontent.com/71498717/197765521-058cf685-d178-4f34-9bae-50d36c888a60.png)

Filteröidään ZAP:in puolella proxyn kautta kulkevan liikenteen historiaa komennoilla 

``` https://localhost:8080/WebGoat/.* ```
ja
```.*/WebGoat/service/.*mvc ````

![image](https://user-images.githubusercontent.com/71498717/197766272-82954620-f022-4092-a43e-12e0222ffa8d.png)

Seuraavaksi siepataan webgoatin lähettämä pyyntö ja muokataan sitä

![image](https://user-images.githubusercontent.com/71498717/197767746-52e4d47c-dfe3-4143-afcd-2ce2888fe179.png)

Pyyntö saatiin siepattua ja muokattua. Vihreällä korostetut kohdat ovat muokattu vastaamaan tehtävän vaatimuksia

![image](https://user-images.githubusercontent.com/71498717/197770959-5bccf313-946f-4543-8f9f-216a3fb54a37.png)


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


# H
# X
