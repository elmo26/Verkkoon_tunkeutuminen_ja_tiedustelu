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

Tässä avasin siepatusta liikenteestä ensimmäisen paketin. Sovelluskerroksen (application layer) protokollana on DNS, eli kysessä on osoitekysely. Kuljetuksekerroksen (Transport layer) protokollana on UDP ja kohdeporttina 53, mitkä ovat DNS oletusportti ja protokolla (toki myös tcp/53 on varattu DNS:lle). Verkkokerroksen (internet layer) osalta näkyy että protokolla on ipv4. Kohdeosoitteen ja lähtevän osoitteen osalta, kuten arvata saattaa, kohdeosoite on lähiverkon default gateway, lähtevä osoite virtuaalikone.
Viimeisenä nähdään siirtoyhteyskerros (link layer), josta näkee kohdeosoitteen ja lähtevän osoitteen MAC-osoitteet. Myös paketin koko (87 tavua) ja siepattava verkkokortti näkyvät.

<img width="1046" height="770" alt="kuva" src="https://github.com/user-attachments/assets/6dcd6eab-e3a7-4347-b8ed-736342af1870" /> Kuva 4. TCP/IP malli yksittäisessä paketissa.


## e) Mitäs tuli surffattua

## f) Vapaaehtoinen, vaikea: Mitä selainta käyttäjä käyttää? 

## g) Minkä merkkinen verkkokortti käyttäjällä on? 

## h) Millä weppipalvelimella käyttäjä on surffaillut?

## i) Analyysi

## Lähteet
Karvinen, T.2026 Verkkoon tunkeutuminen ja tiedustelu: oppitunti 27.10.2026

Karvinen, T.2026 Verkkoon tunkeutuminen ja tiedustelu: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/ Luettu 28.10.2026

Karvinen, T.2025 Wireshark - Getting Started: https://terokarvinen.com/wireshark-getting-started/ Luettu 28.10.2026

Karvinen, T.2025 Network Interface Names On Linux: https://terokarvinen.com/network-interface-linux/ Luettu 28.10.2026

geeksforgeeks, 29.1.2024. Disabling and Enabling an Interface on Linux System: https://www.geeksforgeeks.org/linux-unix/disabling-and-enabling-an-interface-on-linux-system/ Luettu 28.10.2026
