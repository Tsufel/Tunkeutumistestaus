# A
# B
# C
# D
# E
# F
# G
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
