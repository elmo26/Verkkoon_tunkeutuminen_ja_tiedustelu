# Labratakki

Aloitin pistämällä moodlesta löytyvän Mininet-ympäristön pystyyn VMware:n päälle ja latasin MobaXtermin, jotta saan virtuaalikoneiden pääteikkunat näkyviin. Pistin myös VMware:n network-adapterin host-only tilaan, jotta ympäristö olisi mahdollisimman eristetty. Host koneena toimi Windows 11 versio 26H2. 

## a) Aja tunnilla esitetty ARP hyökkäys ja tutki, miten se toimii.

Aloitin seuraamalla ohjeita ja pistin mininet-koneen päälle ja katsoin sen ip:n ssh-yhteyttä varten. Näkyy kuvassa 1.

<img width="794" height="623" alt="kuva" src="https://github.com/user-attachments/assets/c04a7739-4bb8-4a1e-aa86-e17d6c0eaa4a" />

Kuva 1. Mininet-virtuaalikone käynnissä

Tämän jälkeen seurasin ohjeita ja otin ssh-yhteyden mininet-koneelle sekä aloitin arp-hyökkäyksen ajon valmistelun.

Ajoin ensin `./get_xauth.sh` scriptin ja sitten `sudo -s xauth add mininet-vm/unix:10 MIT-MAGIC-COOKIE-1 "cookie"`, sillä oli hieman ongelmia xtermin kanssa, ja tämä oli ohjeissa ratkaisuna ongelmille. Tämä näkyy kuvassa 2.

Ajoin komennon `ryu-manager ryu.app.simple_switch_13`, mikä käynnistää Ryu SDN -kontrollerin. Sitten ajoin komennon `sudo mn --topo single,3 --mac --switch ovsk --controller remote`, mikä luo mininet-verkon sisältäen virtuaalikoneet `h1,h2,h3`, mac-osoitteet `--mac` ja yhteyden kontrolleriin `--controller remote`.

Ajoin myös komennot `pingall` ja `xterm h1 h2 h3`: ensin pingall täyttää arp-taulut kun koneet pingaavat toisiaan, ja xterm-komento avaa kaikille koneille terminaali-ikkunat. Nämä komennot näkyvät alla olevissa kuvissa 1. ja 2.

<img width="681" height="864" alt="kuva" src="https://github.com/user-attachments/assets/e9e1320f-44aa-4082-9937-642cca43c1d7" />

Kuva 2.

<img width="534" height="267" alt="kuva" src="https://github.com/user-attachments/assets/6457c5d4-7bb5-40a3-896c-0a3a01347eae" />

Kuva 3.

Tämän jälkeen jatkoin ohjeiden seuraamista ja ajoin seuraavat komennot: 

`Node: h1 > arp -n` : Näyttää tallennetut arp-taulut eli koneiden h2 ja h3 mac-osoitteet.

`Node: h3 > sysctl -w net.ipv4.ip_forward=1`: pistää h3-koneen forwardaamaan ip-paketit.

`Node: h3> iptables -t nat -A PREROUTING -p tcp --dport 22 -j REDIRECT --to-port 22` : Ohjaa h3 kautta kulkevan ssh-liikenteen omaan porttiinsa 22.

`Node: h3> tcpdump –i h3-eth0 –w arp-p.pcap &` : Tallentaa kortin `h3-ethO` verkkoliikenteen `arp.p.pcap`-tiedostoon.

`Node: h3> wireshark &` : Käynnistää wiresharkin taustalle.

`Node: h3> sshesame -port 22 -listen_address 0.0.0.0` : Käynnistää kuuntelijan taustalle.

`Node: h3> python3 ./arp_poison.py ` : Käynnistää arp-poisoning hyökkäyksen. Käynnistin tälle toisen xterm-session h3-koneelle, jotta saan listenerin ja arp-poisoning-hyökkäyksen ajettua samaan aikaan.

`Node: h1> ssh h2` : h1 yrittää ottaa yhteyden h2-koneeseen ssh:lla

`Node: h1 : WARNING REMOTE HOST IDENTIFICATION HAS CHANGED` : h1 varoittaa ssh-yhteyden sormenjäljen vaihtumisesta, koska h2 suunnattu ssh-liikenne meneekin oikeasti h3-koneelle, eikä ssh-avainpari ole sama.

`Node: h1> ssh-keygen –R h2  poistetaan vanha avain`: poistetaan vanha avain, jotta saadaan liikenne läpi.

`Node: h1> ssh h2 -> Are you sure you want to continue` : Ohitetaan varoitus tuntemattomasta hostista, annetaan salasana "salasana" -> ollaan sisällä.

<img width="1685" height="1011" alt="kuva" src="https://github.com/user-attachments/assets/e476d771-7325-4e61-b6c6-cb2bea60b751" />
Kuva 4. ARP-poisoning

