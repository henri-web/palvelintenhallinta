# H1 Hei Ansiblen maailma tehtävä

## Tiivistelmä 

27.10.2026

Aikaa en muista mutta tällöin tein osan tehtävistä H0 varten.

03.11.2026

Aloitus aika: 18.00

Lopetusaika: 22.00

04.11.2026

Aloitusaika 01.00

Lopetusaika 05.21

Tässä raportissa suoritin H1 tehtävän, sekä parantelin asiat kuntoon edellisestä H0 tehtävästä, jonka edellisen palautuksen voi unohtaa kokonaan raportit alkaa tästä. Ohjeena käytin Tero Karvisen sivuilta otettuja linkkejä.

Käytin apunu Geminiä asioitten oikein suomentamisessa.

## x) Lue ja tiivistä

Ensimmäisessä artikkelissä "SSH public key - Login without password" puhutaan lyhyesti mikä SSH-avain ja miten se asennetaan

- SSH-palvelimen asentaminen ja käynnistäminen Debian-ympäristössä.
- Paikallisen yhteyden tarkistaminen onnistuu `ssh localhost` komennolla
- Salasanattoman kirjautumisen automatisointi luomalla avainpari `ssh-keygen` ja kopioimalla se palvelimelle `ssh-copy-id`
- Ongelmatilanteiden selvittämiseen työkaluja `journalctl` ja `ssh -v`

SSH-avain on helpompi käyttää, sitä ei tarvitse muistaa ja se on turvallisempi kuin normaali salasana suojaus.

Toisessa artikkelissa "Hello Ansible" puhutaan Ansible järjestelmän käyttöönottoa ja hallintatyökalujen perusteita.

- Ansible on konfiguraatiohallintatyökalu. Kirjoitat infrastruktuurisi koodina.
- Ansible toimii SSH-yhteyden yli. Siten kohdekoneilla tarvitsee olla asennettuna vain SSH-taustapalvelu ja Python.
- Ansiblen asentaminen `sudo apt-get update` `sudo apt-get install ansible micro bash-completion tree`
- Kerrotaan asetusten määrittelystä, opastetaan luomaan kohdekoneita määrittelevä `hosts.ini` tiedosto.
- Rakennetaan yksinkertainen `site.yml` pelikirja ja `hello` rooli, joka luo testitiedoston kohdekoneen tmp-hakemistoon.
- Pelikirjan ajaminen toimii `ansible-playbook site.yml` komennolla.
- Lopussa käydään läpi yleisiä ongelmia ja virheitä, kuten puuttuva inventaario.

## a) Debian 13 asennus virtuaalikoneeseen (Tein tämän kohdan aikaisemmin eikä minulla ole ruutukaappauksia siitä)
Aloitin lataamalla koneelle virtualbox-ohjelman. Menin https://www.virtualbox.org/wiki/Downloads sivulle ja valitsin Windows hosts. 

Tarvitsin myös oikean version debianista, jonka sain sivulta https://cdimage.debian.org/debian-cd/current-live/amd64/iso-hybrid/ oikea versio: debian-live-13.7.0-amd64-xfce

Avaan virtualbox ohjelman ja klikkaan kohtaa "NEW" 

Seuraavassa valikossa nimeän koneen haluamallani tavalle nimesin sen itse "palvelintenhallinta" Sitten valitsen sijainnin ja ISO image kohtaan lataamani version.

Käyttöjärjestelmäksi tulee Linux ja tärkeää muistaa valita OS version kohtaan Debian (64-bit)

Base memory 4090MB, Number of CPUs 2 Disksize 50 GB

Seuraavaksi paina Finish.

Seuraavaksi klikkaan konetta valikostani ja laitan sen päälle. Paina Live system (amd64)

Testataan internetyhteyden toiminta menemällä verkkoon ja katsomalla toimiiko nettisivut vaikkapa Wikipedia.

Tämän jälkeen avaa terminaali ja anna komento `sudo reboot` 

Live Boot Menu with GRUB Start installer. 

Seuraavana tulee asetuksia jotka valitsin punnalathomaksen ohjeiden mukaan https://github.com/punnalathomas/linux-course/blob/main/h1-installing-linux.md paitsi domain nimi jätin mikä siinä oli valmiina.

## b) SSHsecrets ja c) Pubkey

Aloitin tarkistamalla onko minulla oikea ssh versio komennoilla `sudo apt-get update` ja `sudo apt-get -y install ssh`

<img width="651" height="112" alt="image" src="https://github.com/user-attachments/assets/c20deb35-16c5-437f-982a-3110b6fcba53" /> 

Seuraavaksi laitan SSH-demonin käynnistymään käynnistyksen yhteydessä ja laitan sen heti päälle. `sudo systemctl --now ssh` 

Seuraavaksi testataan sen toimivuus komennolla `ssh localhost` 

<img width="912" height="203" alt="image" src="https://github.com/user-attachments/assets/0a52ce03-d279-4e13-838c-c2b319f91429" />

Seuraavat kohdat olivat minulla jo tehtynä, mutta tässä ne tulevat, jotta voit automatisoida kirjautumisen ssh-avaimella ja sen testaus.

