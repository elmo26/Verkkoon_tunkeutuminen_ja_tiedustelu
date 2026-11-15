# h4 - NFC ja RFID

## a) Tarkastele käytössäsi olevia RFID tuotteita, mieti miten hyvin olet suojautunut RFID urkinnalta?

RFID eli radio frequency identification ja NFC eli Near-field communication ovat erittäin suosittuja langattomia lähilukuteknologioita, jotka näkyvät jokaisen arjessa nykyään. NFC toimii vain hyvin lyhyllä 
etäisyyksillä (joitakin senttejä) (Wikipedia - Near-field communication). Vanhempi RFID-teknologia voi toimia pidemmilläkin etäisyyksillä, joskin suurin osa rfid-tunnisteista ovat passiivisa eivätkä
käytä omaa virtalähdettä, mikä lyhentää niiden kantamaa käytetystä taajuudesta riippuen maksimissaan muutamiin metreihin, todellisuudessa usein paljon lyhyemmälle matkalle (Wikipedia RFID). Tämä on oleellista tietoa kun mietitään miten urkinnalta suojaudutaan.

Itselläni on käytössä seuraavat rfid- ja/tai nfc-teknologiaa hyödyntävät ratkaisut.

* Puhelimen apple wallet, jonka kautta käytän pankkikorttia ja kauppojen etukortteja
* fyysinen pankkikortti
* passi, jossa on siru
* Muita kortteja, kuten kirjastokortti ja välillä kuntosalin kulkukortti

Suurimmat riskit RFID-urkinnalle itselläni ovat kun liikun julkisilla paikoilla tai paikoissa, joissa on paljon ihmisiä. Lähtökohtaisesti pyrin suojautumaan mahdolliselta urkinnalta pitämällä mukanani mahdollisimman vähän mahdollista hyökkäyspinta-alaa eli siis käytännössä kortteja. Esimerkkinä en yleensä pidä mukanani pankkikorttia, passia tai muita kortteja kuin ajokortin. Ajokortissa ei ole sirua ja se on myös turvallisempi fyysisestä näkökulmasta, sillä se ei ole virallinen henkilöllisyystodistus.

Passin ja pankkikortin sirut ovat lähtökohtaisesti hyvin suojattuja, joten etäurkinta ei näiden kohdalla muodostu kovin suuriksi riskeiksi, silloin harvoin kun niitä mukanani pidän. Pankkikorttilla maksaessa maksutapahtuma on suojattu niin, että kortti ei luovuta kaikkia tietoja maksupäätteeseen, joten vaikka maksupääte olisi vihamielinen, ei korttia saisi kopioitua ainakaan kaikkine tietoineen. Puhelimen tapauksessa lähimaksutoiminnossa NFC:n käyttö vaatii biometrisen tunnistuksen, ja itse maksutapahtuma on fyysistäkin korttia turvallisempi, sillä puhelin ei luovuta edes niitä tietoja mitä fyysinen kortti.

Suurimman riskin kortti on varmaankin omalla kohdallani salin kulkukortti. Tälle väitteelle ei ole mitään faktapohjaa, muuta kuin se, että kyseessä ei ole niin kriittinen kortti ja sen ylläpitovastuu on yksittäisellä firmalla. En ole kyseistä korttia yrittänyt lukea tai vastaavaa, joten pelkkä ajatus.

Itseltäni löytyy myös rfid-suojattu lompakko, joten fyysinen urkinta on täten mahdotonta. Paras suojautumistapa omalla kohdallani on edellämainittujen asioiden lisäksi olla hereillä ja pyrkiä siihen, että en jätä esim salikorttia salilla ollessani hetkeksikään kenenkään ulottuville.


## b) Tutustu APDU komentojen rakenteeseen (voit käyttää tekoälyä tutustumiseen)

Kysyin Gemini pro 3.1 - tekoälymallilta asiasta kopioimalla ylläolevan tehtävänannon suoraan tekoälylle.

APDU eli Application Protocol Data Unit kometoja käytetään älykorttien kuten maksukorttien, SIM-korttien ja muiden nfc-protokollaa hyödyntävien korttien kanssa ja lukijalaitteiden välisessä tiedonsiirrossa ISO/IEC 7816-4 -standardin mukaisesti. Tiedonsiirto perustuu komento-vastaus pariin : Command APDU (C-APDU) ja Response APDU (R-APDU).

