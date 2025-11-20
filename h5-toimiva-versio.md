# h5 Toimiva versio

[Palvelinten hallinta 2025 loppusyksy](https://terokarvinen.com/palvelinten-hallinta/) -kurssin viidennessä tehtävissä harjoittelin versionhallintatyökalu Gitin käyttöä.

Git-asennuspaketit Windowsille ja Macille löytyvät osoitteesta https://git-scm.com/downloads. Linuxille Git asentuu näppärimmin paketinhallinnasta: ` sudo apt-get -y install git`.

Tein tehtävän keskiviikkona 19.11.2025 klo 12:30-17:30.

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

### Mikä Git on?

_Chacon and Straub 2014: [Pro Git, 2ed](https://git-scm.com/book/en/v2): [1.3 Getting Started - What is Git?](https://git-scm.com/book/en/v2/Getting-Started-What-is-Git%3F)_ 

Chacon ja Straubin e-kirjassa (jonka muokkaamisessa sadat myötävaikuttajat ovat avustaneet) kerrotaan, kuinka Git eroaa muista versionhallinatyökaluista ja mitkä ovat sen pääominaisuudet.

- Git tallentaa projektin historian snapshoteina, ei muutoksina (delta), mikä tekee historia-tiedosta luotettavaa ja aiempien versioiden palauttamisesta suoraviivaista.
- Git toimii pääasiassa paikallisesti, joten useimmat toiminnot ovat erittäin nopeita eikä verkkoa tarvita versionhallintaan.
- Git käyttää kryptografisia tarkisteita (SHA-1) varmistaakseen datan eheyden: mikään tiedosto tai commit ei voi muuttua huomaamatta.
- Gitissä jokainen tiedosto voi olla kolmessa tilassa (modified, staged, committed), mikä mahdollistaa tarkan kontrollin siitä, mitkä muutokset päätyvät seuraavaan commitiin.

### Yleisimmät git-komennot

_Gitin käyttö on lähinnä 'git add . && git commit; git pull && git push'. Selitä tuon komennon jokainen osa. Käytä apuna itse valitsemiasi lähteitä ja viittaa niihin._

Gitin käyttöön löytyy lukuisia oppaita. Olen havainnut helppokäyttöisiksi paitsi git-scm:än oppaat (kuten https://git-scm.com/cheat-sheet), myös Haaga-Helian opiskelijoille suunnatun oppaan (https://github.com/mruonavaara/git101) ja Helsingin yliopiston tietojenkäsittelytieteen ensimmäisen vuoden opiskelijoille suunnatun oppaan (https://tkt-lapio.github.io/git/) .

- `git add .` Lisää tiedosto staging-alueelle. Piste lisää kaikki tiedostot, joihin on tehty muutoksia.
- `git commit` Kommitoi eli tallenna muutokset.
- `git pull` Hae muutokset etärepositoriosta ja yhdistä ne projektisi paikalliseen haaraan.
- `git push` Työnnä paikalliset kommitoidut muutokset etähaaraan (”tracking branch”).

### Git-loki ja muutokset

_Varaston terokarvinen/suolax/ historia, eli loki ja muutokset. Kätevimmin komentokehotteesta 'git clone https://github.com/terokarvinen/suolax.git; cd suolax/; git log --patch --color|less -R'. Wepistäkin saattaa onnistua kliksuttelemalla "Commits"._

Karvisen repositorion suolax historian saa GitHubissa näkymään etsimällä kohdan Commits ja navigoimalla ko. sivulle. Tai https://github.com/terokarvinen/suolax/commits/main/ . 

<img width="665" height="338" alt="image" src="https://github.com/user-attachments/assets/561ec0d9-8e48-4b28-b7b1-a0d117f48a40" />

Kommitoinnit. Id kertoo tarkemmin mitä muuttui. Kulmasuluista voi navigoida projektin ko tilaan.

<img width="665" height="623" alt="image" src="https://github.com/user-attachments/assets/82cbf1b7-8e3d-45f8-a95b-8e5faaacce67" />

Osa kommitoinneista Linuxin komentokehotteessa, kun repo on kloonattu (`git log --patch --color|less -R`).

## a) Online

_Tee uusi varasto GitHubiin (tai Gitlabiin tai mihin vain vastaavaan palveluun). Varaston nimessä ja lyhyessä kuvauksessa tulee olla sana "snow". Aiemmin tehty varasto ei kelpaa. (Muista tehdä varastoon tiedostoja luomisvaiheessa, esim README.md ja GNU General Public License 3.)_

LoinGitHubissa uuden varaston ’linux-test-snow’. https://github.com/nlholm/linux-test-snow/tree/main 

<img width="665" height="305" alt="image" src="https://github.com/user-attachments/assets/f4a5a1bd-256d-4a76-80cf-7b66746ddf57" />

## b) Dolly

_Kloonaa edellisessä kohdassa tehty uusi varasto itsellesi, tee muutoksia omalla koneella, puske ne palvelimelle, ja näytä, että ne ilmestyvät weppiliittymään._

Kloonasin varaston Linuxilleni käyttäen SSH:ta (Debianin  julkinen avain oli lisätty jo aiemmin). 

`git clone git@github.com:nlholm/linux-test-snow.git`

<img width="665" height="104" alt="image" src="https://github.com/user-attachments/assets/b1e9b614-211f-4cfa-89c2-3ef2edaad82b" />

Tein muutoksen README-tiedostoon.

<img width="608" height="268" alt="image" src="https://github.com/user-attachments/assets/bb7e0e0b-5b89-4c4c-80d4-5e13d3cfb131" />

`git add .` , `git commit` ja commit-viesti. Sitten `git pull` ja `git push` . Pull oli tässä tapauksessa turha, koska kukaan muu ei ollut työstänyt online-varastoa, mutta pidin komennon mukana.

<img width="665" height="297" alt="image" src="https://github.com/user-attachments/assets/38a60700-c8d5-4eda-be19-f98a4273d8cb" />

Muutos näkyi GitHubissa. Korjasin otsikon jälkikäteen (markup; puuttuva välilyönti risuaidan ja otsikon välissä).

## c) Doh! 

_Tee tyhmä muutos gittiin, älä tee commit:tia. Tuhoa huonot muutokset ‘git reset --hard’. Huomaa, että tässä toiminnossa ei ole peruutusnappia._

<img width="665" height="108" alt="image" src="https://github.com/user-attachments/assets/bbb06477-61c3-46bc-afba-5abfb582c4a2" />

Muokkasin README-tiedostoa uudestaan. 

<img width="522" height="294" alt="image" src="https://github.com/user-attachments/assets/a6bb744e-03da-4249-859b-66644601d269" />

Peruin muutoksen ennen kommitointia `git reset --hard`. Cheat sheetin (https://git-scm.com/cheat-sheet) mukaan tämän pitäisi toimia jopa kommitoiduille muutoksille.

<img width="665" height="107" alt="image" src="https://github.com/user-attachments/assets/8fbbc168-b85e-47c9-bd0d-5556a744d996" />

<img width="665" height="630" alt="image" src="https://github.com/user-attachments/assets/f378cdd2-6646-46a6-bfdd-95845e2d2d55" />

Ei toiminut, kommitoituja muutoksia ei voinut palauttaa. Palautin README:n lähtötilanteeseen testien jälkeen.

## d) Tukki

_Tarkastele ja selitä varastosi lokia. Tarkista, että nimesi ja sähköpostiosoitteesi näkyy haluamallasi tavalla ja korjaa tarvittaessa._

<img width="665" height="499" alt="image" src="https://github.com/user-attachments/assets/489a0cb5-f666-45c0-89e7-84ddf393257a" />

Lokissa näkyvät kommitit ja niiden ajankohta. Myös linkattu GitHub-tilini näkyy.

## e) Suolattu rakki

_Aja Salt-tiloja omasta varastostasi. (Salt tiedostot mistä vain hakemistosta "--file-root teronSaltHakemisto". Esimerkiksi 'sudo salt-call --local --file-root srv/salt/ state.apply', huomaa suhteellinen polku.)_

Loin jälleen uuden repositorion GitHubiin.

<img width="665" height="266" alt="image" src="https://github.com/user-attachments/assets/9f8bf160-c3b1-4637-bdfc-6c354df3e947" />
 
<img width="558" height="134" alt="image" src="https://github.com/user-attachments/assets/85fc0cfc-bd13-41e7-bbaf-ed239c115b24" />

Kloonasin projektin Linuxin code-kansioon ja loin sille alikansion, jonne taas loin hello-moduulin.

<img width="340" height="142" alt="image" src="https://github.com/user-attachments/assets/cbdc61f6-25a8-4c9f-aa2f-c8d888e061d2" />

Loin helllo-moduuliin Salt-tilan, joka luo tyhjän tiedoston tmp-polkuun.

Sitten tultiin mutkikkaaseen kohtaan. Komennossa ` sudo salt-call --local --file-root <polku> state.apply <tila>` __--file-root__ ottaa polun tilojen juureksi, kuten tekoäly (ChatGPT 5.1) kertoi, kun pyysin sitä selventämään mitä komennossa tapahtuu. Vs. oletuspolku Salt-tiloille on /srv/salt/.  

<img width="665" height="235" alt="image" src="https://github.com/user-attachments/assets/54aa24f0-c330-46d8-afd6-ad38f2f11185" />

Siirryin kansiorakenteessa kohtaan salt-git-test ja ajoin sieltä ` sudo salt-call --local --file-root saltdir state.apply hello`. Tila meni läpi. Vastaava absoluuttinen polku olisi ollut ` sudo salt-call --local --file-root ~/code/salt-git-test/saltdir state.apply hello`.

<img width="665" height="310" alt="image" src="https://github.com/user-attachments/assets/5331266b-806e-4fd2-a4ed-dc1ee5acff4d" />

Kansio ja sen sisältö ilmestyivät myös GitHubiin.

## f) Pari 

_Vapaaehtoinen: Hae paria projektiin Moodlen keskustelusta. (Parin saa valita, arvon muille parin.)_

Pari löytyi jo, [punnalathomas]( https://github.com/punnalathomas).

## g) Se toinen järjestelmä 

_Vapaaehtoinen: Se toinen järjestelmä: kokeile Gittiä eri käyttöjärjestelmällä kuin sillä, millä teit muut harjoitukset. Selitä niin, että kyseistä järjestelmää osaamatonkin onnistuu. Mahdollisuuksia on runsaasti: Debian, Fedora, Windows, OSX..._

Suoritan parhaillani Ohjelmointi2-kurssia, jonka tehtävät jaetaan GitHubiin opiskelijan henkilökohtaiselle tilille. Tehtävävarasto kloonataan omalle koneelle, tehtäviä työstetään, ja muutokset pusketaan takaisin GitHubiin. Suoritan kurssia Wundows-koneellani. Gitin osalta käyttö on hyvin samankaltaista kuin Linuxissa.

<img width="665" height="213" alt="image" src="https://github.com/user-attachments/assets/e25c62ba-fa89-42cd-8439-acdc529783ae" />

Tässä kloonasin viimeisimmän tehtävärepositorion Windowsin command promptissa ja avasin kansion VSCodella (`code .`). `Git status`, `git add .`, `git commit` ja `git push` toimivat vastaavasti kuin Linuxissa. GitHub Classroom tarkistaa ja pisteyttää tehtävät, kun ne on puskettu takaisin GitHubiin.

## h) Yhteistyötä

_Vapaaehtoinen: yhteistyötä: anna kaverillesi (tai alter egollesi) oikeus kirjoittaa varastoosi (commit access). Tehkää molemmat muutoksia varastoon gitillä._

Lisäsin parini testi-repon yhteistyökumppaniksi GitHubissa. Kyseessä on henkilökohtainen repo (ei organisaatiorepo), joten kumppani saa automaattisesti write-oikeudet.

<img width="665" height="335" alt="image" src="https://github.com/user-attachments/assets/c3e019fc-3f60-4fe8-9306-09b23860081c" />

Emme vielä testanneet muutosten tekemistä yhdessä.

## Lähteet

Chacon, S. & Straub, B. 2014/2025. Apress. Pro Git. https://git-scm.com/book/en/v2  

Helsingin yliopisto. s.a. Tietokone työvälineenä - Lapio. Osa 2 - Versionhallinta: Git ja Github. https://tkt-lapio.github.io/git/ 

Git-scm.com. s.a. Git Cheat Sheet. https://git-scm.com/cheat-sheet 

Git-scm.com. s.a. Install. https://git-scm.com/install/ 

Karvinen, T. 2025. Palvelinten Hallinta. https://terokarvinen.com/palvelinten-hallinta/

Mruonavaara. s.a. Git 101. https://github.com/mruonavaara/git101 
