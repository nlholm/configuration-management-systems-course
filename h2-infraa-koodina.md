# h2 Infraa koodina

[Palvelinten hallinta 2025 loppusyksy](https://terokarvinen.com/palvelinten-hallinta/) -kurssin toisessa tehtävässä harjoittelin Saltin käyttöä luomalla infraa koodina. Kun asetukset tehdään työasemille ja palvelimille koodaamalla, on mahdollista hallita lukuisia koneita yhtä aikaa yksinkertaisilla, tekstitiedostoihin kirjoitettavilla tilafunktioilla.

Tein tehtävän keskiviikkona 29.10.2025 klo 14.00-20.00.

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

__Karvinen 2014: Hello Salt Infra-as-Code__

Karvinen ohjeistaa luomaan yksinkertaisen ”Hello World” -tyyppiseen moduulin Saltilla seuraavasti: 
- Asenna Salt.
- Luo kansio moduulille master-koneen /srv/salt-kansioon. Master jakaa ohjeet orjille tästä kansiosta. Esim. ’hello’.
- Luo init.sls-tiedosto moduulikansioon. `sudoedit init.sls`.
- Luo init.sls-tiedostoon sisältöä YAML-kielellä. Esimerkiksi am. koodi luo /tmp/-kansioon hello-infra-nimisen tyhjän tiedoston käyttämällä tilafunktiota file.managed.
```
/tmp/hello-infra:
  file.managed
```
- Aja moduulissa ja sen init.sls-tiedostossa määritetty tila: `sudo salt-call --local state.apply hello`.
- Tarkkaile idempotenssia. Jos ajat ym. tilan uudelleen, muutoksia pitäisi tulla 0, koska tiedosto on jo luotu; tila on idempotentti.
 
__Salt contributors: Salt overview,__ kohdat Rules of YAML, YAML simple structure, Lists and dictionaries - YAML block structures

YAML Salt -konfiguraatiotiedostomuoto

YAML (YAML Ain’t Markup Language) on merkintäkieli, jossa keskiössä ovat data-rakenteet (sanakirjat ja listat). Salt käyttää oletuksena YAML-rendreröintiä, joka muuntaa YAML-rakenteet Python-rakenteiksi.

Perussäännöt
- Tiedot esitetään avain: arvo -pareina.
- Avain ja arvo erotetaan ”: ” (kaksoispiste + välilyönti).
- Avainten arvot voivat olla eri rakenteita.
- Avaimet ovat kirjainkokoherkkiä.
- Vain välilyönnit on sallittu, ei tabulaattoreita.
- Kommentit alkavat #.

Perusrakenteet

1) Skalaarit (scalar, alkioarvo/perusarvo)
Arvo voi olla numero, merkkijono tai boolean.

```
vegetables: peas
fruit: apples
grains: bread
```

2) Listat
Arvona lista, jokainen alkio omalle rivilleen.

```
vegetables:
  - peas
  - carrots
fruits:
  - apples
  - oranges
```

3) Sanakirjat (dictionary)
Sisältää avain: arvo -pareja ja listoja.

```
dinner:
  appetizer: shrimp cocktail
  drink: sparkling water
  entree:
    - steak
    - mashed potatoes
    - dinner roll
  dessert:
    - chocolate cake
```

Lohkokenteet (block structures)

YAML-kieli kirjoitetaan lohkorakenteella.

- Rakenne määräytyy sisennyksen perusteella – käytä välilyöntejä (yleensä 2).
- Listan jokainen alkio alkaa: - (välilyönti)

__Salt contributors: The top file,__ kohdat Introduction, A basic example

Top-tiedosto (top.sls)

Top-tiedosto määrittää, mitä Saltin tiloja (state) sovelletaan mihin koneisiin.

Suurin osa ympäristöistä koostuu konejoukoista, joilla on samankaltaiset roolit. Esim. joukko verkkopalvelimia saattaa vaatia Apache-paketin ja palvelun käynnissäolon.

Saltissa top-tiedosto muodostaa kartoituksen:
-  mille koneille sovelletaan
-  mitä konfiguraatiorooleja (SLS-tiloja)

Top-tiedoston nimi on oletuksena top.sls, ja se sijaitsee tila¬tiedostojen hakemistorakenteen (state tree) juurella.

Top-tiedostot koostuvat kolmesta osasta: 
- ympäristö (environment): hakemisto, jossa sijaitsevat tilatiedostot (yleensä ’base’)
- kohde (target): konejoukko, johon tiloja sovelletaan
- tilatiedostot (state files): SLS-tiedostot, jotka määrittävät, mitä tiloja kohteisiin asetetaan

Esimerkki:

