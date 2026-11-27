# h6 wifi


## a) Tutustu wifi challenge lab 2.1 harjoitus ympäristöön ja käytä tarvittaessa hyväksesi jo olemassa olevia ohjeita.

Aloitin harjoitukseen tutustumisen lukemalla moodlen luentomateriaalit ja WifiChallenge Labin sivut (Wifi lab challenge s.a.a). WifiChallenge labin harjoitusympäristö on `Dockerilla` ja `mac80211_hwsim ` softaradiosimulaattorilla luotu virtuaaliympäristö, joka pyrkii simuloimaan yritystason langattomia verkkokokonaisuuksia ja luo täten turvallisen ympäristön WiFi-hyökkäyksien harjoitteluun virtuaalikoeen sisällä. Ympäristö sisältää 17 langatonta verkkoa sisältäen eri salausmenetelmiä, kuten `OWE, WEP, WPA/WPA2-PSK ja WPA3-SAE ` (WifiChallengelab s.a.b.). Ympäristön verkot toimivat taajuuksilla `2,4 , 5 ja 6 GHz` (WiFiChallenge lab s.a.b.).

Latasin harjoitusympäristön kurssin moodle-sivuilta ja liitin ympäristön VMwareen. Ympäristöä ei tarvinnut konfiguroida, se oli valmis sellaisenaan.

Tämän jälkeen tutustuin ympäristöön avaamalla harjoitusympäristön ja tutustumalla netistä löytyvään dodumentaatioon `aircrack-ng` työkaluista (aircrack-ng Main documentation s.a.). `Aircrack-ng suite` oli itselleni ennestään tuntematon, joten siihen tutustuminen vei hetken aikaa. Katsoin myöskin WifiChallenge labin sivuilta löytämästäni walkthrough-osiosta (Calvo Laorden 2024) muutaman esimerkin ensimmäisistä haasteista, minkä jälkeen toistin ratkaisut. Koin tämän helpottavan työkalujen kanssa alkuunpääsyä, varsinkin syntaxin osalta.

## b) Kirjoita raportti siitä mitä opit ja mitkä asia yllättivät sinut kun tutustuit harjoitukseen.

## c) Miten suhtautumisesi WLanin turvallisuuteen muuttui sen jälkeen kun teit harjoitukset?

## Lähteet 

WiFiChallenge lab s.a.a Luettu 26.11.2026 Luettavissa: https://lab.wifichallenge.com/

Iso-Anttila, L 2026. Haaga-Helia ammattikorkeakoulu. Verkkoon tunkeutuminen ja tiedustelu. Luento, moodlen luentomateriaalit. Luettu 24.11.2026.

Karvinen, T 2026. Verkkoon tunkeutuminen ja tiedustelu. Luettu 24.11.2026. Luettavissa: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/

Calvo Laorden, R 2024. Walkthrough WiFiChallenge Lab v2.0. Luettu 27.11.2026. Luettavissa: https://r4ulcl.com/posts/walkthrough-wifichallenge-lab-2.0/

aircrack-ng Main documentation s.a. Luettu 27.11.2026. Luettavissa: https://www.aircrack-ng.org/documentation.html

WiFiChallenge lab README s.a.b. Luettu 27.11.2026. Luettavissa: https://lab.wifichallenge.com/README
