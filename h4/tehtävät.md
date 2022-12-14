Kaikki nmapit on tehty oman koneen ja metasploitable2 välillä

# A nmap TCP connect scan -sT
```nmap -sT 192.168.252.3```  
![image](https://user-images.githubusercontent.com/71498717/202702426-999860a8-db1d-477f-9a04-e55046978b1f.png)  
Elikkä nmap skannasi kohdekoneen portteja TCP connectilla ja muodosti yhteyden kohdekoneeseen.  
![image](https://user-images.githubusercontent.com/71498717/202711233-d1514112-e0bd-4d7c-a608-d2aa3737b560.png)  
Puolessa sekunnissa tuli 2,5t pakettia joten en niitä kaikkia käy läpi tässä. Otetaan tarkempaan tarkasteluun yksi portti, niin saadaan ehkä jokin selko tapahtumien kulusta. Filtteriksi laitetaan ```tcp.port == 80``` eli tutkitaan vain kohdekoneen portin 80 tapahtumat. Ja kuten nmapin tuloksista voimme päätellä oli portti 80 auki.  
![image](https://user-images.githubusercontent.com/71498717/202712047-0efbe799-2a87-4d31-bf22-b57035d44415.png)  
Tuloksia tarkastellessa huomataan, että nmap teki 3 vaiheisen kättelyn ja yhteyden lopettamisen kahdesti, mutta eri porteista. Eli ensin lähetettiin ```[SYN]``` (synkronoi). Kohdekone vastasi ```[SYN][ACK]``` synkronointi ja vahvistetaan paketin saapuminen. Oma kone vahvistaa paketin saapumisen ja sen jälkeen lähettää ```[RST][ACK]``` eli RESET, mikä tarkoitaa yhteyden sulkemista ilman kuittauksia yhteyden loppumisesta. Ja jostain syystä tuo sama tehdään toisesta portista.

# B nmap TCP SYN "used to be stealth" scan, -sS
```sudo nmap -sS 192.168.252.3```
Tässä tehdään skannaus vain lähetämällä [SYN] paketti eikä mitään muuta. Ja vastauksista päätellään onko portit auki vai ei. Ja tässä skannissa vaadittiin root oikeuksia.  
![image](https://user-images.githubusercontent.com/71498717/202718795-0d664acf-5822-435b-b7db-b4854851e54f.png)  
Tässäkin skannauksessa tuli pari tuhatta pakettia alle sekunttin, joten en käy kaikkia läpi. Otan portin 80 jälleen tarkempaan syyniin. ```tcp.port == 80```  
![image](https://user-images.githubusercontent.com/71498717/202719216-4f3ab3e5-e2ec-4f59-80ef-9849aa25103f.png)  
Portti 80 oli auki nmapin tuloksista päätellen. Lähdetään liikkeelle oman koneen lähettämällä [SYN] paketilla. Kohdekone vastaa [SYN][ACK] ja siihen oma kone vastaa lähettämällä yhteyden katkaisun [RST]. SYN skannia pidetään huomaamattomanpana, koska kolmivaiheista kättelyä ei tehdä kokonaan loppuun vaan vain kaksi vaihetta tehdään, jonka jälkeen yhteys katkaistaan. Tarkastellaan seuraavaksi suljettua porttia 6669 ```sudo nmap -sS 192.168.252.3 -p 6669``` ja filtteriin ```tcp.port == 6669```  
![image](https://user-images.githubusercontent.com/71498717/202721951-8c29e6c9-788d-479a-934b-b1e28739976a.png)  
![image](https://user-images.githubusercontent.com/71498717/202721989-159519d1-27fc-4569-941a-0e5cf8b4598a.png)  
Kuten huomataan omasta koneesta lähti [SYN] paketti kohdekoneeseen ja sieltä vastattiin yhteyden sulku paketilla [RST][ACK]. Tästä nmap tietää portin olevan suljettu, koska se vastasi eli paketti meni perille, mutta [RST] paketista johtuen se ei voi olla avoin portti.

# C nmap ping sweep -sn
```sudo nmap -sn 192.168.252.3```  
```-sn``` lippu estää nmappia tekemästä porttiskannausta löydetyille laitteelle.  
![image](https://user-images.githubusercontent.com/71498717/202725280-5fecd009-b0b6-46e0-9401-977e760ee49d.png)  
![image](https://user-images.githubusercontent.com/71498717/202725400-924e5285-f521-4e79-9672-6aba3c777cf3.png)
Ensin lähetetään broadcast pakettina kysely,että kuka on tässä ip-osoitteessa. Kohteessa oleva kone vastaa kuka siitä osoitteesta löytyy ja millä mac osoitteella.

# D nmap don't ping -Pn
```sudo nmap -Pn 192.168.252.0-255```
```-Pn``` lipulla host discovery pitäisi olla pois päältä eli nmap ei erittele päällä olevia koneita kiinni olevista. Nmap skannaa kaikki määritelyt ip-osoitteet huolimatta siitä onko vastaanottaja tavoitettavissa vai ei. Paikallisessa verkossa nmap suorittaa silti arp skannauksen, koska se tarvitsee mac osoitteet tarkempaan skannaukseen. 
![image](https://user-images.githubusercontent.com/71498717/202731152-296136c8-2ca6-44f2-8004-1c8511cfeb4e.png)  
![image](https://user-images.githubusercontent.com/71498717/202731227-788e3b00-9ed5-42cf-a4b4-5a66ec7a297d.png)  
Kuten tästä huomaamme arp skannaus on silti suoritettu. Tämän voi estää ```--disable-arp-ping``` lipulla.  
```sudo nmap -Pn --disable-arp-ping 192.168.252.0-255 ```  
![image](https://user-images.githubusercontent.com/71498717/202732925-855f8c11-62c1-4bdd-ada5-650f0762f082.png)  
![image](https://user-images.githubusercontent.com/71498717/202732972-6ec1e1b9-a842-4545-9011-a1c113e278d0.png)  
Jostain syystä silti lähtee arp kyselyitä kokoajan kaikkiin osoitteisiin, jotka eivät vastaa. Siinä välissä sitten yritetään tehdä skannauksia portteihin osoitteissa jotka on päällä. Tuloksena voimme todeta, että tämä on todella hidas tapa skannata ja ei välttämättä kaikista fiksuin.

# E  nmap version detection -sV
```sudo nmap -sV -p 80 192.168.252.3```  
Tehdään versio skannaus porttiin 80.  
![image](https://user-images.githubusercontent.com/71498717/202736770-ca7ef537-650d-4bd4-b1f6-826d9853276c.png)  
![image](https://user-images.githubusercontent.com/71498717/202737969-40f25cc2-2f7f-4c5f-836c-f9c8c157b8f2.png)  
Ensin tehdään keskeneräinen kättely ja lopetetaan yhetys, jotta portin avoimuus voidaan todeta. Sen jälkeen luodaan yhteys ja tehdään get pyyntö.
![image](https://user-images.githubusercontent.com/71498717/202738592-f9d66208-cb29-46e9-898a-3b1fada83771.png)  
![image](https://user-images.githubusercontent.com/71498717/202738764-9f62f964-48d6-4fa4-89e5-3d3d6e53673b.png)  
Vastauksessa saatiin jo tietää serveri ja sen versio. Huomionarvoista on että kun käytössä on http protokolla tulee vastaus selkokielisenä ja kuka vain voi kaapata tuon sivun tiedot välistä (periaatteessa).

# F nmap porttien valinta -p1-100, --top-ports 5, -p-
```sudo nmap -p1-100 192.168.252.3```  
Tällä skannataan kaikki kohdekoneen portit välillä 1-100  
![image](https://user-images.githubusercontent.com/71498717/202744312-1a878562-c2ad-4070-8225-c52a5d17518a.png)  
![image](https://user-images.githubusercontent.com/71498717/202744355-0071f3d8-a9bc-460e-bdbd-97d21ce175b5.png)  
Suljetut portit vastaavat [RST][ACK] ja mikäli portti on auki ei yhteyttä muodosteta vaan se suljetaan ennen sulkemista.


```sudo nmap --top-ports 5 192.168.252.3```
```--top-ports [x]``` skannaa yleisimmät avoimet portit, jossa x kuvaa haluttavien porttien määrää.  
![image](https://user-images.githubusercontent.com/71498717/202745463-bd1adc62-9432-4a8c-8155-94dbcd60fded.png)  
![image](https://user-images.githubusercontent.com/71498717/202745526-fc3d0efa-4755-4ca3-bd4a-cabf3f77bcef.png)  
![image](https://user-images.githubusercontent.com/71498717/202745905-c57739c2-25b9-4239-917f-565f81698bcd.png)  
Portti 443 on yleensä avoin,mutta kohdekoneessa se on kiinni ja se silti näkyy listassa minkä nmap tulostaa.


```sudo nmap -p- 192.168.252.3```
```-p-``` tarkoittaa että skannataan kaikki mahdolliset portit (65,535).  
![image](https://user-images.githubusercontent.com/71498717/202747229-3d07106b-3bd0-4319-b09a-89e531b2eb40.png)  
![image](https://user-images.githubusercontent.com/71498717/202748704-c8c215e8-874d-4d8f-a5d1-3b889b8f7e01.png)  
Paketteja jälleen kertyi 130t jotan ei käydä niitä liian tarkasti läpi, mutta kaikkiin mahdollisiin portteihin lähetettiin [SYN] paketti. Suljetuista porteista vastattiin [RST][ACK] paketilla.

# G nmap ip-osoitteiden valinta; luettelo, verkkomaskilla 10.10.10.0/24, alku- ja loppuosoitteella 10.10.10.100-130
```sudo nmap 192.168.252.0/24``` Komennolla nmap käy läpi kaikki 256 verkkomaskin takana olevaa osoittetta eli 192.168.252.0-255  
![image](https://user-images.githubusercontent.com/71498717/202774090-d0a17f96-3b69-4740-a99c-55dc548dfd95.png)  
Kätevä jos haluaa käydä läpi kaikki osoitteet tuolta väliltä.

```sudo nmap 192.168.252.100-130``` Komennolla käydään läpi kaikki 100-130 välillä olevat ip-osoitteet. Nitä on 31 kappaletta.  
![image](https://user-images.githubusercontent.com/71498717/202774557-62e3e99f-d1f1-4c5a-8664-33251820f87a.png)  
Kätevä jos tietää miltä väliltä haluaa tutkia osoitteita.

# H nmap output files -oA foo
```sudo nmap -oA foofoo 192.168.252.3```  
```-oA``` lippu tallentaa saadut tulokset kolmessa eri tiedostomuodossa.  
![image](https://user-images.githubusercontent.com/71498717/202775107-3d6f591f-66c1-4085-9c52-fc730905a803.png)
![image](https://user-images.githubusercontent.com/71498717/202775146-4e7aaebf-2099-4ae0-912e-bf70ee426dbb.png)  
```.gnmap```  
![image](https://user-images.githubusercontent.com/71498717/202775524-ecceb2d8-cdc4-4281-9a1e-984c17babc33.png)  
Tätä tiedostomuota voi käyttää hos haluaa käyttää ```grep``` työkalua. Tulokset ovat riveittäin.
```.nmap```  
![image](https://user-images.githubusercontent.com/71498717/202775576-3115d119-4cbf-49a6-af0d-f2e10f3194cd.png)  
Tämä muoto on helpoiten luettava, kun tiedostomuoto on sama mitä nmap tulostaa muutenkin.
```.xml```  
![image](https://user-images.githubusercontent.com/71498717/202775612-511b4d8b-f94f-4526-b567-b9a14ff65839.png)  
Jo shaluaa tehdä raportteja ja näyttää ne esim. nettisivulla tämä muoto on siihen kaikista luontevin.

# K nmap ajonaikaiset toiminnot (man nmap: runtime interaction): verbosity v/V, help ?, packet tracing p/P, status s
## verbosity v/V ajonaikana tai lippuna -v
![image](https://user-images.githubusercontent.com/71498717/202780006-b31ebf1e-ea2d-4bf1-854c-c90fe1dd6a8e.png)  
Verbosityä lisäämällä saadaan nmap tulostamaan lisää tietoa esim. skannauksen tilasta ja mitä porttia ollaan skannaamassa tällä hetkellä.
## packet tracing p/P
![image](https://user-images.githubusercontent.com/71498717/202780394-d731e52c-4a66-41cf-b630-6640fd4c0679.png)  
Tulostaa ajonaikana tietoa paketeista mitä lähetetään. Tästä käy ilmi minkä protokollan paketteja menee ja kuka lähettää kenelle. Myös pakettien koko ja tarkoitus selviää. [SYN][ACK][RST] eli käytännössä kaikki mitä myös wireshark tekee.

## status s
![image](https://user-images.githubusercontent.com/71498717/202781235-ce5749a7-dbc1-4e1d-9e5a-797d35d664e6.png)
Status antaa tietoja siitä kuinka paljon aikaa on kulunut ja kuinka paljon on jäljellä.

# I normaalisti 'sudo nmap'. Miten nmap toiminta eroaa, jos sitä ajaa ilman sudoa? Suorita ja analysoi esimerkki.
## nmap
![image](https://user-images.githubusercontent.com/71498717/202782770-310207c7-1887-4425-8209-9c069e1bdc29.png) 
![image](https://user-images.githubusercontent.com/71498717/202783368-1f5c0144-17cb-44a6-93b3-9d5db7cbca55.png)  

Ainoa ero minkä tässä äkkiseltään huomaa on,että kun käyttää sudoa tulostaa nmap myös MAC osoitteen. Molememmat nmapit lähettävät suunnilleen saman verran paketteja. Ja sudo nmap lähettää broadcastina kyselyn mikä kone löytyy halutusta osoitteesta ja saa vastauksena kohdekoneen MAC osoitteen.

## sudo nmap
![image](https://user-images.githubusercontent.com/71498717/202782807-daa94baa-1fbf-4b40-ad13-529899a9d9fe.png)  
![image](https://user-images.githubusercontent.com/71498717/202783270-b0232be7-bb23-49a3-92b6-24a874378155.png)

# M nmap, vertaile -A ja ilman kestoa koneeseen, jossa on useita portteja auki
```sudo nmap -A 192.168.252.3```  ```-A``` lippu tarkoittaa agressiivista skannausta, eli löytyy os detection, script scanning ja traceroute. Aikaa kului 184 sekunttia ja wireshgarkin mukaan paketteja liikkui lähes 5000 kappaletta.  
![image](https://user-images.githubusercontent.com/71498717/202785535-9f4238e9-6c81-483c-a8b3-72532d1bbab6.png)  
![image](https://user-images.githubusercontent.com/71498717/202785647-7c4c6cee-c11e-409d-a98b-814ccd7a4340.png)  
![image](https://user-images.githubusercontent.com/71498717/202785724-96f547ad-1302-47d2-9381-e687df1d6a49.png)  
Myös UDP protokollan paketteja lähetettiin joka varmasti hidasti skannauksen nopeutta.  

```sudo nmap 192.168.252.3```  
Aikaa kului alle sekuntti ja paketteja lähetettiin noin 2000 kpl. myöskään UDP paketteja ei lähetetty yhtäkään. Nmapin tulostama data on toki myös paljon rajatumpaa ilman ```-A``` lippua 
![image](https://user-images.githubusercontent.com/71498717/202786244-60c909cd-765f-4bfd-a638-d117accc3342.png)  
![image](https://user-images.githubusercontent.com/71498717/202786290-47a07687-3783-414e-b158-ee7963733efd.png)  

# N Ninjojen tapaan. Piiloutuuko nmap-skannaus hyvin palvelimelta?
Apache serveri pyörii portissa 80 joten rajataan skannausta siihen porttiin ja tehdään myös versioskannaus ```sudo nmap -sV -p 80 192.168.252.4```
Oli virhe painaa p kesken ajon joka aktivoi pakettien seurannan. Dataa tuli niin paljon että meni terminaali solmuun. Apachen vakiosivu palautuu salaamattomana ja sitä voi tarkastella esim. wiresharkilla.
![image](https://user-images.githubusercontent.com/71498717/202849551-32350e82-5721-44a7-bbb9-37737a03840b.png)  
![image](https://user-images.githubusercontent.com/71498717/202849679-8d0b7935-97b6-4afa-867e-4d1a06842e8e.png)  
Komennolla ```sudo cat /var/log/apache2/access.log``` voimme tarkastella apachen omia logeja. Täältä voimme huomata, että sinne jää merkintä nmapin käytöstä. Myöskin se herättää huomiota, että samasta osoitteesta on tullut useampi GET pyyntö pienen ajan sisällä.  
![image](https://user-images.githubusercontent.com/71498717/202849886-04aaefa8-71ce-4a43-bc99-baa46880098a.png)  
![image](https://user-images.githubusercontent.com/71498717/202849961-50f45819-781f-4dcd-b1e1-fd7caf0605c4.png)  
Kuusi GET pyyntöä alle sekunttiin on suhteellisen paljon.

# e Mitkä ovat tavallisimmat tai kiinnostavimmat palvelut, joita UDP-skannauksella voisi löytää?
Tavallisimmat UDP skannauksella löytyvät palvelut ovat Domain Name Service (DNS), Simple Network Management Protocol (SNMP) ja Dynamic Host Configuration Protocol (DHCP). Lähde https://www.geeksforgeeks.org/what-is-udp-scanning/

# f  Miksi UDP-skannaus on hankalaa ja epäluotettavaa? Miksi UDP-skannauksen kanssa kannattaa käyttää --reason flagia?
UDP skannauksessa ongelmaksi tulee se, että kohdeportit ei välttämättä vastaa mitään jolloin portin statukseksi tulee open|filtered . Myöskin koska vastausta ei tule saadusta paketista välttämättä pitää paketti lähettää muutamaan kertaan, jolloin voidan olla varmoja paketin saapumisesta perille. ICPM myös rajoittaa vakiona joissain linux versioissa vastauksien nopeutta esim. yhteen vastaukseen sekunnissa. ```--reason``` lippua kannattaa käyttää udp skannauksissa, koska sen avulla saat tiedon syystä minkä takia mikäkin portti on merkattu avoimeksi/suljetuksi. Helpottaa elämää kun tietää, että miksi portti on closed|filtered.
lähteet https://nmap.org/book/scan-methods-udp-scan.html https://geek-university.com/the-reason-flag/







# Z
## Santos et al: The Art of Hacking (Video Collection)
https://learning.oreilly.com/videos/the-art-of/9780135767849/9780135767849-SPTT_04_01/
- Tiedustelu on tärkeä osa hyökkäystä, jotta aika ei lopu kesken ja löytää oikeat keinot ajoissa
- Nmap on monipuolisin ja vakain ohjelma porttien skannaukseen
- ```-Pn``` on tärkeä lippu, koska muuten nmap saattaa luulla jonkin kohteen olevan pois päältä vaikka se olisikin päällä
- EyeWitness on hyvä työkalu jos täytyy käydä läpi useita nettisivuja
- 

## Lyon 2009: Nmap Network Scanning: Chapter 1. Getting Started with Nmap Suoraan hevosen suusta
https://nmap.org/book/nmap-overview-and-demos.html
- Turvallisuuteen liittyy kompromissi käytettävyyden suhteen
- Nmapin tulosteesta harjaantunut asiantuntija erottaa onko kohde hyvin suojautunut.

## Nmap reference guide
https://nmap.org/book/man.html
### Port Scanning Basics
#### Open
- portti on avoin ja odottaa yhteydenottoa
#### Closed
- kohde on päällä mutta portissa ei ole mitään kuuntelemassa
#### Filtered
- yleensä palomuurista johtuen nmapin paketit eivät tavoita kohdetta
### Port Scanning Techniques
#### -sS
- TCP SYN skannaus
- lähettää yhteys pyynnön SYN, mutta jos saa vastauksen SYN/ACK ei vastaa takaisin
- vaikeampi havaita kun yhteyksiä ei luoda
- nopea
#### -sT
- TCP Connect protokollan porttiskannaus
- luo TCP yhteyden
- hitaampi koska dataa täytyy lähettää enemmän
- jää logeihin merkintä yhteydestä
#### -sU
- UDP protokollan skannaus
- hidas johtuen joidenkin käyttöjärjestelmien rajoituksista kiinniolevien porttien viesteihin.
- pari yleistä porttia joten ei välttämättä tarvitse kaikkia skannata
