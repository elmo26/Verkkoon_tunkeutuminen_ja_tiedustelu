# h6 wifi


## a) Tutustu wifi challenge lab 2.1 harjoitus ympäristöön ja käytä tarvittaessa hyväksesi jo olemassa olevia ohjeita.


Aloitin harjoitukseen tutustumisen lukemalla moodlen luentomateriaalit ja WifiChallenge Labin sivut (Wifi lab challenge s.a.a). WifiChallenge labin harjoitusympäristö on `Dockerilla` ja `mac80211_hwsim ` softaradiosimulaattorilla luotu virtuaaliympäristö, joka pyrkii simuloimaan langattomia verkkokokonaisuuksia ja luo täten turvallisen ympäristön WiFi-hyökkäyksien harjoitteluun virtuaalikoeen sisällä. Ympäristö sisältää 17 langatonta verkkoa sisältäen eri salausmenetelmiä, kuten `OWE, WEP, WPA/WPA2-PSK ja WPA3-SAE ` (WifiChallengelab s.a.b.). Ympäristön verkot toimivat taajuuksilla `2,4 , 5 ja 6 GHz` (WiFiChallenge lab s.a.b.).

Latasin harjoitusympäristön kurssin moodle-sivuilta ja liitin ympäristön VMwareen. Ympäristöä ei tarvinnut konfiguroida, se oli valmis sellaisenaan.

Tämän jälkeen tutustuin ympäristöön avaamalla harjoitusympäristön ja tutustumalla netistä löytyvään dodumentaatioon `aircrack-ng` työkaluista (aircrack-ng Main documentation s.a.). `Aircrack-ng suite` oli itselleni ennestään tuntematon, joten siihen tutustuminen vei hetken aikaa. Katsoin myöskin WifiChallenge labin sivuilta löytämästäni walkthrough-osiosta (Calvo Laorden 2024) muutaman esimerkin ensimmäisistä haasteista, minkä jälkeen toistin ratkaisut. Koin tämän helpottavan työkalujen kanssa alkuunpääsyä, varsinkin syntaxin osalta.

Sitten aloin tiedustelemaan verkkoja ensin pistämällä virtuaalisen wifikortin monitorointitilaan komennolla `sudo airmon-ng start wlan1`. Tämän jälkeen aloitin liikenteen kaappaamisen komennolla `sudo airodump-ng wlan1mon -w scan --manufacturer --wps --abg `. Ensimmäinen komento ohjelmalla `airmon-ng` siis asettaa virtuaalisen wifitikun kuuntevaan modeen ja jälkimmäinen komento alkaa kuuntelemaan liikennettä kyseiseltä "tikulta" sekä tallentaa kuunnellun liikenteen tiedostoon. Kuuntelun lopettamisen jälkeen asetin kuuntelumoden pois komennolla `sudo airmon-ng stop wlan1mon`.

<img width="1150" height="480" alt="kuva" src="https://github.com/user-attachments/assets/8c34cfda-2d27-44d1-971e-b58e5bba34dc" />

Kuva 1. kuunneltua liikennettä.


### WEP

Sitten valitsin hyökkäyksen kohteeksi verkon nimeltä wifi-old, sillä se käytti vanhentunutta ja haavoittuvaa WEP-salausta (wikipedia 2026). Verkko näkyy kuvassa ylhäällä ja se käyttää kanavaa `3`, sekä sen BSSID eli mac-tunnus on `F0:9F:C2:71:22:11`.

Tämän jälkeen lähdin hyökkäämään verkkoa vastaan komennolla `sudo besside-ng -c 3 -b F0:9F:C2:71:22:11 wlan1 `. Komennon sain kurssin moodlen luentokalvoista, muutin vain kanavan ja BSSID:n. `Besside-ng` on siis työkalu, mikä kykenee automaattisesti korkkaamaan WEP ja WPA salattuja wifiverkkoja.

<img width="794" height="700" alt="kuva" src="https://github.com/user-attachments/assets/d8125a17-ed26-45a9-b9ea-167d4c320282" />