Ajetaan apache.sls eli apache-tila kaikille orja-koneille, joiden ID alkaa ’web’-tunnisteella (vs. ’*’ viittaisi kaikkiin orja-koneisiin):

```
base:          # Apply SLS files from the directory root for the 'base' environment
  'web*':      # All minions with a minion_id that begins with 'web'
    - apache   # Apply the state file named 'apache.sls'
```

## a) Hei infrakoodi! 

_Kokeile paikallisesti (esim 'sudo salt-call --local') infraa koodina. Kirjota sls-tiedosto, joka tekee esimerkkitiedoston /tmp/ -kansioon._

Loin [Karvisen ohjeilla](https://terokarvinen.com/2024/hello-salt-infra-as-code/) kansion /srv/salt/hello/ ja sinne init.sls-tiedoston. Tiedostossa kuvaillaan file.managed-tila, joka luo tiedoston hellonlholm temp-kansioon sekä ko. tiedostoon tekstin ”Hei maailma!”.

`sudo mkdir -p /srv/salt/hello`
`cd /srv/salt/hello/`
`sudoedit init.sls`
`sudo salt-call --local state.apply hello`

<img width="542" height="132" alt="image" src="https://github.com/user-attachments/assets/acc3e4b3-123b-47f7-a244-d2ac96fba276" />

Vaihtohtoinen syntaksi:

testi [ID]:
  file.managed [tila]:
    - name: /tmp/hellonlholm [ajettava komento]
    - contents: ”Hei maailma!” [sisältö]

Tilan nimi voidaan siis sijoittaa ID:n paikalle, mutta se ei aina ole selkein tapa.

<img width="613" height="342" alt="image" src="https://github.com/user-attachments/assets/3963ce2d-ebc4-4049-9932-24c3ac507322" />
 
<img width="504" height="93" alt="image" src="https://github.com/user-attachments/assets/4ba0985c-259f-4016-ae54-5577351c74f4" />

`sudo salt-call --local state.apply hello`-komento ja tila luotiin.

## b) Toppping 

_Tee top-file, niin että kaikki omat tilasi ajetaan kerralla komennolla 'sudo salt-call --local state.apply'._

Loin /srv/salt/-kansion juureen top filen nimeltä top.sls. Tiedoston sisältö:

base:
  ’*’:
    - several_states

Vaihtoehtoinen tapa olisi vain listata moduuleita tasolla 3 (esim. hello, hellopkg…).

Top file ajetaan komennolla `sudo salt-call --local state.apply`eli ilman argumenttia. Tässä tapauksessa se ajaa moduulin several_states, josta lisää kohdassa d. Tähti tiedoston keskellä tarkoittaa, että tila ajetaan kaikissa orjissa.

<img width="538" height="124" alt="image" src="https://github.com/user-attachments/assets/0c5aaac7-6af6-4780-8c50-ccab461ec79e" />
 
<img width="665" height="84" alt="image" src="https://github.com/user-attachments/assets/5971a844-c5b7-4a8e-aacb-cf645e557167" />

<img width="665" height="268" alt="image" src="https://github.com/user-attachments/assets/c28cc611-6028-4486-b37e-b376e0d5a3ea" />
 
Sain virheen.

<img width="546" height="116" alt="image" src="https://github.com/user-attachments/assets/81b84fa2-7fdd-4cb4-a93e-2d93e7221f1e" />
 
Korjasin hipsut.

<img width="536" height="669" alt="image" src="https://github.com/user-attachments/assets/be423dd8-2e6a-4dee-8b07-232ef97d55de" />

Nyt top filen ajaminen onnistui.

## c) Viisikko tiedostossa 

_Tee erilliset esimerkit kustakin viidestä tärkeimmästä tilafunktiosta pkg, file, service, user, cmd. Kirjoita esimerkit omiksi tiloikseen /srv/salt/ alle, esim /srv/salt/hellopkg/init.sls._

Loin yleisimmille tilafunktioille omat moduulit /srv/salt/-hakemistoon, ja kirjoitin moduuleiden init.sls-tiedostoihin halutut tilat.

hellopkg

micro:
  pkg.installed

hellofile

/tmp/testfile1:
    file.managed

helloservice

apache2:
  service.running

helllouser

testuser1:
  user.present

hellocmd

touch_tmp_test:
  cmd.run:
    - name: touch /tmp/test2
    - creates: /tmp/testfile2

Luotavan tiedoston nimi tarvitaan nimi-kohtaan.

Cmd-tilan luominen oli hieman haasteellisempaa kuin muiden. 

Avuksi tuli muistilista tiloista:
- tilan ID määritetään tasolla 1 (voi olla samalla name)
- tila (esim. cmd.run) tasolla 2
- name tasolla 3 kertoo ajettavan komennon (jos sitä ei ole annettu ID:n yhteydessä)
- asetukset tasolla 3

<img width="550" height="162" alt="image" src="https://github.com/user-attachments/assets/5b248ead-818a-47f4-b913-c1bfd25f873c" />
 
<img width="625" height="460" alt="image" src="https://github.com/user-attachments/assets/ee31101f-7e10-406d-84b8-a99566c6a4d1" />

<img width="290" height="262" alt="image" src="https://github.com/user-attachments/assets/e8d5fb08-77c1-4a56-bd80-f29481d61200" />

Testailua:

<img width="665" height="298" alt="image" src="https://github.com/user-attachments/assets/1dff142f-0c08-4f6f-9184-ab0e870ae0f9" />
 
hellofile toimi.

<img width="665" height="174" alt="image" src="https://github.com/user-attachments/assets/a7d0ae3f-2893-44be-97c1-00e30c90a204" />
 
hellocmd sen sijaan ei toiminut, testfile2-nimistä tiedostoa ei ollut.

<img width="598" height="145" alt="image" src="https://github.com/user-attachments/assets/87df3cf1-82a8-4539-86ff-814d7030919d" />

Kirjoitin tiedoston nimen name-kohtaan.

<img width="665" height="180" alt="image" src="https://github.com/user-attachments/assets/e7263dba-951e-4e09-8c9f-a446473f1610" />

Nyt toimi.

## d) Monta tilaa

_Tee sls-tiedosto, joka käyttää vähintään kahta eri tilafunktiota näistä: package, file, service, user. Tarkista eri ohjelmalla, että lopputulos on oikea. Osoita useammalla ajolla, että sls-tiedostosi on idempotentti._

Loin kansioon /srv/salt/ moduulin several_states ja sinne init.sls-tiedoston. Tiedoston sisältö:

pacakges_ufw_installed:
  pkg.installed
    - name: ufw

users_testuser3:
  user.present:
    - name: testuser3

files_testfile3:
  file.managed:
    - name: /tmp/testfile3
    - contents: “This is test file 3.”

Koin, että ID:eiden erottaminen nimistä oli itselleni havainnollisempi tapa hallita tiloja.

<img width="606" height="249" alt="image" src="https://github.com/user-attachments/assets/3c41c733-4674-4258-84af-e145715a894d" />
 
Testailuja:

<img width="665" height="383" alt="image" src="https://github.com/user-attachments/assets/d15c3c5d-5080-4cb5-88d3-6e8d5f8a082a" />
 
Jaa, jotain meni pieleen.

<img width="665" height="279" alt="image" src="https://github.com/user-attachments/assets/740b9729-ebf9-420d-b258-b9e25d8b66c5" />

Korjasin puuttuvan kaksoispisteen.

<img width="665" height="443" alt="image" src="https://github.com/user-attachments/assets/e0cdb9b0-6b77-4c7f-a57e-b73600b95afc" />
 
<img width="665" height="251" alt="image" src="https://github.com/user-attachments/assets/52f77062-f7b7-4ed5-9788-3c8ce7d59954" />

Nyt ei tullut virheitä.

<img width="572" height="222" alt="image" src="https://github.com/user-attachments/assets/9a90f03d-8f2e-45ba-8c9e-6fc27fbb3681" />

Palomuuri oli päällä.

<img width="412" height="58" alt="image" src="https://github.com/user-attachments/assets/3d04040a-e839-484f-8e32-3e82bca3f63b" />

Testikäyttäjät löytyivät (`cat /etc/passwd`).

<img width="662" height="208" alt="image" src="https://github.com/user-attachments/assets/102173c4-f869-4db7-a175-f8218031de9f" />

Samoin kuin tetsfile3.

<img width="605" height="298" alt="image" src="https://github.com/user-attachments/assets/01c7065d-3440-4654-9750-2ce0d0e33787" />

Kun ajoin tilan `sudo salt-call --local state.apply hellouser` uudemman kerran, muutoksia ei ilmennyt: käyttäjä on jo luotu ja tila on idempotentti.

## Lähteet

Karvinen, T. 2014. Hello Salt Infra-as-Code. https://terokarvinen.com/2024/hello-salt-infra-as-code/

Karvinen, T. 2025. Palvelinten Hallinta. https://terokarvinen.com/palvelinten-hallinta/

Salt Project. s.a. Salt overview. https://docs.saltproject.io/salt/user-guide/en/latest/topics/overview.html#rules-of-yaml

Salt Project. 2025. The Top File. https://docs.saltproject.io/en/latest/ref/states/top.html 
