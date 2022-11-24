# X Lue ja tiivistä valitsemasi Bellingcatin OSINT-opas
https://www.bellingcat.com/resources/how-tos/2018/07/09/digitally-verify-middle-east-conflicts/
- Tunnistaminen koostuu monesta vihjeestä, jotka saattavat olla ristiriidassa
- Maastopuvun kuviointi on yksi iso vihje
- Käytetyt hihamerkit ovat kanssa hyvä huomioida
- Kuvassa näkyvät aseet saattavat myös kertoa paljon, mutta lähi-idän ollessa kyseessä aseista ei voi vetä varmoja johtopäätöksiä yksinään
# Y SuomiOsint. Monet OSINT-oppaat keskittyvät isoihin maihin. Kuvaile jokin OSINT-lähde, joka sopii erityisesti Suomeen.
- Verotiedot verovirastosta
- finder.fi tietoja yrityksen päättäjistä ja liiketoiminnasta
- Välillisesti suomeen hitta.se mikäli henkilö asuu ruotsissa tai on asunut
# A Zap. Asenna OWASP ZAP. Surffaile wepissä ja näytä, että liikenne näkyy proxyssa.
Kalissa ZAP oli asennettuna valmiiksi joten ei käydä asennusta niin tarkasti läpi, mutta osoitteesta https://www.zaproxy.org/download/ valitsee cross platform packegen niin sen pitäisi toimia mainiosti. ZAP:in ollessa auki oikeasta yläkulmasta kun avaa selaimen saa proxyn näppärästi väliin.  
![image](https://user-images.githubusercontent.com/71498717/203804786-b4e7c7ae-7e9d-4d51-9f09-458756ba85b9.png)  
Yhdistetään selaimella metasploitablen osoitteeseen ja avataan phpmyadmin sivu osoitteesta 192.168.252.3/phpMyAdmin/  
![image](https://user-images.githubusercontent.com/71498717/203805679-d820effd-68f1-4187-9494-28873d9e5970.png)  
Hyvin näyttäsi proxy toimivan liikenteen kaappaamisessa.


# B ZAP certificate. Sieppaa ZAP:lla TLS-salattua liikennettä.
Hetki meni säätää apache serveriin https salaus päälle, mutta wiresharkilla varmistin, että salaus on päällä.  
![image](https://user-images.githubusercontent.com/71498717/203821591-375af73d-f30a-48b1-bb83-6c4fcb174ed6.png)  
Sitten avataan zapilla proxy ja siepataan sertifikaatti. Ilmeisesti kun avaa sillä pikanäppäimellä proxy selaimen osaa zap automaattisesti viedä sertifikaatin. Mutta se selviää kohta. Ilmeisesti tämä on totta kun tutkii https://localhost/ sivun sertifikaattia.  
![image](https://user-images.githubusercontent.com/71498717/203823057-21c60e6c-f729-4bd3-b4f4-6bbd020e1af9.png)  
![image](https://user-images.githubusercontent.com/71498717/203823161-4d4a009a-7cf5-4530-996e-21722cebe71c.png)  
Joten sivu palautuu http muodossa.



# C Mitmproxy. Asenna mitmproxy. Surffaile wepissä ja näytä, että liikenne näkyy proxyssa.
Mitmproxy oli myös asennettuna Kalissa valmiiksi, mutta ```pip install mitmproxy``` komennolla sen saa asennettua.
Asennetaan myös foxyproxy lisäosa firefoxiin https://addons.mozilla.org/en-US/firefox/addon/foxyproxy-standard/  
Käynnistetään mitmproxy komennolla ```mitmproxy 8080 ``` joka käynnistää proxyn porttiin 8080. Sen jälkeen konfiguroidaan foxyproxyyn oikea proxy osoite.  
![image](https://user-images.githubusercontent.com/71498717/203828573-cf865d37-6b49-4374-b518-451ab7f797ec.png)  
Nyt liikenne näkyy mitmproxyssä.  
![image](https://user-images.githubusercontent.com/71498717/203828733-d5c019ce-3e7a-49a5-86c9-862c22a2e587.png)  


# D Totally Legit Sertificate. Sieppaa Mitmproxylla TLS-salattua liikennettä.
Kokeillaan ensiksi siirtyä https://localhost/ sivulle vain mitmproxy päällä, mutta se ei onnistu.  
![image](https://user-images.githubusercontent.com/71498717/203831580-77f91e98-a625-4304-b149-6992285d2cc3.png)  
Täytynee generoida oma sertifikaatti, jotta saadaan sivu auki. Mitmproxyn dokumentaatiosta löytyy ohjeet sertifikaatin generointiin. https://docs.mitmproxy.org/stable/concepts-certificates/  
Generoidaan sertifikaatti pem formaatissa. ```openssl genrsa -out cert.key 2048```openssl req -new -x509 -key cert.key -out cert.crt ``` ```cat cert.key cert.crt > cert.pem ``` Käynnistetään mitmproxy uudestaan komennolla ```mitmproxy --certs *=cert.pem -p 8080``` ja kokeillaan toimiiko nyt. Ja eihän se toimi taitaa olla apachessa asetus joka estää käytön itse varmistetuilla sertifikaateilla. Ei ollut siitäkään kiinni. Jäikin kiinni mitmproxyn asetuksista.  Eli käynnistetään mitmproxy komennolla ```mitmproxy --certs *=cert.pem --ssl-insecure -p 8080```  
![image](https://user-images.githubusercontent.com/71498717/203834963-84fb61b2-6623-4415-8543-ef2c92ae343a.png)
Näin saatiin siepattua tls salattua liikennettä ainakin todennäköisesti. Täytyypä varmistaa vielä wiresharkilla. Salattua liikenne oli.  
![image](https://user-images.githubusercontent.com/71498717/203835800-2f97a153-1003-4d70-a4be-a02ae928d2ef.png)  


# E Ratkaise valitsemasi WebGoat-tehtävä niin, että käytät välimiesproxya, esim ZAP tai Mitmproxy.
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
# 