Kuvassa näkyy h3-logeissa `SSH connection established`, eli yritimme ottaa ssh-yhteyttä `h1->h2` ja päädyimme koneelle `h3`. Kuten kuvassa näkyy, ssh-yhteyttä otettaessa h1 varoittaa että ip osoitteen `10.0.0.2`  julkinen ssh-avain eroaa tunnetusta `h2` koneen julkisesta avaimesta. Tämä johtuu siitä, että arp-hyökkäystä suorittava `h3` esittää olevansa `h2`, minkä ip-osoite on `10.0.0.2`.



### Miten hyökkäys toimi

ARP (Adress resolution protocol) on tilaton ja siksi ns MITM eli man in the middle hyökkäys on mahdollinen (mitre 2025). Tämä siis tarkoittaa sitä, että välissä oleva laite voi esittää olevansa joku muu laite kuin oikeasti on, vastaamalla `h1-koneen` arp-kyselyihin olevansa h2 ja `h2-koneen` arp kyselyihin olevansa h1. Tällä periaatteella `h3` kykeni esittämään koneelle `h1` olevansa kone `h2`, ja aikaisemmin ajettu komento `iptables -t nat -A PREROUTING -p tcp --dport 22 -j REDIRECT --to-port 22` ohjasi kaiken `h3` läpi menevän ssh-liikenteen omaan porttiinsa 22.

## b) Samassa hakemistossa on myös ICMP Spoofing ja TCP Session Hijacking. Aja molemmat labrat läpi ja kerro, miten molemmat tekniikat toimivat.

### ICMP Spoofing

Minulla oli edellisestä tehtävästä koneet `h1,h2 ja h3` päällä, joten ympäristö oli luullakseni valmis. Katsoin ohjeet tiedostosta README.md, joissa kerrottiin scriptit `sniff_icmp.py` ja `spoof_icmp.py` sekä se, että yhdellä koneella h2 pingata `10.0.0.1` (h1 kone). Scriptit löytyivät polusta `home/mininet/lab/01-Network-Security-Lab/scripts`. Ohjeet olivat aikalailla epäselvät: niissä mainittiin molemmat scriptit mutta vain toinen mainittiin toteutusvaiheessa.