Kuva 2. Verkon avain saatu.

#### Miten hyökkäys toimi?

 Kuuntelemalla liikennettä saatiin selville, että verkko käytti vanhentunutta salausmetodia. Sieltä saatiin myös verkon käyttämä kanava sekä `BSSID ` nimen eli `ESSID` lisäksi. Näillä tiedoilla kyettiin käyttämään `besside-ng` -ohjelmaa ja sillä saatiin selville verkon avain.

### WPA2-PSK

Sitten valitsin toisen verkon hyökkäyksen kohteeksi. Tällä kertaa valikoitui `WPA2-PSK` salattu verkko sillä `WPA2-PSK`-salauksen murtamisesta oli esimerkki luentokalvoissa. Valitsin kuvassa 1 näkyvistä verkoista verkon nimeltä `wifi-mobile`. Sen `BSSID` on `F0:9F:C2:71:22:12 ` ja kanava `6`. 

Rupesin kaappaamaan liikennettä komennolla `sudo airodump-ng wlan1mon -w wpa-psk -c 6 --wps`. Huom wlan0 on monitor-moodissa.  Generoin myös liikennettä samaan verkkoon komennolla `sudo aireplay-ng -0 10 -a F0:9F:C2:71:22:12 wlan1mon`.

<img width="1598" height="572" alt="kuva" src="https://github.com/user-attachments/assets/374967f5-ad6d-4327-a26e-6978ef697a88" />

Kuva 3. WPA2-PSK capture, generoitu liikenne.

Sitten lopetin liikenteen kaappauksen ja hyökkäsin `aircrack-ng` -ohjelmalla tallennettuun tiedostoon, jossa kaappaus oli. Tämän tein komennolla `sudo aircrack-ng wpa-psk-01.cap -w /root/rockyou-top100000.txt`. Tämäkin komento oli näkyvissä moodlen luentokalvoissa.

Wordlistina toimi komennossa näkyvä `rockyou-top100000.txt`. Tämä siksi, että labin README-tiedostossa (WiFiChallenge lab s.a.b.) oli mainittu, että kaikki salasanalistaa hyödyntävät hyökkäykset on mahdollista suorittaa kyseisellä listalla, joten turha ajaa isompia listoja.

<img width="845" height="482" alt="kuva" src="https://github.com/user-attachments/assets/08462b60-bcbc-456c-8fe7-da4111a173b4" />

Kuva 4. verkon `wifi-mobile` korkattu avain.

`Aircrack-ng` sai verkon avaimen selville erittäin nopeasti.

#### Miten hyökkäys toimi?

## b) Kirjoita raportti siitä mitä opit ja mitkä asia yllättivät sinut kun tutustuit harjoitukseen.

## c) Miten suhtautumisesi WLanin turvallisuuteen muuttui sen jälkeen kun teit harjoitukset?

## Lähteet 

WiFiChallenge lab s.a.a Luettu 26.11.2026 Luettavissa: https://lab.wifichallenge.com/

Iso-Anttila, L 2026. Haaga-Helia ammattikorkeakoulu. Verkkoon tunkeutuminen ja tiedustelu. Luento, moodlen luentomateriaalit. Luettu 24.11.2026.

Karvinen, T 2026. Verkkoon tunkeutuminen ja tiedustelu. Luettu 24.11.2026. Luettavissa: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/

Calvo Laorden, R 2024. Walkthrough WiFiChallenge Lab v2.0. Luettu 27.11.2026. Luettavissa: https://r4ulcl.com/posts/walkthrough-wifichallenge-lab-2.0/

aircrack-ng Main documentation s.a. Luettu 27.11.2026. Luettavissa: https://www.aircrack-ng.org/documentation.html

WiFiChallenge lab README s.a.b. Luettu 27.11.2026. Luettavissa: https://lab.wifichallenge.com/README

Wikipedia 2026. Wired Equivalent Privacy. Luettu 2026. Luettavissa: https://en.wikipedia.org/wiki/Wired_Equivalent_Privacy
