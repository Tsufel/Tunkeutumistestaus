# Tunkeutumistestaus
Repo kurssin tehtäviä varten

# Ensimmäisessä tehtävässä oli tarkoitus analysoida verkkoliikennettä

Tehtiin Wiresharkilla tallennus omasta verkkoliikenteestä

https://user-images.githubusercontent.com/71498717/197481726-180a4a80-07c3-4371-a9c4-fb2d347254f2.png

Viiden sekunnin tallennuksella tuli 569 pakettia dataa.

https://user-images.githubusercontent.com/71498717/197480110-228e0db5-6c5f-41eb-9738-9a22119df684.png

Kuten kuvasta näkyy tavaraa tuli riittävästi ja kun filtteröin näytettävän liikenteen tcp filtterillä, niin silmään osui JSON muodossa oleva HTTP pyyntö.

https://user-images.githubusercontent.com/71498717/197481036-08e6f254-20cc-4a7d-98c2-dfb61f9c5bad.png

Tätä pakettia kun tarkemmin tutki sieltä löytyi omat HUE lamput, jotka ovat yhteydessä wifiin. Yllättävää kyllä tätä tietoa ei oltu yritettykkään piilottaa vaan se löytyi selkokielisenä