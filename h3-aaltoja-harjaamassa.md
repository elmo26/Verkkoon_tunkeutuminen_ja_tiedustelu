# h3 Aaltoja harjaamassa

Alustana kaikissa tehtävissä oli Kali Linux virualboxin päällä, host käyttöjärjestelmä Windows 11.

## x) Lue ja tiivistä

### Hubacek 2019: Universal Radio Hacker SDR Tutorial on 433 MHz radio plugs tiivistelmä [3:19-7:40](https://www.youtube.com/watch?v=sbqMqb6FVMY&t=199s)
* Spectrum analyzer työkalulla on hyvä varmistaa, että signaalien sieppaaminen onnistuu ja varmistaa siepattavien signaalien taajuus.
* Signaalin tarkka taajuus tulee valita signaalin keskiosasta.
* Varmista signaalin modulaatiotapa ja bittien pituus oikeaksi.
* Demodulaation jälkeen signaalin muotoa voi vaihtaa esim. heksadesimaaleiksi.

### Cornelius 2022: [Decode 433.92 MHz weather station data](https://www.onetransistor.eu/2022/01/decode-433mhz-ask-signal.html) tiivistelmä
* Signaalien dekoodaamiseen riittää tieto signaalin taajuudesta ja softaradio.
* rtl_433 osaa automaattisesti dekoodata signaaleja. Kaikki tuetut signaaliprotokollat ja laitteet löytyvät [tietokannasta](https://triq.org/explorer/)
* URH:n avulla voi tallentaa, analysoida, muuttaa ja lähettää signaaleja. Lähettämiseen vaaditaan lähettävä laite.

### Lohner 2019: [Decoding ASK/OOK_PPM Signals with URH and rtl_433 ](https://github.karllohner.com/SDR/Decoding/Example_2019-01-24/) tiivistelmä
* URH:n asetuksia voi muuttaa tarkemmiksi ja virhemarginaaleja pienentää.
* Tarkastele symbolit yksi kerrallaan: selvitä pituus bitteinä. 
* URH:n analysis-sivun takana on dekoodaus-menetelmiä.
* rtl_433: selvitä modulaatio ja lähetysväli ja dekoodaa signaali niiden avulla

## b) rtl_433 asennus ja ajo

Kali Linuxissa on rtl_433 paketti saatavilla.

Asensin rtl_433 ohjelman komennolla `sudo apt-get update && sudo apt-get istall rtl-433` ja tarkistin että se on ajettavissa komennolla `rtl_433`

<img width="826" height="224" alt="rtl_433" src="https://github.com/user-attachments/assets/08f1dd27-6f0e-4eb2-b946-0495653629e4" />

Kuva 1. rtl_433 asennettuna ja asennuskomento haettu esille.

## c) Automaattinen analyysi

Latasin tehtävässä osoitetun näytteen ja avasin sen rtl_433 ohjelmalla komennolla `rtl_433 Converted_433.92M_2000k.cs8`


<img width="1470" height="864" alt="kuva" src="https://github.com/user-attachments/assets/18c43dca-3ace-4627-8db6-c02952846f92" />
Kuva 2. rtl_433 avattu näyte `Converted_433.92M_2000k.cs8`

`8785315` ID/House Code kentässä viittaa lähettävän laitteen tunnisteeseen. Ideana se, että jos useampi laite on samalla taajuudella, ei kaikki laitteet mene yhdestä signaalista päälle. 

Näytteessä näyttäisi näkyvän neljä signaalia, jotka kaikki listataan rtl_433 toimesta kolmeen kertaan. Tämän voi päätellä siitä, että aika sekä id tai "House Code", kuten kuvastakin näkyy, 
ovat kolmella ensimmäisellä rivillä samat. Nimet, jotka toistuvat samalla logiikalla koko näytteen ajan ovat `KlikAanKlikUit-Switch`, `Proove-Security` ja `Nexa-Security` sekä id/House code arvolla `8785315`.

Kurkkasin Corelius-tiivistelmässä mainitusta tietokannasta (https://triq.org/explorer/) dekoodereita näytteessä näkyvillä nimillä ja löysin kolme erillistä dekooderia. Nämä ovat numerot 15, 51 ja 96. Yhdessä oli mainittu kaikki kolme merkkiä.
Oletukseni on, että kolme eri valmistajaa käyttää samaa lähetintä tms: mainitaan dekooderissa #51.

<img width="1084" height="401" alt="kuva" src="https://github.com/user-attachments/assets/499927d8-11a5-4248-a422-b9ae5cee28a8" />
Kuva 3. Kaksi kolmesta eri dekooderista - Samankaltaisia.



## d) Too complex 16?

Tässä piti konvertoida URH:lla nauhoitettu `.complex16s` tiedosto `rtl_433` yhteensopivaan muotoon ja analysoida se. Aloitin lataamalla näytteen tehtävästä ja katsomalla vinkit tehtäväsivun alta.
Vinkeissä mainittiin seuraavat asiat: Vain tiedoston nimi muuttuu, tiedoston nimessä pitää olla oikea taajuus ja näytteenottotaajuus ja nämä erotetaan väliviivalla.

Kokeilin ajaa suoraan alkuperäisen `.complex16s` päätteisen tiedoston ja se meni läpi.

<img width="1640" height="437" alt="kuva" src="https://github.com/user-attachments/assets/b26c3818-254c-4ff2-8237-a80c915f7e55" />

Kuva 4. Alkuperäisen tiedoston ajo.


Selvittelin hieman asiaa ja ilmeisesti `cs8` ja `complex16s` on sama tiedostotyyppi eri merkintätavalla [(rtl_433 dokumentaatio, IQ_FORMATS.md)](https://github.com/merbanan/rtl_433/blob/ac1e4a8c5a36fb90e3b06c0f01cef00bb3b2614d/docs/IQ_FORMATS.md#file-name-meta-data), ja rtl_433 tukee tätä tiedostotyyppiä, joskin `cu8` on rtl_433-ohjelman oletustiedostotyyppi.

Kokeilin kuitenkin muuttaa tiedoston ohjeiden mukaan. Alkuperäinen tiedosto oli `Recorded_HackRF-20250412_113805-443_92MHz-MSps-2MHz.complex16s` ja sain ohjeiden perusteella siitä tiedoston `näyte_433.92M_2000k.cs8` Komennolla
`cp Recorded-HackRF-20250411_183354-433_92MHz-2MSps-2MHz.complex16s nayte_433.92M_2000k.cs8`

<img width="1367" height="565" alt="kuva" src="https://github.com/user-attachments/assets/6a69933a-bbe8-455b-b395-2d685999d8c9" />

Kuva 4. Tiedoston nimenvaihto ja ajo.

Tässähän ei mikään muuttunut ja näyte näyttäisi olevan sama kuin kohdassa c. Myös näytteen analyysi siis siellä.

Jälkikäteen selvitin kun kaveri ei saanut ajettua tuota complex16s-tiedostoa, että kalissa rtl_433-ohjelma on uudemmassa versiossa (25.12) vs debian (25.02). Mitä ilmeisemmin uudessa versiossa rtl osaa ajaa complex16s-tiedostot suoraan, sillä mitään muuta selitystä en tälle keksinyt.

## e) Ultimate: URH asennus

Aloitin tehtävän jälleen katsomalla ohjeet tehtäväsivun lopusta (Karvinen, 2026), jonka jälkeen aloitin pipx:n asennuksen komennoilla `sudo apt-get update && sudo apt-get install pipx`.
Tämän jälkeen asensin Ultimate Radio Hackerin komennolla `pipx install urh`. 

<img width="1013" height="322" alt="kuva" src="https://github.com/user-attachments/assets/1f3a991e-c406-4df1-863e-dfdf97f7d008" />
Kuva 5. URH asennus.

  ### f) yleiskuva

  Avasin URH-ohjelman komennolla `urh`, menin ohjelmassa file-> open -> ja `1-on-on-on-HackRF-20250412_113805-433_912MHz-2MSps-2MHz.complex16s`.

  Avasin URH-ohjelmassa tiedoston signal-details kohdan ja päättelin tiedostonimestä näytteen taajuuden olevan `433.912MHz` sekä muutin sample rate kohdan `2.0M` eli 2 miljoonaa näytettä per sekunti. 
  Täten näytteen kesto 2,75 sekuntia. Näytteen nauhoittamisajankohtaa en saanut selville.

  
  <img width="1431" height="430" alt="Näyttökuva 2026-11-07 013346" src="https://github.com/user-attachments/assets/28959bd0-87b1-4970-8ab2-59467e8435e7" />
  Kuva 6. perustietoja näytteestä.
  
  Aloin katsomaan yleiskuvaa signaalista. Silmämääräisesti näyttää siltä, että näytteessä olisi kolme samanlaista signaalia.

  <img width="1825" height="303" alt="kuva" src="https://github.com/user-attachments/assets/b89846be-c268-494c-815a-fa870236b408" />

  

  ### g) Bittistä

## h) 

## i) 


## Lähteet: 
Karvinen, T 2026. Verkkoon tunkeutuminen ja tiedustelu:

https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/ Luettu 6.11.2026

Karvinen, T 2026. Verkkoon tunkeutuminen ja tiedustelu oppitunti 3.11.2026.

TRIQ.ORG s.a. a Decoder & sample explorer: https://triq.org/explorer/ luettu 6.11
