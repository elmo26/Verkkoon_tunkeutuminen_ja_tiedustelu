# h2 Nuuh

## x) Lue ja tiivistä
Tiivistelmä artikkeleista [Wireshark - Getting Started (Karvinen 2025)](https://terokarvinen.com/wireshark-getting-started/) ja [Network Interface Names On Linux (Karvinen 2025)](https://terokarvinen.com/network-interface-linux/).

### Wireshark - Getting Started
* Wiresharkin asennus `sudo apt update && sudo apt install wireshark` (apt-get ei ole tarpeellinen nykyään). "Should non-superusers be able to capture packets?" Yes.
* Lisätään käyttäjä wireshark-ryhmään, jotta voimme ajaa snifferiä. `Sudo adduser "user" wireshark`. Ryhmä päivittyy kun seuraavan kerran kirjautuu sisään.
* Wiresharkin GUI:n saa terminalista auki komennolla `wireshark`.
* Valitse sniffatessa sellainen interface (verkkokortti, fyysinen tai virtuaalinen), jossa on liikennettä.
* Kaapatessa liikennettä, fiksua kaapata mahd. vähän, jotta läpikäyminen on helpompaa. Jos kaappauksessa ei ole liikennettä, kokeile generoida sitä: Avaa vaikka jokin nettisivu selaimella.
* Statistics-valikon avulla voi valita, mitä kyseisestä kaappauksesta haluaa nähdä. Esimerkiksi kaikki kaappauksessa esiintyvät hostit ja protokollahierarkia.
* Display filter -palkista voi filtteröidä kaapattua liikennettä esimerkiksi protokollan, portin, ip-osoitteen tai domainin perusteella.

### Network Interface Names On Linux
* Verkkokortin nimen alku perustuu sen tyyppiin. `lo` = loopback adapteri (localhost), `wl` = WLAN (WiFi) ja en =  Wired Ethernet.
* Esimerkkejä: `lo` = loopback adapteri, `ensp1s0` = langallinen Ethernet verkkokortti, `wlp4s0` = wifi (langaton) verkkokortti.
* Kuinka tarkastaa interfacet : `ip a`

## a) Linux
Kali linux on valmiiksi asennettuna pöytäkoneelle. Alustana virtualbox ja host-koneena Windows 11.

## b) Ei voi kalastaa

Boottasin kalin ja katsoin interfacejen tilanteen komennolla `ip a`. Interface `eth0` oli ylhäällä `state UP`. Todensin internetyhteyden toimivuuden pingaamalla googlen nimipalvelua  `ping -c4 8.8.8.8`.
Kuten kuvasta näkyy, internetyhteys oli ylhäällä.

<img width="1245" height="685" alt="kuva" src="https://github.com/user-attachments/assets/57c15722-f3e3-475f-a841-d87d6277c890" />Kuva 1. virtuaalikoneen internetyhteys ylhäällä, onnistunut ping-testi.



Sain ajettua internetyhteyden alas komennolla `sudo ip link set eth0 down` [(GeeksForGeeks 2024)](https://www.geeksforgeeks.org/linux-unix/disabling-and-enabling-an-interface-on-linux-system/).
Tämän jälkeen näin `ip a` komennolla, että interface `eth0` on tilassa `DOWN`. Varmistin vielä  yhteyden toimimattomuuden yrittämällä pingata googlen nimipalvelua komennolla `ping -c4 8.8.8.8`. Vastauksena
Sain: `Ping: connect: Network is unreachable`.

<img width="1181" height="563" alt="kuva" src="https://github.com/user-attachments/assets/042282eb-fdc7-4cb7-8b8a-684b3ce5e0cc" />Kuva 2. Virtuaalikoneen internetyhteys ajettu alas, epäonnistunut ping-testi.


Lopussa palautin internetyhteyden takaisin ylös komennolla `sudo ip link set eth0 up` ja todensin sen toimivuuden pingaamalla googlen nimipalvelua vielä kerran: `ping 8.8.8.8`.

## c) Wireshark

Wireshark on minulla valmiiksi asennettuna kali linuxille. En ollut bootannut pöytäkoneen kalia pitkään aikaan, joten ajoin komennon `sudo apt update && sudo apt full-upgrade -y && sudo apt autoremove --purge && sudo apt clean`. Tällä komennolla ajoin uusimmat päivitykset sisään ja poistin samalla turhat "riippuvuudet" joita vanhoissa paketeissa oli. 

Päivityksen jälkeen starttasin wiresharkin komennolla `wireshark` ja aloitin liikenteen sieppaamisen `eth0` kortilta. Generoin hieman liikennettä menemällä selaimella WebSdr-sivustolle ja sieltä edelleen Washington DC:ssä sijaitsevan vastaanottimen sivulle. Linkki: http://na5b.com:8902/ Tämä siksi, että nämä sivustot on salaamattomia, ja ajattelin sieppauksen tuloksien olevan mielenkiintoisempia. Sieppasin liikennettä muutaman sekunnin ja paketteja kertyi yli 7000.

<img width="1522" height="692" alt="kuva" src="https://github.com/user-attachments/assets/2f595378-d0ed-4a69-b830-a34d2d9f729c" /> Kuva 3. Wireshark ja liikenteen sieppaus.


## d) Oikeesti TCP/IP 

Tässä avasin siepatusta liikenteestä ensimmäisen paketin. Sovelluskerroksen (application layer) protokollana on DNS, eli kysessä on osoitekysely. Kuljetuksekerroksen (Transport layer) protokollana on UDP ja kohdeporttina 53, mitkä ovat DNS oletusportti ja protokolla (toki myös tcp/53 on varattu DNS:lle). 

Verkkokerroksen (internet layer) osalta näkyy että protokolla on ipv4. Kohdeosoitteen ja lähtevän osoitteen osalta, kuten arvata saattaa, kohdeosoite on lähiverkon default gateway, lähtevä osoite virtuaalikoneeen eth0 verkkokortti. Viimeisenä nähdään siirtoyhteyskerros (link layer), josta näkee kohdeosoitteen ja lähtevän osoitteen MAC-osoitteet. Myös paketin koko (87 tavua) ja siepattava verkkokortti näkyvät.

<img width="1046" height="770" alt="kuva" src="https://github.com/user-attachments/assets/6dcd6eab-e3a7-4347-b8ed-736342af1870" /> Kuva 4. TCP/IP malli yksittäisessä paketissa.


## e) Mitäs tuli surffattua

Avasin surfing-secure.pcap:in ja aloin muodostamaan yleiskuvaa liikenteestä katsomalla ensin pakettien määrän ja sieppaus ajan: alle 300 pakettia ja noin 7,5 sekuntia (joskin noin 4 sekunnin tauko keskellä capturea, joten voi olla että se keskeytettiin ja aloitettiin uudelleen.)  Sitten katsomalla kohdasta statistics->protocol hierarchy, katsoin, että mistä protokollista liikenne koostuu. Suurin osa TCP-liikennettä, pieni osa UDP-liikennettä, ja siitä suurin osa DNS-liikennettä. Katsoin myös nopeasti statistics->endpoints, että montako eri osoitetta löytyy: Ipv4-osotteita oli 7, ipv6 ei esiintynyt kaappauksessa lainkaan.

DNS-kyselyiden perusteella ja yleisesti TCP ja TLS liikenteiden perustella voi sanoa, että kyseisessä kaappauksessa esiintyy pääasiassa weppiliikennettä eli https liikennettä. 

## f) Vapaaehtoinen, vaikea: Mitä selainta käyttäjä käyttää? 

Aloitin tehtävän käymällä läpi kaappauksen ja etsimällä salaamatonta liikenettä, jota en löytänyt. Tämän jälkeen aloin etsimään netistä tietoa, miten käyttäjän selaimen voisi selvittää salatusta liikenteestä. Löysin artikkelin JA3 ja JA4 tiivisteistä: [JA3 and JA4 Compared](https://www.coronium.io/blog/ja3-vs-ja4). Käytännössä kyseessä on siis kaksi eri tavalla toteutettua algoritmia, jotka sisältävät teknistä tietoa esim. käytetystä salausalgoritmeista. Algoritmit muodostetaan TLS Client hello pakettien perusteella, joissa salauksesta sovitaan ennen http-liikenteen lähettämistä.

Eri selaimet käyttävät eri kirjastoja salauksiin, joten tätä kautta selain voidaan tunnistaa.

<img width="1842" height="706" alt="kuva" src="https://github.com/user-attachments/assets/a46c0b7d-381d-40bf-92c0-83902e43ef9f" />
Kuva 5. TLS-paketti, sisältää JA3, JA4 arvot.

Netistä löytyi useita työkaluja JA4-tiivisteiden tunnistamiseen, kuten www.usefoil.com/ja4 , jota itse käytin.

<img width="1094" height="768" alt="kuva" src="https://github.com/user-attachments/assets/1e41d279-b97c-4bfb-bfbb-f8a4da7bd333" />
Kuva 6. JA4-tiivisteen tunnistus,

Kuten kuvasta näkyy, sivusto ilmoitti että kyseinen JA4-tiiviste löytyi sivun referenssitietokannasta ja viittaa Firefox-selaimeen. Kaappauksessa käytetty selain on siis Firefox.

## g) Minkä merkkinen verkkokortti käyttäjällä on?

Käyttäjän verkkokortin merkin tunnistamiseen tarvitsemme MAC-osoitteen OUI(Organizationally unique identifier) osan. Ensin on tiedettävä käyttäjän ip-osoite, jotta katsomme oikeaa pakettia. DNS-liikenteen perusteella käyttäjän ip-osoite on oletettavasti `192.168.122.7`, sillä tästä osoitteesta lähtee DNS-kyselyt ja vastaukset tulevat tähän osoitteeseen. MAC-osoitteet löytyvät protokollapinosta link layerin alta, Ethernet-kohdan alta.
IEEE jakaa kaikille laitevalmistajille omat OUI:t, joista voidaan nähdä laitteen MAC-osoitteen perusteella, kenen valmistama se on.

<img width="808" height="187" alt="kuva" src="https://github.com/user-attachments/assets/fe519d8f-8552-4301-a762-aa49c3a9bb45" />

Kuva 7. surfing-secure.pcap kaappauksen ensimmäinen paketti, DNS-kysely. Mac-osoitteet näkyvissä.

Source MAC eli lähtevä MAC-osoite `52:54:00:2f:e1:e5` on merkitty LAA (Locally administered adress), eli toisinsanoen kyseessä ei ole verkkokortin raudassa oleva MAC, vaan ohjelmallisesti toteutettu MAC-osoite, josta on mahdoton päätellä valmistajaa, sillä sellaista LAA-osoitteille ei ole. MAC-lookup millä tahansa netistä löytyvällä sivulla antaa saman vastauksen: LAA-osoite, ei OUI-osaa.


## h) Millä weppipalvelimella käyttäjä on surffaillut?

DNS-kyselyiden perusteella käyttäjä on surffaillut seuraavilla weppipalvelimilla: www.google.com, terokarvinen.com, gc.zgo.at(CNAME) -> goatcounter.netlify.com ja terokarvinen.goatcounter.com


## i) Analyysi

Käytin tässä analyysissä samaa tallentamaani kaappausta jonka toteutin C-kohdassa. Kaappauksen toteutuksesta ja ympäristöstä enemmän tietoa C-kohdassa. Otin tarkasteluun pienen määrän paketteja, sillä ohjeissa kehotettiin tarkastelemaan todella pientä määrää liikennettä. Tässä on tarkastelussa vain vain pieni määrä paketteja.


<img width="1578" height="240" alt="kuva" src="https://github.com/user-attachments/assets/fae4a3b2-b323-4e5e-a2d8-277a0fe8e816" />

Kuva 8. Tarkemmin tarkasteltavat paketit

#### Yleistiedot kaappauksesta:
* Virtuaalikoneen `eth0` verkkokortin MAC-osoite `08:00:27:de:11:e2`, ip-osoite `10.0.2.15`
* oletusyhdyskäytävän  MAC-osoite `52:54:00:12:35:00`, ip-osoite `192.168.10.1`

#### Ensimmäiset 4 pakettia - DNS-kyselyt

* Asiakas (virtuaalikone) ei tiedä, missä osoitteessa on safebrowsing.googleapis.com
* Paketit 1,2: Asiakas kysyy osoitteita ipv4 (A) ja ipv6 (AAAA) tietueista domainiin safebrowsing.googleapis.com
* Vastaukset kyselyihin tulevat paketeissa 3 ja 4 : A-tietue `64.233.162.95`, AAAA-tietue: `2a00:1450:4010:c20::5f`

#### TCP-yhteyden muodostus (Three way handshake)
* Paketti 5 (SYN): Asiakas lähettää yhteydenottopyynnön portista 47264 palvelimen porttiin 443
* Paketti 6 (SYN,ACK): Palvelin kuittaa pyynnön, hyväksyy yhteyden avaamisen
* Paketti 7 (ACK) Asiakas kuittaa palvelimen vastauksen. TCP-käsittely on valmis ja yhtyes on auki

#### TLS-käsittely ja salattu tiedonsiirto
* Paketti 8 Client Hello, asiakas aloittaa TLS-kättelyn, ilmoittaa mm. tukemansa salausmenetelmät
* Paketit 9-10 (ACK): Palvelin kuittaa vastaanottaneensa asiakkaan tiedot
* Paketti 11 (Server Hello): Palvelin valitsee salaustavan, siirtyy salattuun tilaan.
* Paketit 12-13: Asiakkaan kuittaus, palvelimen dataa
* Paketti 14: Application data: yhteys salattu - > varsinainen sovelluskerroksen data alkaa liikkumaan





## Lähteet
Karvinen, T.2026 Verkkoon tunkeutuminen ja tiedustelu: oppitunti 27.10.2026

Karvinen, T.2026 Verkkoon tunkeutuminen ja tiedustelu: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/ Luettu 28.10.2026

Karvinen, T.2025 Wireshark - Getting Started: https://terokarvinen.com/wireshark-getting-started/ Luettu 28.10.2026

Karvinen, T.2025 Network Interface Names On Linux: https://terokarvinen.com/network-interface-linux/ Luettu 28.10.2026

geeksforgeeks, 29.1.2024. Disabling and Enabling an Interface on Linux System: https://www.geeksforgeeks.org/linux-unix/disabling-and-enabling-an-interface-on-linux-system/ Luettu 28.10.2026

Coronium.io, s.a. a. JA3 and JA4 compared: https://www.coronium.io/blog/ja3-vs-ja4 Luettu 31.10.2026
