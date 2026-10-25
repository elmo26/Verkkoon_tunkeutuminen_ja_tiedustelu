# h1 Korva netissä

x) Piirsin oman sähkömagneettisen spektrin paintilla, ja merkitsin mihin suuntiin aallonpituus, antennin koko, läpäisevyys
ja kyky siirtää tietoa kasvavat. Otin kolme asiaa havainnollistavaa esimerkkiä ja merkitsin ne spektrin yläpuolelle taajuuden perusteella.
Havainnollistin myös aallonpituutta spektrin yläpuolella olevalla janalla, jonka toisessa päässä on pisimmät ja toisessa lyhimmät aallot.
<img width="1876" height="834" alt="aallonpituus" src="https://github.com/user-attachments/assets/4942c06a-682a-4247-b374-19c8ae5d0093" />

a) Löysin WebSDR.org-sivustolta Washington DC:n lähellä olevan vastaanottimen, etäisyys noin 7000km mitattuna google mapsilla. Sain lähetyksen kuulumaan heti, 
kun painoin äänen päälle. Käytin asetuksia jotka sivulla oli avatessani sen: Taajuus 1499.95 KHz, BAND eli antenni MW-160 ja modulaatio AM eli amplitudimodulaatio. 
Aallonpituuden voi laskea kaavalla λ = v/f, jossa v on aallon nopeus ja f taajuus. Aallon nopeus on sama kuin valon nopeus, sillä radioaalto etenee valon nopeudella.
Aallonpituus = 300/1,5 ≈ 200 metriä. (Pyöristetyt arvot käytössä. 1499.90 KHz ≈ 1,5 MHz, Koska megahertsi = miljoona hertsiä, valon nopeudesta 300 000 000 m/s tulee 300.)
Kuulemani perusteella kyseessä oli yleisradiolähetys, sillä keskustelussa oli yhdysvaltain taloustilanne ja kanavalla toistettiin paljon mainoksia. Tässä vielä linkki
sivulle: http://na5b.com:8901/ 
<img width="1885" height="866" alt="kuva" src="https://github.com/user-attachments/assets/8f29cc65-436e-4913-b654-4206815d9fed" />


b) Aloitin tutkimalla eri modulaatiotapoja ja kysyin tekoälyltä asiasta. Kuten FM-radion nimestä päättelin, ylemmät radiotaajuudet ovat usein FM eli taajuusmoduloituja,
alemmat taas, kuten a-kohdassa, amplitudimoduloituja. A-kohdassa käyttämäni sivu ei kattanut taajuuksia yli 30 MHz enkä löytänyt muita modulaatiotapoja käyttäviä lähetyksiä.
Löysin saman sivun, mutta eri portin alta identtisen sivun, joska löysin taajuusmoduloidun (FM) yleislähetyksen, joka käsitteli USA:n säätä. Linkki: http://na5b.com:8902/
Kanava oli taajuudella 162.5 MHz, kuten kuvasta näkyy.
<img width="1861" height="870" alt="kuva" src="https://github.com/user-attachments/assets/3ea74c77-2556-4fd7-9b13-5071ffceb03e" />

c) Löysin ADS-B exchange -palvelusta lentonumerolla FIN482 lentävän ATR-75-500 lentokoneen. Kone oli lentoradasta päätellen matkalla kuusamosta Helsinki-Vantaalle. 
Koneen maanopeus oli katsomallani hetkellä 230 solmua ja lentokorkeus 18000 jalkaa eli noin 5,5 kilometriä.

Tiedot on saatu ADS-B exchange palveluun ADS-B eli "Automatic Dependent Surveillance–Broadcast" teknologian avulla, jota lentokoneet käyttävät esimerkiksi sijaintitietojen 
jakamiseen radiosignaalien avulla. Lentokoneet jakavat ADS-B:n avulla paikkatietoja ja muita parametreja toisilleen, lennonjohdolle ja kenelle tahansa kellä on ADS-B vastaanotin päällä.
Harrastelijoiden tai organisaatioiden ADS-B vastaanottimet ottavat tiedot vastaan ja lähettävät ne ADS-B exchange sivuston palvelimille.
Näistä tiedoista rakennetaan sivustolle kuva siitä, missä ja milloin mikäkin lentokone on. Vastaanottimet voivat olla maa-antenni pohjaisia tai esim. sateliitteja.

d),e),f) En tehnyt näitä tehtäviä, sillä Android-puhelinta ei ollut käytössäni, joten F-Droidin käyttöönsaaminen olisi ollut haastavaa.

## Lähteet
Tero Karvinen luento 20.10 Haaga-Helia Ammattikorkeakoulu

Tero Karvinen: Verkkoon tunkeutuminen ja tiedustelu - 
https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/

Sähkömagneettinen spektri - https://jkorpela.fi/smag.html Luettu 24.10.2026

Aallonpituus Wikipedia - https://fi.wikipedia.org/wiki/Aallonpituus Luettu 24.10.2026

Gemini 3.8 Flash - promptattu: 
"mitkä taajuudet yleensä käyttää am ja mitkä fm modulaatiota ?" Luettu 24.10.2026

ADS-B exchange about - https://www.adsbexchange.com/about/ Luettu 25.10.2026

Automatic Dependent Surveillance–Broadcast Wikipedia - 
https://en.wikipedia.org/wiki/Automatic_Dependent_Surveillance%E2%80%93Broadcast Luettu 25.10.2026


