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
# H

# Z
##
##

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
