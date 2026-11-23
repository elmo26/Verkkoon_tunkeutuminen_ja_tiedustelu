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

`Node: h3> iptables -t nat -A PREROUTING -p tcp --dport 22 -j REDIRECT --to-` : Ohjaa h3 kautta kulkevan ssh-liikenteen omaan porttiinsa 22.

`port 22` : Ohjaa koneen läpi menevän, muaalle tarkoitetun ssh-liikenteen omaan porttiinsa 22.

`Node: h3> tcpdump –i h3-eth0 –w arp-p.pcap &` : Tallentaa kortin `h3-ethO` verkkoliikenteen `arp.p.pcap`-tiedostoon.

`Node: h3> wireshark &` : Käynnistää wiresharkin taustalle.

`Node: h3> sshesame -port 22 -listen_address 0.0.0.0` : Käynnistää kuuntelijan taustalle.

`Node: h3> python3 ./arp_poison.py ` : Käynnistää arp-poisoning hyökkäyksen.



## b) Samassa hakemistossa on myös ICMP Spoofing ja TCP Session Hijacking. Aja molemmat labrat läpi ja kerro, miten molemmat tekniikat toimivat.

## c) Hakemistossa 02-SDN-DDos_Simulation tryout-kansiossa on työkalut, jotta voit ajaa TCP SYN-Flood-hyökkäyksen turvallisesti. Kirjoita, miten ajoit hyökkäyksen ja miten kyseinen hyökkäys toimii.

## Lähteet

Iso-Anttila L, 2026. Tietoverkkoihin tunkeutuminen ja tiedustelu luento Haaga-Helia ammattikorkeakoulu 17.11.2026

Iso-Anttila L, 2026. Tietoverkkoihin tunkeutuminen ja tiedustelu Mininet luento materiaalit ja ohjeet Haaga-Helia ammattikorkeakoulu. Luettavissa: https://hhmoodle.haaga-helia.fi/course/view.php?id=49869&section=1#tabs-tree-start Luettu 23.11.2026 

ssam246 2024. Network security lab Github. Luettavissa: https://github.com/ssam246/Network-Security-Lab Luettu 23.11.2026