Ajoin seuraavat komennot (ajoin molemmat scriptit h1 demonstroidakseni hyökkäyksen, mikä tietysti ei ole kovin fiksua hyökkäysnäkökulmasta.

`h1: python3 ./sniff_icmp.py`

`h1: python3 ./spoof_icmp.py`

`h2: ping 10.0.0.1`

<img width="1698" height="523" alt="kuva" src="https://github.com/user-attachments/assets/0b392f6d-1342-49f5-a12e-4e1a9c1f66b6" />

Kuva 5. ICMP-spoofing
Lopputuloksena sain scriptit ja pingauksen menemään läpi, kuvassa näkyy duplikaatti vastaukset.


#### Miten hyökkäys toimii

ICMP-protokollaa käytetään mm. verkkoyhteyksien testaamiseen pingaamalla. Varsinaista autentikointia ei ole, joten paketin oikeaa lähettäjää on vaikea todentaa. Toteuttamani hyökkäyksen tapauksessa se teoriassa onnistui, sillä vastaukset tuli tuplana. Toki ajoin sekä sniffing että spoofing scriptit samalla koneella, sillä en saanut sitä muuten toimimaan, joten varsinaisessa hyökkäystarkoituksessa ajologiikka on hyödytön.

Demonstraatiomielessä logiikka on kuitenkin pitävä, kun vain ajaisi spoofing-scriptin toisella koneella. Jälkikäteen mietittynä ympäristö olisi ilmeisesti pitänyt käynnistää hub_topo.py topologialla ohjeiden perusteella, jollon hyökkäyksen olisi voinut toteuttaa fiksusti.

### TCP Session Hijacking

Tämän aloitin ajamalla oikean topologian `sudo python hub_topo.py`, joka olisi pitänyt ajaa myös edellisessä tehtävässä, ja seurasin `README.md` ohjeita. Huomasin ongelmia ohjeiden mukaan injektoinnissa kun h3-koneella käytti kahta erillistä skriptiä. Samasta kansiosta löytyi kuitenkin myös `tcp_sniff_and_hijack.py` scripti ja kokeilin sitä.

`h1: python tcp_server.py` : Tämä käynnistää TCP-palvelimen, joka kuuntelee liikennettä portista `12345`.

`h3: python tcp_sniff_and_hijack.py` : Käynnistää skriptin joka tarkkailee liikennettä ja väärentää sen, minkä jälkeen lähettää eteenpäin.

`h2: python tcp_client.py` : Asiakaskone, joka lähettää TCP-yhteyden yli palvelimen osoitteeseen `10.0.0.1:12345` (kuuntelava palvelin) viestin `Hello from host 2` ja vastaanottaa viestin takaisin: `Hello from host 1`.


 Kuvasta näkee, että hyökkäys onnistui, kone h1: `"Hello from host 1, You are hacked!"`

<img width="1743" height="674" alt="kuva" src="https://github.com/user-attachments/assets/00cad1ed-98d4-4c46-85d6-fbccede08a07" />

Kuva 6. Onnistunut toteutus. 

#### Miten hyökkäys toimii

h3-kone näkee ja haistelee salaamatonta tcp-liikennettä h1 ja h2 koneiden välillä, luo väärennetyn tcp-paketin samoilla ACK ja SYN arvoilla ja lähettää itse luomansa version paketista koneelle h1, minkä seurauksena teksti "Hello from host 1, You are hacked!" printtaantuu koneen 1 terminaaliin.

## c) Hakemistossa 02-SDN-DDos_Simulation tryout-kansiossa on työkalut, jotta voit ajaa TCP SYN-Flood-hyökkäyksen turvallisesti. Kirjoita, miten ajoit hyökkäyksen ja miten kyseinen hyökkäys toimii.

Ennen tehtävän suoritusta putsasin ympäristön mininetin uudelleenkäynnistyksellä, jotta vanhat topologiat/konfiguraatiot eivät häiritse taustalla.

Toteutin TCP SYN-Flood-hyökkäyksen `02-SDN-DDos_simulation` githubista löytyvän avoimen projektin (santhisenan) ohjeiden avulla. Aloitin katsomalla `README.md` tiedoston githubista, jossa oli ohjeet tehtävän suoritusta varten. Törmäsin

Ensimmäisenä käynnistin ajon kohdalla tuli valitus että "Unable to contact remote controller at 127.0.0.1", joten ajoin `sudo ryu-manager simple_switch_13.py &` saadakseni kontrollerin päälle. Tämän jälkeen avasin uuden ikkunan ja pistin topologian tree_topology.py:n komennolla `sudo python tree_topology.py`. En saanut wiresharkkia fiksusti toimimaan graafisesti, joten kaappasin liikennettä käyttäen komentoa ` sudo tcpdump -i s4-eth3 -w syn_flood.pcap` kytkimen s4 eth3 kortista, ja avasin kaappauksen jälkikäteen wiresharkilla.

<img width="1627" height="349" alt="kuva" src="https://github.com/user-attachments/assets/a3d41d8f-1d6a-4315-b394-b2500c380f80" />

kuva 7. Hyökkäyksen ajo ja kaapattu liikenne.

Kuvassa seitsemän näkyy hyökkäyksen eri vaiheet (Episode), ja mitkä laitteet kulloinkin hyökkäävät mihinkin.

<img width="1064" height="394" alt="kuva" src="https://github.com/user-attachments/assets/4c43641e-e7f3-4737-ac52-e1f008ea34d7" />

Kuva 8. kaappauksessa ilmaantunutta liikennettä.

#### Miten hyökkäys toimii

Tässä on kysessä ns DDos eli denial of service tai palvelunestohyökäys. Kuten jälkimmäisessä kuvassa numero kahdeksan näkyy, hyökkäävä laite (10.0.0.1) lähettää ainoastaan ensimmäisen `SYN` viestin monesta eri lähdeportista, joka pakottaa vastaaottavan laitteen (10.0.0.8) allokoimaan resursseja avattaville TCP-yhteyksille (Wikipedia). TCP:n kolmivaiheinen kättely ei kuitenkaan ikinä mene läpi, koska hyökkäävä kone ei lähetä viimeistä `ACK` viestiä. Tästä syystä kohdekoneen resurssit voivat hyökkäyksen johdosta mennä täyteen ja tällöin kone tukkiutuu. Tällaisessa tapauksessa esimerkiksi oikeat käyttäjät eivät välttämättä saa liikennettä palveluun läpi, sillä resurssit ovat ylikuormittuneet.

## Lähteet

Iso-Anttila L, 2026. Tietoverkkoihin tunkeutuminen ja tiedustelu luento Haaga-Helia ammattikorkeakoulu 17.11.2026

Iso-Anttila L, 2026. Tietoverkkoihin tunkeutuminen ja tiedustelu Mininet luento materiaalit ja ohjeet Haaga-Helia ammattikorkeakoulu. Luettavissa: https://hhmoodle.haaga-helia.fi/course/view.php?id=49869&section=1#tabs-tree-start Luettu 23.11.2026 

ssam246 2024. Network security lab Github. Luettavissa: https://github.com/ssam246/Network-Security-Lab Luettu 23.11.2026

mitre 2025. Adversary-in-the-Middle: ARP Cache Poisoning Luettu 23.11.2026. Luettavissa: https://attack.mitre.org/techniques/T1557/002/

Santhisenan 2020. DDoS Simulation in a Software Defined Network Luettu 23.11.2026 Luettavissa: https://github.com/santhisenan/SDN_DDoS_Simulation/blob/master/README.md

Wikipedia 2026. SYN flood. Luettu 23.11.2026 Luettavissa: https://en.wikipedia.org/wiki/SYN_flood