`ssh-keygen` ja paina Y, `ssh-copy-id localhost` ja `ssh localhost`

## d) Hei Ansible 

Aloitetaan lataamalla Ansible `sudo apt-get update` ja `sudo apt-get install ansible micro bash-completion tree` 

Pitkän latauksen jälkeen tehdään kansio Ansible konfigurointiin. `cd` , `mkdir ansible/` ja `cd ansible` 

<img width="321" height="18" alt="image" src="https://github.com/user-attachments/assets/861e6836-ecad-4293-af8a-652f44684460" />

Sitten luomme kansioon ansible host.ini ja sinne lisätään teksti local host. Eli `micro hosts.ini` kirjoitetaan sisälle localhost. Tallennetaan ctr+s ja poistutaan painamalla ctrl+q, tämän jälkeen vielä testaus `cat hosts.ini`

<img width="620" height="77" alt="image" src="https://github.com/user-attachments/assets/d1331ee6-b85b-46fa-b7d5-79e7c2059005" />

Testataan komentoa joka kertoo kauan Uptimen. `ansible all -a 'uptime' -i hosts.ini` 

<img width="1256" height="115" alt="image" src="https://github.com/user-attachments/assets/ffd3eb3f-5c54-4d12-bb28-fd63011b3d10" />


Seuraavaksi poistetaan kuvassa oleva python teksti ettei se tule joka kerta avataan `micro hosts.ini`

Lisätään sinne kehote [all:vars] ansible_python_interpreter=/usr/bin/python3 ja testataan uudestaan `cat hosts.ini`

<img width="475" height="85" alt="image" src="https://github.com/user-attachments/assets/86b57536-e5da-41d2-b760-bebc6d3d8156" />

ja Uptime uudestaan <img width="754" height="64" alt="image" src="https://github.com/user-attachments/assets/7dbc6bbc-22e6-478d-8e58-5b72e71c00f3" />

Sitten helpotetaan elämää ettei aina tarvitse kirjoittaa hosts.ini komentoa. `micro ansible.cfg` ja laitetaan sinne [defaults] inventory = hosts.ini 

Seuraavaksi testaus uptime komennolla ilman hosts.ini kohtaa. 

<img width="766" height="60" alt="image" src="https://github.com/user-attachments/assets/be92cd01-2984-43b4-92fa-9c00c37ef764" />

Nyt haluamme kaikkien tietokoneitten saavan roolin "Hello" Käytämme tähän site.yml mikä listaa mitkä ryhmät koneuta saavat mitkä roolit.

`micro site.yml` ja sinne

<img width="319" height="93" alt="image" src="https://github.com/user-attachments/assets/311a96e5-0e59-4cbc-bcb3-b9c34dd85bc6" />

Suoritin komennon ansible-playbook site.yml, joka antoi minulle error viestin 'Hello was not found in hakemistosta. 

Tämä on ohjeitten mukaan joten etenen seuraavaan kohtaan eli tehdään rooli Hello `mkdir -p roles/hello/tasks/`

`micro roles/hello/tasks/main.yml` ja kirjoitetaan sinne <img width="390" height="69" alt="image" src="https://github.com/user-attachments/assets/c0e21748-1ecd-402d-9cf9-92d004c5830d" />

Ajetaan koko pelikirja komennolla `ansible-playbook site.yml`

<img width="1212" height="444" alt="image" src="https://github.com/user-attachments/assets/bd4a48a2-e2ba-4554-9459-f2053fbf549d" />

Testataan vielä toisella tavalla muutoksiamme `ssh localhost 'cat /temp/hello-ansible` Tämä antaa vastauksen Tykkaan mansikoista niinkuin kuuluu

Seuraavaksi muokkaan ansible.cfg tiedostoa, jotta se näyttää minulle myös tehtävän argumentit.

`micro ansible.cfg` ja lisätään sinne display_args_to_stdout = true inventoryn alle

Sitten `ansible-playbook site.yml` 

<img width="1185" height="246" alt="image" src="https://github.com/user-attachments/assets/b3ff2e83-36a8-4ada-9838-5219b1c5c422" />

Ja lopputuloksemme saamme esille komennolloilla `tree -f`

<img width="1185" height="246" alt="image" src="https://github.com/user-attachments/assets/6969455d-9bbf-4738-b00f-980eda08de15" />

head -1000 ansible.cfg hosts.ini site.yml roles/hello/tasks/main.yml

<img width="1033" height="305" alt="image" src="https://github.com/user-attachments/assets/08972107-dc59-444c-9091-7a8201dd4913" />

## Lähteet

Geminiä käytetty suomentamiseen tiettyjä käsitteitä.

Karvinen, T 2026. SSH public key -Login without password luettavissa: https://terokarvinen.com/ssh-public-key-login-without-password/#install-ssh Luettu 3.11.2026

Karvinen, T.2026. Hello Ansible. Luettavissa: https://terokarvinen.com/hello-ansible/. Luettu 3.11.2026

Punnala, T. 2025. H1-installing-Linux. Luettavissa: https://github.com/punnalathomas/linux-course/blob/main/h1-installing-linux.md Luettu 3.11.2026
