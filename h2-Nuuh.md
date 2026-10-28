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

Bootataan kali ja katsotaan interfacejen tilanne komennolla `ip a`. Nähdään interface `eth0` ylhäällä `state UP`. Todennetaan internet yhteyden toimivuus pingaamalla googlen nimipalvelua  `ping -c4 8.8.8.8`.



## c) Wireshark

## d) Oikeesti TCP/IP 

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

geeksforgeeks, 29.1.2024. Disabling and Enabling an Interface on Linux System: 
