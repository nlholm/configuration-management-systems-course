# h4 Pkg-file-service

[Palvelinten hallinta 2025 loppusyksy](https://terokarvinen.com/palvelinten-hallinta/) -kurssin neljännessä tehtävässä opettelin asentamaan ja hallitsemaan demoneja eli palvelinohjelmistoja Saltin avulla. Tämä tapahtuu vain kolmea Salt-tilaa hyödyntämällä: pkg, file, service.

Tein tehtävää keskiviikkona 12.11.2025 klo 15:30-19:45 sekä torstaina 13.11.2025 klo 15:30-17.00. Aloitin lueskelemalla aiempien kurssitoteutusten opiskelijoiden raportteja.

Tehtävänanto tiivistää mistä pkg-file-servicessä on kyse:

<img width="903" height="395" alt="image" src="https://github.com/user-attachments/assets/499f0a6a-5461-4440-8fb0-ce253eb1ebf4" />

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

### Karvinen 2018: [Pkg-File-Service – Control Daemons with Salt – Change SSH Server Port]( https://terokarvinen.com/2018/04/03/pkg-file-service-control-daemons-with-salt-change-ssh-server-port/)

Karvisen ohjeessa kerrotaan (hyvinkin tiiviisti), kuinka on mahdollista toteuttaa yksinkertainen pkg-file-service-konffi. Esimerkkinä käytetään SSH-demonin kuuntelevan portin muutosta.

Konffi toteutetaan Salt-masterin sshd-moduulin init.sls-tiedostossa:

- Asenna SSH-palvelin:
  
```
openssh-server:
 pkg.installed
```

- Luo kopio* SSHd-konfiguraatiotiedostosta sshd-moduuliin, tee siihen haluttu muutos (esim. lisää kuunteleva portti) ja käytä muokattua tiedostoa minion-koneiden konfiguraatiotiedoston yliajamiseen (näitä vaiheita ei artikkelissa varsinaisesti kerrota):

\* `sudo cp etc/ssh/sshd_config srv/salt/sshd/sshd_config`

```
/etc/ssh/sshd_config:
 file.managed:
   - source: salt://sshd/sshd_config
```

- Seuraa mahdollisia muutoksia muokattuun konffitiedostoon. Jos sellaisia havaitaan, käynnistä demoni uudelleen:

```
sshd:
 service.running:
   - watch:
     - file: /etc/ssh/sshd_config
```

Salt-tila ajetaan `sudo salt '*' state.apply sshd`. Se testataan `nc -vz hostname@minion ’uusi portti’ ` (jos käytössä Vagrant) tai `nc -vz localhost ’uusi portti’ ` ja `ssh -p ’uusi portti’ hostname@localhost` (jos testaus paikallisesti). Ennen testausta kannattaa avata palomuurista uusi portti (`sudo ufw allow ’uusi portti’/tcp`ja `sudo ufw status`) sekä käynnistää SSH uudelleen (`sudo systemctl restart ssh`).

## a) SSHouto

_Lisää uusi portti, jossa SSHd kuuntelee._
- _Jos käytät Vagrantia, muista jättää portti 22/tcp auki - se on oma yhteytesi koneeseen. SSHd:n asetustiedostoon voi tehdä yksinkertaisesti kaksi "Port"-riviä, molemmat portit avataan._
- _Löydät oikean asetuksen katsomalla SSH:n asetustiedostoa._
- _Nyt tarvitaan service-watch, jotta demoni käynnistetään uudelleen, jos asetustiedosto muuttuu masterilla._
- _Ensin käsin: muista näyttää, että ensin teit ja testasit muutoksen käsin._
- _Näytä, että tilasi korjaa puutteet. Voit esimerkiksi poistaa paketin tai tehdä virheen tiedostoon käsin, sitten ajaa tilasi._

<img width="703" height="570" alt="image" src="https://github.com/user-attachments/assets/0ee4bf06-3c42-481e-9bc1-87dbf2e5b23f" />

Tein tehtävän paikallisesti virtuaali-debianillani. Aloitin lisäilemällä avoimia portteja debianin palomuuriin (ml. portin 8888 jonka ajattelin avata SSH-demonille).

<img width="1039" height="168" alt="image" src="https://github.com/user-attachments/assets/3d970f3c-3f38-437f-90d7-2ef996b949d4" />

Totesin, että ssh-demonia ei ole asennettu. Asensin demonin ensin käsin: `sudo apt-get install -y openssh-server`. 

<img width="808" height="101" alt="image" src="https://github.com/user-attachments/assets/713d739f-c8fa-433a-91ec-17d52a5fcae9" />

Demoni kuunteli oletusportissa 22.

<img width="931" height="369" alt="image" src="https://github.com/user-attachments/assets/a96ed077-9e8f-4496-83bb-6d72f2d5f0f9" />

Loin masterin /srv/salt-kansioon moduulin sshd ja sinne init.sls-tiedoston. Määritin pkg-, file- ja service-tilat.

SSHd oli jo asennettu, joten navigoin kohdteeseen /etc/ssh ja menin tutkimaan alkuperäistä sshd_config-tiedostoa. Kopioin tiedoston /srv/salt/sshd/-kansioon. `sudo cp /etc/ssh/sshd_config /srv/salt/sshd/sshd_config`

<img width="1039" height="924" alt="image" src="https://github.com/user-attachments/assets/084b4ea6-5712-4b53-a668-c0f7ee286b9b" />

Lisäsin uuden portin kopioituun konffitiedostoon. Käytin samaa kommentointityyliä kuin tiedostossa, vaikka asia hieman epäilytti.

<img width="846" height="1140" alt="image" src="https://github.com/user-attachments/assets/59e1c0cb-97d0-466e-a385-47d26a3823b9" />

<img width="463" height="215" alt="image" src="https://github.com/user-attachments/assets/a004d7f1-728f-4d55-8a95-c815cd9c6ad3" />

Kun olin korjaillut init-tiedostoa muutamaan kertaan, tila meni läpi. SSHd oli jo asennettu, joten vain kaksi muutosta.

<img width="1022" height="79" alt="image" src="https://github.com/user-attachments/assets/12020895-0e24-432b-98ed-fac860cc33c8" /> 

SSH-palvelin ei kuitenkaan harmillisesti vastannut portista 8888.

<img width="1039" height="873" alt="image" src="https://github.com/user-attachments/assets/f8c084da-db7b-4509-937e-f7bff4466bec" />
 
Poistin kommentit sshd-moduulin konffitiedostosta (oletusportti 22 toimii myös kommentoituna).

<img width="790" height="921" alt="image" src="https://github.com/user-attachments/assets/5f05108b-84f7-4503-bc3a-00fdc56442cf" />
 
<img width="765" height="95" alt="image" src="https://github.com/user-attachments/assets/a6d380c4-ba3a-4e54-a9fd-15854160b0a3" />

<img width="1039" height="247" alt="image" src="https://github.com/user-attachments/assets/0b522f4e-cc9b-4998-bd43-2820e7d3d565" />

Nyt SSHd vastasi portista 8888, tehtävä tuli suoritettua.

## b) Apache & Name Based Virtual Host

Tässä vapaaehtoisessa tehtävän b)-kohdassa kokeilin, onnistuisinko automatisoimaan Apachen asennuksen Saltilla niin että Apachen etusivu näyttää weppisvua. Ajan puutteen vuoksi en tehnyt tehtävää ensin käsin. Inspiraationani toimi veitim:in [raportti]( https://github.com/veitim/palvelinten_hallinta/blob/main/h4_pkg_file_service.md). Tein tehtävän kolmen koneen Vagrant-verkossa.

Aloitin tarkistamalla, että Salt saa yhteyden minioneihin. Sen jälkeen loin masterille moduulin namebased (`sudo mkdir -p /srv/salt/namebased`), ja sinne tiedoston init.sls sekä kansion, johon laitoin tiedostot, jotka tulisivat yliajamaan Apachen konffitiedoston sekä käyttäjän vagrant index.html-tiedoston. Root-käyttäjä on kaikissa koneissa nimeltään vagrant.

<img width="1039" height="755" alt="image" src="https://github.com/user-attachments/assets/9d431cce-e0be-4882-b1c6-8e7dd3c04d10" />

Init.sls

-	__install&run_apache2__. Asentaa Apachen, käynnistää ja uudelleenkäynnistää kun tiedostoa 000-default.conf muokataan (Apachen oletuksena päällä oleva .conf tiedosto).
-	__namebased_conf__. Name: tiedosto, jota muokataan. Source: tiedosto, jolla muokataan. Eli korvataan 000-default.conf-tiedoston sisältö.
-	__create_namebased__. File.directory on keino tehdä hakemistoja. True tekee hakemistot, jos niitä ei ole. User ja Group määrittävät oikeuksia.
-	__handle_html__. Tekee tiedoston index.html ja korvaa sen sisällön sourcesta tulevalla sisällöllä.

<img width="1039" height="364" alt="image" src="https://github.com/user-attachments/assets/8bb10945-de46-4cc3-ac02-87f796d0a9b1" />

index.html

<img width="946" height="335" alt="image" src="https://github.com/user-attachments/assets/4d2f273a-863f-4456-b195-e28e10d8012d" />

namebased.conf

<img width="1039" height="210" alt="image" src="https://github.com/user-attachments/assets/e76279fd-cf40-4cd9-868d-fd110a6cc779" />

Sitten kokeilemaan. `sudo salt-call --local state.apply namebased`

<img width="709" height="259" alt="image" src="https://github.com/user-attachments/assets/9b120da3-2b03-4a73-98d4-6d35c3d527cd" />
 
<img width="1039" height="341" alt="image" src="https://github.com/user-attachments/assets/25b74c54-b040-4c7f-95a7-6eaa80b436c3" />

Kas, sehän toimi.

Seuraavaksi kokeilin ajaa tilan minion-koneisiin. `sudo salt-call ‘*’ state.apply namebased`

<img width="594" height="220" alt="image" src="https://github.com/user-attachments/assets/823e4c7b-1dfd-43d5-b44b-ec9298c6ffae" />
 
<img width="1039" height="361" alt="image" src="https://github.com/user-attachments/assets/8e3405fd-a672-4801-ab37-2ded6e1f9e41" />

Weppisivu tuli asetettua vastaamaan localhostista myös minioneissa. Hyvillä ohjeilla pääsee pitkälle, vaikka tämä ei ehkä hienostunein ratkaisu ollutkaan.

## Lähteet

Karvinen, T. 2025. Palvelinten Hallinta. https://terokarvinen.com/palvelinten-hallinta/

Karvinen, T. 2018 Pkg-File-Service – Control Daemons with Salt – Change SSH Server Port. https://terokarvinen.com/2018/04/03/pkg-file-service-control-daemons-with-salt-change-ssh-server-port/

Veitim. 2025. h4_Pkg_file_service. https://github.com/veitim/palvelinten_hallinta/blob/main/h4_pkg_file_service.md 


