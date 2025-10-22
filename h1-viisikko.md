# h1 Viisikko

[Palvelinten hallinta 2025 loppusyksy](https://terokarvinen.com/palvelinten-hallinta/) -kurssin ensimmäisessä tehtävässä tutustuin keskitetyn hallinnan periaatteisiin: __idempotenssi, infrastruktuuri koodina, yksi totuus__. Asensin uuden virtuaali-Linuxin VirtualBoxiin ja siihen ohjelmiston konfiguraationhallintatyökalun Saltin. Harjoittelin Saltin käyttöä paikallisesti. Kertasin myös komentorivityöskentelyä Linuxissa sekä lueskelin infrastruktuurista koodina.

Tein tehtävää tiistaina 21.10 klo 12-16 sekä keskiviikkona 22.10 klo 13.15-19.

## Laitteisto

### Host

Käytössäni on Windows 11 Home -käyttöjärjestelmällä varustettu jokusen vuoden vanha HP:n kannettava kone, jonka tiedot ovat seuraavat: 

- HP:n kannettava Envy x360
- 64-bittinen käyttöjärjestelmä: Windows 11 Home Edition
- CPU ja näytönohjain: AMD Ryzen 7 3700U with Radeon Vega Mobile Gfx 2.30 GHz (4 ydintä, 8 säiettä)
- RAM: 16,0 GB (13,9 GB käytettävissä)
- Levytila: 475 GB SSD, vapaana 187 GB

### Guest

- Virtualisointiympäristönä Oracle VM VirtualBox 7.2.4
- Virtuaalikoneena Debian GNU/Linux 13.1.0 (”Trixie”)
- CPU 4 ydintä
- RAM: 4 GB
- Levytila: 60 GB

## x) Lue ja tiivistä
 
__Karvinen 2025: [Install Salt on Debian 13 Trixie]( https://terokarvinen.com/install-salt-on-debian-13-trixie/)__

Salt on konfiguraationhallintatyökalu – infraa koodina. Jotta se voidaan asentaa Linuxiin, täytyy luoda apt-paketinhallintatyökalulle uusi repositorio (varasto). Repo koostuu kahdesta tiedostosta, jotka ladataan Salt Projectilta: PGP -julkinen avain sekä sources.list. Jälkimmäisen tiedoston sisältönä on URL-osoitteita joissa sijaitsevat projektin binäärikoodit. Kun repo on luotu, voidaan asentaa Salt (salt-minion, salt-master).

`$ sudo apt-get update`

`$ sudo apt-get install salt-minion salt-master`

__Karvinen 2023: [Run Salt Command Locally]( https://terokarvinen.com/2021/salt-run-command-locally/)__

Salt-komentoja on kätevää testata paikallisesti. Tärkeimpiä tilafunktioita (state function) on viisi: __pkg, file, service, user ja cmd__.  

`$ sudo salt-call --local -l info state.single`+ komento 

$ sudo salt-call --local -l info state.single 

- ... pkg.installed ”ohjelma” – tarkistaa onko ohjelma (paketti) asennettu (jos ei, asentaa sen)
- ... pkg.removed ”ohjelma” – poistaa ohjelman
- ... file.managed /”polku”/”tiedosto” – muokkaa tiedostoa (tai luo sen, jos sitä ei ole)
- ... file.absent /”polku”/”tiedosto” – poistaa tiedoston
- ... service.running ”demoni” enable=True – käynnistää demonin (mutta ei asenna)
- ... service.dead ”demoni” enable=False – sulkee demonin
- ... user.present ”user” – tarkistaa onko käyttäjä olemassa (jos ei, lisää käyttäjän)
- ... user.absent ”user” – poistaa käyttäjän
- ... cmd.run ”komento” – ajaa komennon

__Karvinen 2018: [Salt Quickstart – Salt Stack Master and Slave on Ubuntu Linux]( https://terokarvinen.com/2018/03/28/salt-quickstart-salt-stack-master-and-slave-on-ubuntu-linux/)__

Saltin avulla yhdellä isäntäkoneella voidaan hallita lukemattomia orjakoneita. Orjat voivat olla näkymättömissä (palomuuri, NAT), vain isäntäkone tarvitsee julkisen palvelimen ja tunnetun IP-osoitteen.

Isäntäkoneen palomuuriin on iskettävä reiät portteihin 4505/tcp ja 4506/tcp. Orja laitetaan ottamaan yhteyttä isäntäkoneeseen komennolla `slave$ sudo systemctl restart salt-minion.service`eli käynnistämällä minion-demoni. Tämän jälkeen orjaa voidaan käskeä isäntäkoneesta käsin, esim. `master$ sudo salt '*' cmd.run 'hostname -I'`.

__Karvinen 2006: [Raportin kirjoittaminen]( https://terokarvinen.com/2006/06/04/raportin-kirjoittaminen-4/)__

Onnistunut raportti on täsmällinen, toistettava, helppolukuinen ja lähteistetty. Sepittäminen ja plagiointi eivät ole sallittua.

## a) Debian 13 Trixie

_ Asenna Debian 13 Trixie virtuaalikoneeseen._

Asensin Debian 13 Trixien VirtualBoxiin Karvisen [ohjeilla]( https://terokarvinen.com/2021/install-debian-on-virtualbox/). Jouduin tekemään asennuksen kahteen kertaan: VirtualBoxissa oli muistiin liittyvä ongelma, joka johti uuden koneen kaatumiseen toistuvasti käynnistettäessä. Ongelma ratkesi poistamalla VirtualBox kokonaan ja lataamalla sen uusin versio puhtaalta pöydältä.

## b) Salt (salt-minion) Linuxille

_Asenna Salt (salt-minion) Linuxille (uuteen virtuaalikoneeseesi)._

Asensin Saltin Linuxille [Karvisen ohjeella](https://terokarvinen.com/install-salt-on-debian-13-trixie/).

 <img width="665" height="314" alt="image" src="https://github.com/user-attachments/assets/5be6d41f-1e3c-4b45-a73f-adc96d233a36" />

<img width="470" height="73" alt="image" src="https://github.com/user-attachments/assets/4e19e0ff-6601-4c20-a3f1-3627ff438c0b" />

Salt tuli asennettua onnistuneesti. 

<img width="268" height="64" alt="image" src="https://github.com/user-attachments/assets/4a18f69f-648e-4dc2-b863-e5dd8c6fe379" />

Tein palomuuriin valmiiksi reiät publisher- ja returner-moduuleita varten (edelliseen portin kautta tullaan lähettämään käskyjä orjille, jälkimmäisen kautta orjat tulevat vastaamaan).

## c) Viisi tärkeintä 

_Näytä Linuxissa esimerkit viidestä tärkeimmästä Saltin tilafunktiosta: __pkg, file, service, user, cmd__. Analysoi ja selitä tulokset._

### pkg.installed/removed

Pkg-komennot liittyvät paketinhallintaan eli niiden avulla asennetaan ja poistetaan ohjelmistoja.

`sudo salt-call --local -l info state.single pkg.installed tree`-komento tarkistaa, onko tree-ohjelma asennettu ja asentaa sen tarvittaessa.

<img width="665" height="623" alt="image" src="https://github.com/user-attachments/assets/8d671248-9b98-40ee-92c5-0d21fd11286b" />

Tiloja 1 (muutoksia 1), eli tree-ohjelma tuli asennettua.

<img width="257" height="256" alt="image" src="https://github.com/user-attachments/assets/86b58633-1f62-4e74-ab1e-453e18fb99d2" />
 
Tree-ohjelma listaa kansiot ja tiedostot puumuodossa.

### file.managed/absent

File.managed ja file.absent-tilafunktioilla luodaan (tai muokataan) sekä poistetaan tiedostoja.

`sudo salt-call --local -l info state.single file.managed /tmp/testi`luo tiedoston.

<img width="665" height="271" alt="image" src="https://github.com/user-attachments/assets/33dec270-47cb-4b06-a456-7d2c0deb8558" />

Uusia ja muuttuneita tiloja on 1.

`sudo salt-call --local -l info state.single file.managed /tmp/testi contents=”testitesti”` luo tiedostoon sisältöä. 

<img width="665" height="391" alt="image" src="https://github.com/user-attachments/assets/fb310b70-5d12-451a-a1cb-586bdfacb316" />
 
<img width="342" height="106" alt="image" src="https://github.com/user-attachments/assets/9407c052-2d3c-4a8d-9ab0-82ae799cb805" />

`sudo salt-call --local -l info state.single file.absent /tmp/testi`poistaa tiedoston.

<img width="665" height="467" alt="image" src="https://github.com/user-attachments/assets/1cc65e20-531b-457b-8634-f7d81d59c001" />

### service.running/dead

Service.running ja service.dead -tilafunktioilla hallitaan demoneja.

`sudo salt-call --local -l info state.single service.running apache2 enable=True`käynnistää Apache2-palvelinhallintaohjelmiston jos se on asennettuna.

`sudo salt-call --local -l info state.single service.dead apache2 enable=False` sulkee demonin.

<img width="665" height="325" alt="image" src="https://github.com/user-attachments/assets/0a706336-2728-4832-a523-8e1ce4989747" />

En ollut vielä asentanut Apachea uuteen virtuaali-Linuxiini, joten sitä ei ollut mahdollista käynnistää. Komento asennukselle olisi `sudo salt-call --local -l info state.single pkg.installed apache2`.

### user.present/absent

User.present ja user.absent -tilafunktioilla hallitaan käyttäjiä.

`sudo salt-call --local -l info state.single user.present nlholllm`-komennolla tarkistetaan, onko käyttäjä ’nlholllm’ olemassa. Jos ei ole, käyttäjä luodaan.

<img width="665" height="350" alt="image" src="https://github.com/user-attachments/assets/591da19f-c249-4daf-b37a-dbf07c5bba38" />
 
<img width="345" height="182" alt="image" src="https://github.com/user-attachments/assets/753061e3-50b3-48b6-b3f4-88c58da14bb2" />

`cat /etc/passwd`-komennolla voidaan käydä kurkkaamassa käyttäjää: 

<img width="612" height="67" alt="image" src="https://github.com/user-attachments/assets/5968c714-8d86-4106-8043-009b2b491af2" />

Pohdin, olisiko salasana mahdollista asettaa käyttäjän luomisen yhteydessä. Todennäköisesti (tai joka tapauksessa erikseen).

`sudo salt-call --local -l info state.single user.absent nlholllm `-komennolla poistetaan käyttäjä ‘nlholllm’.

<img width="665" height="415" alt="image" src="https://github.com/user-attachments/assets/c51d58cf-11f7-493b-9f23-797b21c0525b" />
 
### cmd.run

Cmd.run-tilafunktiolla ajetaan komentoja.

`sudo salt-call --local -l info state.single cmd.run 'touch /tmp/testi' creates="/tmp/testi"`: “kosketetaan” tiedostoa ‘testi’ komennolla ’touch’. Cmd-tilafunktio varmistaa, että tiedosto on olemassa, ja jos ei ole, se luodaan. Touch muuttaa tiedoston aikaleimaa muuttamatta muuten itse tiedostoa. [Karvisen]( https://terokarvinen.com/2021/salt-run-command-locally/) mukaan on usein tarkoituksenmukaisempaa käyttää funktiota file, service tai user kuin cmd.

 <img width="665" height="353" alt="image" src="https://github.com/user-attachments/assets/a847a204-4910-443b-bd01-06defd76e0ee" />

## d) Idempotentti

_Anna esimerkki idempotenssista. Aja 'salt-call --local' -komentoja, analysoi tulokset, selitä miten idempotenssi ilmenee._

Idempotenssi liittyy tavoitetilan kuvaukseen: ”Näiden demonien tulee olla asennettuna, näiden tiedostojen löytyä tästä lokaatiosta.” Kun tavoitetila on saavutettu, ei ole tarvetta muutoksille, mikä Saltin kohdalla tarkoittaa sitä, että tilafunktion suorittamisen jälkeen ’succeeded’-arvo on 1, mutta ’changed’-arvoa ei ole, eli se on 0. Näin käy, vaikka funktio suoritettaisiin kerta toisensa jälkeen.

<img width="665" height="264" alt="image" src="https://github.com/user-attachments/assets/7db9a76e-f4f3-4649-8059-9fcc131b446c" />
 
Ajoin `sudo salt-call --local -l info state.single cmd.run 'touch /tmp/testi' creates="/tmp/testi"` -komennon uudelleen. Changed-arvoa ei nyt ole, eli tila on idempotentti; tiedosto on jo olemassa.

## Lähteet

Karvinen 2025. Install Debian on Virtualbox - Updated 2024. https://terokarvinen.com/2021/install-debian-on-virtualbox/

Karvin en 2025. Install Salt on Debian 13 Trixie. https://terokarvinen.com/install-salt-on-debian-13-trixie/

Karvinen 2025. Palvelinten Hallinta. https://terokarvinen.com/palvelinten-hallinta/

Karvinen 2006. Raportin kirjoittaminen. https://terokarvinen.com/2006/06/04/raportin-kirjoittaminen-4/ 

Karvinen 2023. Run Salt Command Locally. https://terokarvinen.com/2021/salt-run-command-locally/

Karvinen 2018. Salt Quickstart – Salt Stack Master and Slave on Ubuntu Linux. https://terokarvinen.com/2018/03/28/salt-quickstart-salt-stack-master-and-slave-on-ubuntu-linux/
