# Ensimmäisessä tehtävässä oli tarkoitus analysoida verkkoliikennettä

Tehtiin Wiresharkilla tallennus omasta verkkoliikenteestä

https://user-images.githubusercontent.com/71498717/197484865-2a23c394-ed0f-4a3f-b979-a5bd3e97c260.jpg

Viiden sekunnin tallennuksella tuli 569 pakettia dataa.

UDP filtteröinnillä tuli paljon dataa joka meni 132.226.xxx.xxx osoitteeseen, mikä todennäköisesti johtuu yhteydestä jits video konfferenssiin.

https://user-images.githubusercontent.com/71498717/197486477-1e84835c-3136-4044-8921-67531b79eb63.png



https://user-images.githubusercontent.com/71498717/197484893-6488f514-d44a-4310-be97-e724b3848458.jpg

Kuten kuvasta näkyy tavaraa tuli riittävästi ja kun filtteröin näytettävän liikenteen tcp filtterillä, niin silmään osui HTTP/JSON protokollan paketti.

https://user-images.githubusercontent.com/71498717/197484921-b02f158c-4151-4972-9f73-45fcb2d9e012.jpg


Tätä pakettia kun tarkemmin tutki sieltä löytyi omat HUE lamput, jotka ovat yhteydessä wifiin. Yllättävää kyllä tätä tietoa ei oltu yritettykkään piilottaa vaan se löytyi selkokielisenä
Asiaa kun tarkemmin tutki niin näitä lamppuja on kritisoitu niiden tietoturva puutteista ja se ei kyllä yllätä.