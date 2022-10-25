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
# H
# X
