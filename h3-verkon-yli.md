# Verkon yli

[Palvelinten hallinta 2025 loppusyksy](https://terokarvinen.com/palvelinten-hallinta/) -kurssin kolmannessa tehtävässä opettelin käyttämään Saltia verkon yli samaan yksityiseen virtuaaliverkkoon asennettujen master- ja slave-koneiden välillä. Tein uudet koneet Vagrant-työkalulla.

Tein tehtävää keskiviikkona 5.11.2025 klo 13:45-22.00 sekä torstaina 6.11.2025 klo 13:30-17:30. Aloitin lueskelemalla n. kolmen tunnin ajan Vagrantista sekä sen asennuksesta Windows-ympäristöön. Apuna oli erityisesti Vagrantin [Get Started -opas](https://developer.hashicorp.com/vagrant/tutorials/get-started). Hyödynsin myös Ryan Yagerin [artikkelia]( https://overgrowncarrot1.medium.com/how-to-run-vagrant-on-windows-1f85392f96d3) aiheesta, samoin kuin aiempien kurssitoteutusten opiskelijoiden raportteja.

ChatGPT5 tiivistää Vagrantin seuraavasti:

Vagrant on työkalu, jonka avulla voidaan helposti luoda, hallita ja jakaa virtuaalisia kehitysympäristöjä.

Vagrant määrittelee kehitysympäristön projektikansion konfiguraatiotiedostossa (Vagrantfile), joka kertoo:
- mitä virtuaalikonetta käytetään (esim. Ubuntu)
- miten se konfiguroidaan (paketit, verkkoasetukset jne.)
- millä komennolla ympäristö käynnistetään

Ympäristö ajetaan yleensä taustalla jonkin virtualisointialustan kautta, kuten:
- VirtualBox
- VMware
- Hyper-V
- Docker

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

### Karvinen 2021: [Two Machine Virtual Network With Debian 11 Bullseye and Vagrant](https://terokarvinen.com/2021/two-machine-virtual-network-with-debian-11-bullseye-and-vagrant/)

(Huomaa: nykyinen Debian stable on 12-Bookworm, Vagrantissa "debian/bookworm64". Vanhentunutta 11-bullseye:ta ei enää käytetä.)

Karvisen artikkelissa kerrotaan tiiviisti, kuinka Vagrant asennetaan Linux-ympäristöön. Kurssillamme useimpien host-ympäristö on Windows, joten artikkelin hyöty jää ehkä hieman vajaaksi, mutta alleviivaa kyllä sitä, miten helppoa pakettien asennus on Linuxille vs. Windowsille.

Vagrant
- luo automaattisesti koneita virtualisointialustalle (kuten VirtualBox)
- automatisoi SSH-sisäänkirjautumisen
- toimii tekstikäyttöliittymässä.

Asenna Vagrant Debianissa/Linuxissa:
`$ sudo apt-get update`
`$ sudo apt-get install vagrant virtualbox`

Luo Vagrantfile projektikansioon ja täytä se Karvisen templatella:
`$ mkdir twohost/; cd twohost/`
`$ nano Vagrantfile`

(Oma huomio: Windows-koneelle luodaan kansio projektille, avataan PowerShell tai CommandPrompt pääkäyttäjän oikeuksin ja navigoidaan projektin kansioon. Siellä `vagrant init debian/bookworm64`(viimeisen osion voi korvata toisella [iso-kuvalla]( https://portal.cloud.hashicorp.com/vagrant/discover), mikä luo Vagrant-kansion ja alustaa koneen. Vagrantfilea voi myös muokata esim. Notepadilla tai VSCodella, vaikka käyttäen Karvisen templateja.)

Käynnistä uusi kone:
`vagrant up`

Kirjaudu uuteen koneeseen:
`$ vagrant ssh t001`

Ja ulos:
`vagrant@t001$ exit`

Tuhoa kone:
`$ vagrant destroy # all files are destroyed`

### Karvinen 2018: [Salt Quickstart – Salt Stack Master and Slave on Ubuntu Linux](https://terokarvinen.com/2018/salt-quickstart-salt-stack-master-and-slave-on-ubuntu-linux/) 

(Huomaa: Nykyisin ennen Saltin asentamista on asennettava ensin varasto (package repository), ohje h1 vinkeissä)

Karvisen artikkelissa kerrataan ohjeet Saltin asentamiseksi: asenna paketit, asenna Salt master ja Salt slave, aseta masterin ip-osoite (tai tuotantokoneen kohdalla domain-nimi) orjan /etc/salt/minion-tiedostoon, hyväksy orjan avain masterilla, testaa.

### Karvinen 2023: [Salt Vagrant - automatically provision one master and two slaves](https://terokarvinen.com/2023/salt-vagrant/)

(Vain kohdat
- Infra as Code - Your wishes as a text file
- top.sls - Which Slave Runs Which States)

Pidin tästä Karvisen artikkelista, se oli hieman pidempi ja perusteellisempi kuin mihin häneltä on totuttu. 

Kohdissa Infra as Code ja top.sls kerrataan edellisen viikon asiaa, josta kirjoitin varsin pitkällisesti [h2-tehtävässä]( https://github.com/nlholm/configuration-management-systems-course/blob/main/h2-infraa-koodina.md), joten en toista itseäni tässä.

Projektille/moduulille luodaan oma kansio masterin /srv/salt/-kansioon ja ko. kansioon (esim. ’hello’) luodaan init.sls-tiedosto. Ko tiedostoon kirjoitetaan halutut Salt-tilat YAML-notaatiolla. `sudo salt '*' state.apply hello`ajaa moduulin kaikissa verkon koneissa. 

Top-file kirjoitetaan masterin /srv/salt-kansion juureen. Siinä voidaan määritellä, mitä tiloja tai moduuleita ajetaan missäkin koneissa. `$ sudo salt '*' state.apply`ajaa kaikki määritellyt tilat kaikissa verkon koneissa.

## a) Hello Vagrant! 

_Osoita jollain komennolla, että Vagrant on asennettu (esim tulostaa Vagrantin versionumeron). Jos et ole vielä asentanut niitä, raportoi myös Vagrant ja VirtualBox asennukset._

Aloitin asennustyöt klo 18.00. 

Host-koneeni on Windows 11, eikä siinä ollut asennettuna Vagrantia. Aloitin asennuksen syventymällä Vagrantin asennusohjeisiin ([lyhyt versio]( https://developer.hashicorp.com/vagrant/tutorials/get-started), [pitkä versio]( https://developer.hashicorp.com/vagrant/docs/installation). Latasin asennuspaketin Windwosille, asensin sen ja käynnistin koneeni uudelleen. 

<img width="548" height="221" alt="image" src="https://github.com/user-attachments/assets/580abf13-b596-46cd-8dc8-16d7f742b547" />
 
<img width="771" height="603" alt="image" src="https://github.com/user-attachments/assets/222b60c7-7e87-49f9-90b8-f8cf8485af3a" />

<img width="753" height="829" alt="image" src="https://github.com/user-attachments/assets/c2f87d65-f8e4-4e70-a2d0-3b9afac21379" />
 
Tarkistin että Vagrant tuli lisättyä Windowsin ympäristömuuttujiin (ilmeisesti melko yleinen ongelma, josta keskustelua Stack Overflown [artikkelissa(https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows?newreg=446f3b22164548faa40ac0a7898e3575)).

<img width="1039" height="284" alt="image" src="https://github.com/user-attachments/assets/28f39e10-24a0-4c5d-858c-ece8b42897b4" />

Testasin että Vagrant löytyy.

## b) Linux Vagrant

_Tee Vagrantilla uusi Linux-virtuaalikone._

Loin host-koneelleni uuden kansion ’singlehost’, jonne perustin ensimmäisen Vagrant-projektini. Navigoin ko. kansioon komentotulkissa ja kokeilin alustaa uuden koneen komennolla `init debian/bookworm64`. Tätä ei varsinaisesti käsketty tekemään Karvisen [ohjeissa]( https://terokarvinen.com/2021/two-machine-virtual-network-with-debian-11-bullseye-and-vagrant/) , joissa sen sijaan luotiin tyhjä Vagrantfile ja täytettiin se Karvisen templatella, mutta komento oli käytössä kurssin aiemman toteutuksen oppilailla (esim. [veitim:illä]( https://github.com/veitim/palvelinten_hallinta/blob/main/h2_soitto_kotiin.md)), joten päätin kokeilla sitä. 

 <img width="1039" height="210" alt="image" src="https://github.com/user-attachments/assets/84508ae8-d209-4fe5-8cfc-41c7ff282c97" />

<img width="1039" height="527" alt="image" src="https://github.com/user-attachments/assets/38228899-5dba-4a94-9de1-5b9aaed0a758" />

Vagrantfile tuli luotua. Koodia on vain muutama rivi, muu sisältö on kommenttia.

<img width="904" height="1041" alt="image" src="https://github.com/user-attachments/assets/aae1afde-48b2-46e1-9b81-c9d870cfd27b" />

Vs Karvisen template on runsaampi. 

`vagrant up`ja pääsin kokeilemaan uuden virtuaalikoneen luomista. Luominen vei muutaman minuutin.

<img width="1039" height="673" alt="image" src="https://github.com/user-attachments/assets/33abbabd-d68c-41db-b5f8-02d25bd5b565" />

Kone löytyi VirtualBoxin listalta. En kuitenkaan päässyt kirjautumaan graafiseen käyttöliittymään (sitä ei ollut).

<img width="1039" height="248" alt="image" src="https://github.com/user-attachments/assets/d7944cde-5a45-4772-a608-107d4ea66dc4" />

SSH:lla kirjautuminen onnistui.

```bash
sudo apt update
sudo apt install xfce4 lightdm -y

sudo reboot

sudo passwd vagrant # jos ei ole oletuksena
```
<img width="1039" height="269" alt="image" src="https://github.com/user-attachments/assets/7820db2c-bf90-42fe-9f16-504236d92c17" />

Asensin graafisen käyttöliittymän mielenkiinnosta (tekoälyn ohjeilla).

<img width="754" height="264" alt="image" src="https://github.com/user-attachments/assets/7a1597df-93ad-4e63-b598-d9c110df3fb7" />
 
Vagrantin voisi myös käynnistää GUI-moodissa, mutta se ei yleensä ole tarkoituksenmukaista (lisää resurssien käyttöä).

 <img width="690" height="106" alt="image" src="https://github.com/user-attachments/assets/5219814c-c33b-490b-b9e6-ba7e0a483c45" />

`exit`. Poistuin koneelta Windowsin komentotulkissa ja suljin myös graafisen käyttöliittymän.

<img width="1039" height="122" alt="image" src="https://github.com/user-attachments/assets/5af74b96-136a-4901-9e8b-489291fca707" />

`vagrant destroy`. Tuhosin koneen. Vagrantfile jäi kuitenkin jäljelle, joten identtinen kone olisi mahdollista luoda muutamassa minuutissa.

## c) Kaksin kaunihimpi

_Tee kahden Linux-tietokoneen verkko Vagrantilla. Osoita, että koneet voivat pingata toisiaan._

## d) Herra-orja verkossa

_Demonstroi Salt herra-orja arkkitehtuurin toimintaa kahden Linux-koneen verkossa, jonka teit Vagrantilla. Asenna toiselle koneelle salt-master, toiselle salt-minion. Laita orjan /etc/salt/minion -tiedostoon masterin osoite. Hyväksy avain ja osoita, että herra voi komentaa orjakonetta._

Yhdistin kotitehtävän osiot c) ja d). Aloitin luomalla uuden projektikansion ’multiplehosts’ ja navigoimalla sinne PowerShellissä. C:\Users\niina\vagrant_vm_c\multiplehost

<img width="898" height="91" alt="image" src="https://github.com/user-attachments/assets/467dd8df-4839-417a-b238-c8e4400b6c5b" />

Todettuani että ‘File’-tyyppistä tiedostoa ei voi Windowsissa luoda suoraan, kokeilin ym tapaa (löytyi [Yagerin]( https://overgrowncarrot1.medium.com/how-to-run-vagrant-on-windows-1f85392f96d3) artikkelista).  Tyhjä Vagrantfile aukesi VSCodeen.

<img width="898" height="91" alt="image" src="https://github.com/user-attachments/assets/12f26831-c6cb-4918-b580-503465e78ca8" />

Täytin Vagrantfilen Karvisen [templatea]( https://terokarvinen.com/2023/salt-vagrant/) mukaillen. Huom. box on tärkeää muuttaa tuoreehkoksi Debianiksi, tässä ’debian/bookworm64’. 

Ajattelin, että pystyisin yhdistämään tehtävänannon kohdat c) ja d) melko helposti asentamalla Saltin jo kohdassa c) ja suoraan Vagrantfileen, mutta kuten pian nähdään, tein tehtävästä itselleni tarpeettoman haastavan. Olisi kannattanut aloittaa yksinkertaisella konfiguraatiolla ja laajentaa sitä vasta kun se varmasti toimii.

`vagrant up`ja koneiden pitäisi alustua. Saan kuitenkin virheilmoituksen, että Salt-minion ei käynnisty. Pohdin voisiko kyse olla vastaavasta ongelmasta kuin tunnilla, jossa palvelu piti pakottaa käyntiin.

<img width="634" height="25" alt="image" src="https://github.com/user-attachments/assets/df2bf138-d2f7-44a7-9ef7-b734a3289b89" />

Menin tutkimaan tilannetta orja-koneelle. `vagrant ssh slave001`

<img width="675" height="138" alt="image" src="https://github.com/user-attachments/assets/7434e996-4483-4ad5-8734-a62af582a955" />
 
<img width="821" height="84" alt="image" src="https://github.com/user-attachments/assets/16869137-e2ea-4f4d-87a2-a4cfc6b4efc5" />

Saltin paketit näyttävät olevan asentuneet, mutta itse ohjelma ei. Tarkemmin katsoen myös salt.sources ja keyring.pgp ovat tyhjiä.

<img width="1039" height="697" alt="image" src="https://github.com/user-attachments/assets/ba8dda5a-b96f-4e5f-8fb8-8ff48180b436" />

Kokeilin vaihtaa wgetin curliin, se näytti toimineen aiemmilla kursseilla. Kirjauduin ulos orja-koneelta exitillä ja ajoin perään uudet `vagrant provision`sekä `vagrant reload`.

Nyt sources.list.d/ asentui, mutta avain ei.

<img width="1039" height="773" alt="image" src="https://github.com/user-attachments/assets/5c6fbec2-0806-4e2b-a6be-376cb44a0f09" />
 
<img width="1039" height="734" alt="image" src="https://github.com/user-attachments/assets/ec04a736-8258-4645-8a32-6300cac83bb7" />

Kokeilin vielä muuttaa Vagrantfileen tekoälyn ehdottaman sisällön.

<img width="648" height="38" alt="image" src="https://github.com/user-attachments/assets/0840e70e-44d5-4f17-a507-51aebf327e64" />

Sain saman virheen, Saltia ei ole asennettu. Minion-konetta ei myöskään ollut asennettu. 

Tässä vaiheessa kello oli 22.00, ja päätin yrittää jatkaa koneiden asennusta seuraavana päivänä (ja asentaa Saltin käsin jos tarpeen).

Jatkoin tehtävää torstaina 6.11.2025 klo 12.30. Yön yli nukuttuani tajusin, että Vagantfilessa oli ainakin yksi olennainen puute: `sudo tee /etc/apt/keyrings/salt-archive-keyring.pgp`luo tiedoston polkuun vain, jos hakemisto on jo olemassa. Hakemisto täytyy siis luoda, jotta avaintiedosto saadaan asettumaan ja Salt asentumaan. Lisäsin Vagrantfileen `sudo mkdir -p /etc/apt/keyrings`.

<img width="1039" height="875" alt="image" src="https://github.com/user-attachments/assets/034b6ea1-9adc-48c5-9455-8bc86d2c2400" />
 
<img width="1039" height="905" alt="image" src="https://github.com/user-attachments/assets/60d2fd3a-42c1-42ae-82fb-3893089233fb" />

Päivitin Vagranfilen vielä kertaalleen (tekoälyn avustuksella). Olennainen muutos on riveillä 21 ja 52.

Tällä kertaa koneita alustettaessa ei tullut virheilmoituksia. 

<img width="815" height="300" alt="image" src="https://github.com/user-attachments/assets/7ef010b2-0c9e-41dc-841b-a2cfc4e5125c" />
 
Masterille oli asentunut Salt, mutta minion1:sen avain ei ollut odottamassa hyväksyntää.

<img width="746" height="281" alt="image" src="https://github.com/user-attachments/assets/4f0ebeec-91e5-42cd-8d8b-ea2154a44fa5" />
 
Minion1:sen pingaus kuitenkin onnistui masterilta, mikä oli varsinainen tehtävä kotitehtävän c-kohdassa.

<img width="778" height="285" alt="image" src="https://github.com/user-attachments/assets/d5adeb68-b83c-4392-91ee-9cdcc316854a" />

Kirjauduin ulos masterilta (´exit´) ja sisään minion1:selle. Pingaus onnistui myös toisin päin.

<img width="766" height="240" alt="image" src="https://github.com/user-attachments/assets/0e83c7eb-3735-44c4-b395-bf950aa42bc4" />
 
<img width="1039" height="370" alt="image" src="https://github.com/user-attachments/assets/db09805a-8ced-4c51-9b94-c482609c04b0" />

Salt-paketin asennustiedostosto olivat nyt kohdillaan.

<img width="928" height="159" alt="image" src="https://github.com/user-attachments/assets/d5ea2922-91b6-4690-a27c-f19fbbe9cf8f" />

Myös /etc/salt/minion-tiedosto oli asettunut.

<img width="1039" height="249" alt="image" src="https://github.com/user-attachments/assets/1f71ac37-6434-4344-ba7b-0d2d54d10815" />

`sudo systemctl restart salt-minion`jäi pyörimään vastaavasti kuin tunnilla. Lähti käyntiin stop-start-satus-restart-käskyillä.

<img width="533" height="321" alt="image" src="https://github.com/user-attachments/assets/86168a6e-1848-4490-a5e2-22b921f9b4b0" />

Minion1:sen avain oli viimein tarjolla. Hyväksyin sen masterilla.

<img width="581" height="104" alt="image" src="https://github.com/user-attachments/assets/e72b2668-58e6-4d47-87a9-383c5affd86d" />

Yhteys oli syntynyt koneiden välille Saltilla.

<img width="996" height="551" alt="image" src="https://github.com/user-attachments/assets/14efde3d-e1f7-4ec9-9112-00ed3be36ec8" />

Myös testitiedosto asettui kauniisti minion1:selle. Tällä kertaa käskynä ’*’ eli ”kaikki orjat”.

Päädyin lopulta vaikeuksien kautta voittoon yrityksessäni asentaa Salt suoraan Vagrantfilen kautta (poislukien minionin restart etc-tiedoston muokkauksen jälkeen), mutta jälkikäteen ajatellen olisi ollut parempi edetä yksinkertaisempaa reittiä vaihe vaiheelta.

Lopuksi halusin testata, onnistunko muokkaamaan Karvisen [ohjetta]( https://terokarvinen.com/2023/salt-vagrant/) kolmen koneen verkolle niin, että Salt-varasto (repo) tulee haettua ja  asetettua automaattisesti. Pienen säädön jälkeen tämä onnistui. Totesin myös, että admin-tila Windowsin komentorivityökaluissa oli tarpeeton.

<img width="1039" height="344" alt="image" src="https://github.com/user-attachments/assets/f31843ed-fd1d-43f3-ae9a-77df43c393d9" />
 
<img width="579" height="240" alt="image" src="https://github.com/user-attachments/assets/686e9f25-811e-44fb-b81c-cdbcfd2f5860" />

Orjien Salt-demoneja tosin piti jälleen käydä potkaisemassa omin jaloin. Tässä yhteydessä kävi ilmi, että Salt ei ollut asentunut minion2:selle, vaikka minon1:selle se asentui ilman ongelmia. Tekoäly kertoi, että kyse saattoi olla yleisestä Vagrantin virheestä monikoneympäristössä (APT-lukitusongelma). Ratkaisu on asentaa koneet peräkkäin: `vagrant up --no-parallel`.

Kokeilin provisioida minion2:sen uudestaan:`vagrant provision minion2`, `vagrant relaod minion2`. Ei onnistunut, joten ´vagrant destroy minion2`, `vagrant up minon2`eli luonti alusta.

<img width="715" height="491" alt="image" src="https://github.com/user-attachments/assets/474041fc-5e1f-44ba-a2cc-8d1c7c2aeedb" />

Sain hyväksyttyä avaimet, mutta testailuvaiheessa jompikumpi koneista oli Saltin ulottumattomissa (tai avainten hyväksyntä katosi). Tekoälyn mukaan puuttuvilla id:eillä minion-tiedossa oli mahdollista vaikutusta asiaan (vaikka niiden pitäisi korvautua automaattisesti host-nimellä). Kävin tosin naputtelemassa ne paikalleen käsin. Linuxin työmuisti taisi myös täyttyä jossain vaiheessa, mikä johti Saltin sulkeutumiseen odottamattomasti. Luovutin muutaman tunnin testailun jälkeen, ja päätin jatkaa kotitehtävää aluksi luodulla kahden koneen setillä. 

<img width="1016" height="1054" alt="image" src="https://github.com/user-attachments/assets/0b3626ed-3a10-4efe-ae44-0c57916c73db" />
 
<img width="869" height="604" alt="image" src="https://github.com/user-attachments/assets/e4574dc5-8bcd-4223-acd4-7f18141217f0" />

Kolmen koneen Vagrant-projektin Vagrantfile näytti tältä (muokattu Karvisen mallista).

## e) Tilat verkon yli

_Kokeile vähintään kahta tilaa verkon yli (viisikosta: pkg, file, service, user, cmd)._

Käytettyäni tuntikausia aikaa erinäisiin harhapolkuihin ja testailuihin, päätin suorittaa loppuosan viikon kotitehtävästä suoraviivaisesti.

Loin masterin /srv/salt-kansioon moduulin ’favpackages’ ja sen init.sls-tiedostoon tilan, joka asentaa micron kaikille koneille. 

<img width="584" height="234" alt="image" src="https://github.com/user-attachments/assets/048247d7-325d-44bc-827e-c5f9ac083c8b" />

`sudo salt ’*’ state.apply favpackages` ja tila olisi tarkoitus ajaa kaikissa orjissa.

<img width="821" height="284" alt="image" src="https://github.com/user-attachments/assets/92e02e37-0929-4049-9e1a-79fadea50b77" />
 
<img width="325" height="209" alt="image" src="https://github.com/user-attachments/assets/b089bdea-ecc3-4cd4-b5dd-3154478b9d87" />

Micro tuli asennettua minion1:seen pkg-installed-tilan kautta moduulista (state.apply).

<img width="996" height="551" alt="image" src="https://github.com/user-attachments/assets/862aef77-d1f9-4afa-8b4a-d79b46dcfaa8" />

File.managed-tila tuli testattua jo aiemmin (silloin käytössä state.single).

Tehtävän loppupuolella avaimet alkoivat taas temppuilla ja yhteys katkeili. Epäilin Linuxin työmuistiongelmia ja testien myötä kasautunutta tauhkaa. Kaikki tehtävän osat oli lopulta suoritettu, joten totesin, että nyt saattaisi olla hyvä hetki tuhota testikoneet. `vagrant destroy -f`.

## Lähteet

HashiCorp. s.a. Discover Vagrant Boxes. https://portal.cloud.hashicorp.com/vagrant/discover

HashiCorp. s.a. Get Started. https://developer.hashicorp.com/vagrant/tutorials/get-started 

HashiCorp. s.a. Get Started. HashiCorp. s.a. Installation. https://developer.hashicorp.com/vagrant/docs/installation 

HashiCorp. s.a. Vagrant. https://developer.hashicorp.com/vagrant

Karvinen, T. 2025. Palvelinten Hallinta. https://terokarvinen.com/palvelinten-hallinta/

Karvinen, T. 2018. Salt Quickstart – Salt Stack Master and Slave on Ubuntu Linux. https://terokarvinen.com/2018/salt-quickstart-salt-stack-master-and-slave-on-ubuntu-linux/ 

Karvinen, T. 2023. Salt Vagrant - automatically provision one master and two slaves. https://terokarvinen.com/2023/salt-vagrant/#infra-as-code---your-wishes-as-a-text-file 

Karvinen, T. 2021. Two Machine Virtual Network With Debian 11 Bullseye and Vagrant. https://terokarvinen.com/2021/two-machine-virtual-network-with-debian-11-bullseye-and-vagrant/

StackOverflow. s.a. Where can I set path to make.exe on Windows? https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows?newreg=446f3b22164548faa40ac0a7898e3575 

Yager, R. 2023. How to Run Vagrant on Windows. https://overgrowncarrot1.medium.com/how-to-run-vagrant-on-windows-1f85392f96d3 
 