<img width="973" height="674" alt="kuva" src="https://github.com/user-attachments/assets/fa622711-f252-4709-b36a-05cd3be7d72c" />
Kuva 1. Gemnini Pro 3.1 Lukijalaitteen C-APDU

<img width="972" height="391" alt="kuva" src="https://github.com/user-attachments/assets/3a3abbeb-cb79-4de1-9c07-12c6081cf672" />

Kuva 2. Gemini Pro 3.1 Kortin R-APDU.



Pakollisten kenttien lisäksi (CLS, INS, P1, P2) Lukijalaitteen C-APDU: dataa menee 0-65535 tavua. Kortin pakollisten kenttien (SW1, SW2) lisäksi R-APDU: 0-65535 tavua.

## c) Tutki ja kerro minkä mielenkiintoisen RFID hakkerointi uutiset löysit. 

Löysin uutisen vuodelta 2024, jossa Mifare Classic korttien klooneista, Kiinalaisen Shanghai Fudan Microelectronics firman rfid-korttien versiosta, joka käytti FM11RF08S sirua, löydettiin takaovi (CVE-2026-4648). Tämä CVE on vuodelta 2026, sillä samaa sirua käyttäneet maksurannekkeet sisälsivät saman FM11RF08S-sirun aiheuttaman takaoven. Vuonna 2024 tapaukselle ei annettu omaa CVE:tä. 
Linkki uutiseen: https://thehackernews.com/2024/08/hardware-backdoor-discovered-in-rfid.html 

En jaksanut lukea koko tieteellistä paperia haavoittuvuudesta, johon on linkki uutisessa, joten pyysin tekoälyä tekemään yhteenvedon tapauksesta. Promptasin Gemini Pro 3.1 mallia seuraavasti: "mifare classic FM11RF08S haavoittuvuus, ja miten se toimii". 

Käytännössä muuttamalla yhtä bittiä kortille lähetettävässä viestissä, hyökkääjä kykeni pääsemään ylläpitotilaan tarkoitettuun paikkaan, joka on suojattu avaimella. Tässä tapauksessa tutkijat olivat onnistuneet brute-forceamaan kyseisen avaimen läpi, joka huomattiin olevan sama jokaisessa FM11RF08S sirullisessa kortissa. Eli siis tämän avaimen ollessa tiedossa, jokaiseen FM11RF08S korttiin pääsee sisään lukemaan kortin datat ja voi vaikkapa kloonata kortin.

Tätä kyseistä "ylläpitokanavaa" johon päästiin sisään, ei ole virallisissa NXP:n valmistamissa Mifare classic korteissa. Ilmeisesti näitä muiden valmistajien kloonikortteja käytetään suhteellisen paljon, joten tämä on merkittävä haavoittuvuus.

Kyseistä haavoittuvuutta ei myöskään voi korjata internetin yli, koska kyseinen takaovi on fyysisellä tasolla. Eli lienee erittäin todennäköistä että kyseistä korttiversiota käytetään edelleen useissa paikoissa.



## Lähteet

Iso-Anttila, L. 2026. Verkkoon tunkeutuminen ja tiedustelu. Luento 10.11.2026. Haaga-Helia ammattikorkeakoulu.

Karvinen, T. 2026. Verkkoon tunkeutuminen ja tiedustelu. Verkkosivusto. Luettavissa: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/. Luettu: 15.11.2026.

Wikipedia 2026. RFID.
Luettavissa: https://fi.wikipedia.org/wiki/RFID Luettu 15.11.2026.

Wikipedia 2025 . Near-field communication.
Luettavissa: https://fi.wikipedia.org/wiki/Near-field_communication Luettu 15.11.2026.

Gemini Pro 3.1 Tekoälymalli. Promptattu ja luettu 16.11.2026

Thehackernews.com 2024. Hardware Backdoor Discovered in RFID Cards Used in Hotels and Offices Worldwide Luettu 16.11.2026
Luettavissa: https://thehackernews.com/2024/08/hardware-backdoor-discovered-in-rfid.html
