# A Metasploitable asennus ja oma verkko
Asennetaan Metasploitable2 virtuaalikone ja eristetään se omaan verkkoonsa Kalin kanssa
Virtualboxissa avataan Host Network Manager ja luodaan toinen ethernet adapteri.

![image](https://user-images.githubusercontent.com/71498717/199259815-7c68bb02-58f4-4a86-912d-2ddb964e1ca2.png)

Sen jälkeen molempien virtuaalikoneiden asetuksista lisätään Host-only Adapter, jolloin molemmat koneet on samassa verkossa ja ne eivät ole yhteydessä ulkomaailmaan.

![image](https://user-images.githubusercontent.com/71498717/199260874-2716b749-a04e-4ac9-9162-13284d644983.png)

Pitää myös muistaa irroittaa kaapeli Kalista, ettei vahingossakaan käy hassusti

![image](https://user-images.githubusercontent.com/71498717/199261519-58c753c5-d7bc-4786-8f52-77e233795428.png)


# B Porttiskannaus

Etsitään ensin metasploitable koneen ip-osoite.
```ifconfig```

![image](https://user-images.githubusercontent.com/71498717/199263409-19ea2c66-b23e-43a3-9f12-b38f43777583.png)

Sieltä löytyi ip mitä tutkitaan tarkemmin.

Avataan metasploit

```sudo msfdb run```

ja tarkistetaan tietokannan status

```db_status```

![image](https://user-images.githubusercontent.com/71498717/199265624-f43c469a-a72e-44b0-83a5-cf4c5688d683.png)

Luodaan metasploitable työtila
```workspace -a metasploitable```

Tehdään nmap tiedettyyn ip-osoitteeseen

```db_nmap -p- -A 192.168.252.3 -oA ports```

```-p-``` Tällä käydään kaikki avoimet portit läpi. ```-A``` vivulla saadaan käyttöjärjestelmä ja version tunnistus. ```-oA ports``` Tällä tallennetan saatu tulos "ports" tiedostoon.

Avoimia portteja oli runsaasti ja silmään pisti tunnillakäydyn perusteella portti 21 ja ftp service sekä portin 1524 bindshell

![image](https://user-images.githubusercontent.com/71498717/199274157-33822ca8-fe1d-4900-b0fb-0a4592b27edf.png)


 
# C Murtaudu Metasploitableen
Aloitetaan murtautuminen tutkimalla löytyykö metasploitista valmiiksi joku scripti.
``` search vsftpd```

![image](https://user-images.githubusercontent.com/71498717/199290877-c58b785f-2d03-46ac-9f7c-143a1f366f02.png)

Sieltä löytyi käutettävä moduuli jota käytetään komennolla  ```use 0```
asetetaan maali ip moduulille ```setg rhosts 192.168.252.3```

![image](https://user-images.githubusercontent.com/71498717/199291767-0a81fae9-8418-4d2c-bb89-8df95514531c.png)

ajetaan moduuli ```run``` komennolla ja ```ls``` komennolla voidaan tarkistaa ollaanko sisälle päästy.

![image](https://user-images.githubusercontent.com/71498717/199292992-553f2e1f-74ef-4d5f-8c4f-a8bfd6fdff5d.png)

 
# D  Murtaudu Metasploitableen jollain toisella tavalla (eri kuin edellisessä kohdassa)
Kuten aikaisemmin huomattiin oli portissa 1524 metasploitable rootshell. Avoimeen rootshelliin voidaan yhdistää netcat komennolla ```nc 192.168.252.3 1524``` jossa viimeisin luku kertoo portin numeron.

![image](https://user-images.githubusercontent.com/71498717/199309919-18f59d30-841a-4ee5-85ef-612af4fdd5e2.png)

Tämä oli yllättävän helppo tapa päästä sisälle koneeseen. Ei vaatinut kuin avoimen root shellin. Toivottavasti omalla koneella ei ole tällaista takaporttia.



# E Vulnhub
Asennettiin Vulnhubista Empire:Breakout https://www.vulnhub.com/entry/empire-breakout,751/
Asetettiin samaan host only verkkoon kuin muutkin koneet. Ja liikkeelle lähdettiin tekemällä nmpa jotta koneen ip löytyisi
```sudo nmap 192.168.252.0-255```

Osoitteesta 192.168.252.5 kone näyttikin löytyvän. Sen jälkeen avattiin metasploit ```sudo msfdb run``` ja avattiin oma työtila ```workspace -a breakout``` Sen jälkeen tutkitaan tarkemmin jo löydettyä osoitetta sekä tallennetaan tiedot tiedostoon
```db_nmap -p- -A 192.168.252.5 -oA breakoutports```

Sieltä löytyi pari avointa porttia

![image](https://user-images.githubusercontent.com/71498717/199445037-41f3634b-5f6d-485a-9cd4-7ad4ce746898.png)

Ensimmäisenä silmään pisti Samba smbd 4.6.2 portissa 139. Hieman googlailtuani löytyi netistä sambacry niminen haavoittuvuus ja metasploitista löytyi siihen sopiva moduuli nimeltä ```exploit/linux/samba/is_known_pipename```. Sen jälkeen ```use exploit/linux/samba/is_known_pipename``` ja asetetaan kohde ```set rhosts 192.252.168.5```

Jostain syystä tämä ei toiminut vaan palautti virhettä. (Syynä saattaa olla väärän ip-osoitteen käyttö........)

![image](https://user-images.githubusercontent.com/71498717/199456862-f3a288a6-6199-4b91-a7e1-998797e07c0d.png)

No ei se toiminut oikeallakaan osoitteella
![image](https://user-images.githubusercontent.com/71498717/199470334-5afa0d03-9102-4158-8311-54c3884939e8.png)


Hetken (pitkän ajan) päätä hakattuani seinään tyydyin katsomaan walktroughta, jotta saisi jonkun vinkin miten edetä. ```https://resources.infosecinstitute.com/topic/empire-breakout-vulnhub-ctf-walkthrough/```
Täältä löytyi vinkki mennä apache nettisivulle joka löytyy koneen ip-osoitteella. Ja jostain syystä tämä ei toimi koneellani, joten en tiedä mistä kiikastaa. Kohde kuitenkin vastaa pingeihin joten ei se ole kokonaan irti verkosta. Ja silmät kun otti käteen niin huomasin, että osoite oli väärä.


Uudella yrityksellä, kun laittoi selaimeen ```view-source:http://192.168.252.5/``` ja selasi koodia tarpeeksi alas tuli vastaan erikoinen pätkä merkkejä.

![image](https://user-images.githubusercontent.com/71498717/199470646-db491dcb-5764-405e-ba8b-fbac3288c242.png)

Ilmeisesti tekstipätkä on branfuck koodia ja sen kun käänsi tuli vastaukseksi salasana ```.2uqPEfj3D<P'a-3```

Nyt kun päästiin selailun makuun käydän muutkin portit läpi ja aloitetaan  ```https://192.168.252.5:20000/```. Sieltä tuli login sivu vastaan ja salasana taidetaankin tietää, mutta käyttäjä vielä uupuu.

![image](https://user-images.githubusercontent.com/71498717/199472991-6f7746e3-f7f7-4b95-92a0-9a4d61f1a612.png)


```db_nmap``` antoi käyttäjänimeksi unknown
![image](https://user-images.githubusercontent.com/71498717/199476188-65f9ec4a-1c5a-4282-b659-0484b67a73cb.png)

Joten jälleen hieman päätä hakattuani seinään päätin katsoa hieman vinkkejä. Vinkiksi annettiin enum4linux. Komennolla ```enum4linux 192.168.252.5``` 

Sieltä löytyi käyttäjä cyber, joka toivottavasti toimii myös kirjautumiseen selaimessa.

![image](https://user-images.githubusercontent.com/71498717/199476972-4c4da8cc-e675-49aa-8958-15b86ce36c67.png)

Tällä käyttäjällä päästiin sisään webmin kirjautumisesta

![image](https://user-images.githubusercontent.com/71498717/199477800-f1fea799-0f61-4d99-8d81-25b2a294a6a5.png)


Sivulla päästiin käsiksi shelliin

![image](https://user-images.githubusercontent.com/71498717/199480274-0e35c9c6-60df-42ac-8df9-10b6e7220e8c.png)

Shellissä käytettiin komentoja ```ls``` ja ```cat.user.txt``` sieltä löytyi ensimmäinen lippu.

![image](https://user-images.githubusercontent.com/71498717/199480579-49e331ab-5dfb-481a-a4ba-8c179dc4c405.png)



# H Metasploitable3 
Aloitettiin Metasploitable 3 asennuksella ja käynnistyksellä. Käynnistettiin Kalissa metasploit  ```sudo msfdb run``` ja sen jälkeen tehtiin omatyötila ```workspace -a meta``` ja käynnistettiin nmap kohde ip-osoitteeseen ```db_nmap -p- -A 192.168.252.7 -oA meta3``` ja tallennettiin saatu tieto myös tiedostoon meta3.

![image](https://user-images.githubusercontent.com/71498717/199994528-2bb8a82a-4ba0-4d92-975a-6fcea9e146ee.png)


Portissa 80 näytti olevan jotain tiedostoja, niin käydään niitä ensin tutkimassa. Avataan selain ja mennään osoitteeseen ```http://192.168.252.7/payroll_app.php```. Sieltä löytyi kirjautumissivu Payroll appiin. Toimisikohan täällä SQL-injektio? 
Kokeillaan käyttäjänimeksi ```letmein``` ja salasanaksi ```letmein' OR '1'='1```

![image](https://user-images.githubusercontent.com/71498717/199995981-45cc43e9-22e2-4e4d-bb1a-75b7b784ef92.png)

Ja sillähän päästiin sisään. Olisikohan näistä tunnuksista jotain hyötyä koneen aukaisemisessa.

![image](https://user-images.githubusercontent.com/71498717/199996180-51a59fd6-40e1-451e-ae46-11ec8b31742c.png)

Kun meillä kerran on mahdollisia käyttäjänimiä, niin kokeillaan käyttää portin 3306 MySQL exploittia. Haetaan sql exploitteja ```search mysql```  Sieltä löytyy haluamamme moduuli nimeltä mysql_login.
![image](https://user-images.githubusercontent.com/71498717/200000051-a418f6a3-df93-4ad0-bb7f-af603899f882.png)

Sen jälkeen ```use 10``` komennolla valitaan moduuli. Ja ```options``` jotta nähdään tarvittavat tiedot.

![image](https://user-images.githubusercontent.com/71498717/200000415-ceb0ec40-84e8-4c68-ab29-9a81b99c6c7c.png)

Sitten luodaan käyttäjänimi lista jo löydetyistä käyttäjistä, sekä geneerisistä nimistä. ```nano sqlusernames.txt```
![image](https://user-images.githubusercontent.com/71498717/200002251-b0e6a5d4-41f0-47f7-9d1c-43377460f2fe.png)

Ja sama salasanoista ```nano sqlpwd.txt```. Käyttäjien ollessa starwars painotteisia laitetaan salasanoihin mukaan starwars aiheisia salasanoja.  ![image](https://user-images.githubusercontent.com/71498717/200003883-1d1d6a09-e610-43e1-a63e-3d8a70d5690c.png)

Sen jälkeen asetetaan luodut tiedoston moduuliin. ```set user_file mysqlusernames.txt``` ```set pass_file sqlpwd.txt```

![image](https://user-images.githubusercontent.com/71498717/200004641-f49a8cde-cd29-46de-ab52-b8352a652c6c.png)

Asetetaan kohde koneen ip ```set rhosts 192.168.252.7```  ![image](https://user-images.githubusercontent.com/71498717/200005165-1b980006-9b58-4a80-bcfe-c5f18ba769e8.png)

Sitten laitetaan vielä ```set user_as_pass true``` jotta ohjelma kokeilee käyttäjänimeä salasanana. ![image](https://user-images.githubusercontent.com/71498717/200005587-997b3e6f-68ff-4df9-8e35-4e3a6f24f81d.png)

Ja jos olisi ollut fiksu olisi tarkistanut etukäteen onko kohdekoneen mysql versio tuettu tai voiko siihen yhditää etänä...

![image](https://user-images.githubusercontent.com/71498717/200013371-1c1a4001-584e-4e72-82a2-530e7dbc1949.png)

Hiukan aikaa pähkäiltyäni ja googleteltuani päätin kokeilla ```use auxiliary(scanner/mysql/mysql_version)``` moduulia. Se palautti, että oman koneen osoitteesta ei voi yhditää tälle mysql serverille.
![image](https://user-images.githubusercontent.com/71498717/200015881-cf817f81-c64f-4965-a2d5-c4f77c387fc3.png)

Täytynee siis päästä käsiksi shelliin ja saada meterpreter yhteys ja sitten kokeilla uudestaa.





# I
# J

# X Yhteenveto artikkelista
€ Jaswal 2020: Mastering Metasploit - 4ed: Chapter 1: Approaching a Penetration Test Using Metasploit

```-Aina jos mahdollista yritä saada meterpreter käyntiin```
```-Piilota prosessi jonkin toisen prosessin sisään, mitä ei todennäköisesti suljeta```
```-Metasploitissa on paljon moduuleja, jotka olisi hyvä tietää jo valmiiksi käytön helpottamiseksi```
