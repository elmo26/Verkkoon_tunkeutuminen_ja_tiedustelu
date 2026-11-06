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

## d) Too complex 16?

## e) Ultimate: URH asennus

  ### f) yleiskuva

  ### g) Bittistä

## h) 

## i) 


## Lähteet: 
Karvinen, T 2026. Verkkoon tunkeutuminen ja tiedustelu:

https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/ Luettu 6.11.2026

Karvinen, T 2026. Verkkoon tunkeutuminen ja tiedustelu oppitunti 3.11.2026.
