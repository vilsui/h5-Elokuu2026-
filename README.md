Tunkeutumistestaus, Tero Karvinen 
Tunkeutumistestaus - ICI005AS3A-3007 - tt7 - 2026p1 - Tero - to 11:00 pa5001
Ville Suikki

h5-Elokuu2026

x) Lue/katso ja tiivistä. (Tässä x-alakohdassa ei tarvitse tehdä testejä tietokoneella, vain lukeminen tai kuunteleminen ja tiivistelmä riittää. Tiivistämiseen riittää muutama ranskalainen viiva kustakin artikkelista. Kannattaa lisätä myös jokin oma ajatus, idea, huomio tai kysymys.)

Cracking Passwords with Hashcat

- Järjestelmät tallentavat sasanojen sijasta yksisuuntaisia tiivisteitä eli hasheja. Näoitä voidaan murtaa kokeilemalla (brute force) automaattisesti miljoonia sanakirjasanoja kunnes oikea salasana löytyy.
- Työkaluna käytetään Hashcattia ja sen apuna laajaa salasanakirjastoa, kuten rockyou.txt minkä tiivisteen tyyppi on tunnistettava ensin hashid työkalulla.
- Hashcat kokeilee salasanoja erittäin nopeasti (parametri -m) ja onnistuessaan se tallentaa murretun salasanan selväkielisienä haluttuun tiedostoon kuten "o - solved"
- Suurin laskentateho/murtamisnopeus saavutetaan käyttämällä fyysisen isäntäkoneen (ei virtuaalikoneen) näytöohjainta virtuaalikoneen prosessorin sijaan. Huom. Testejä saatehdä vain omiin tai erikseen salittuihin kohteisiin (eettisyys)

Huomiona tässä: Artikkelin esimerkissä hashia kokeiltiin 37 miljoonan sanan sekuntivauhdilla pelkällä perustason koneella, joten ei ihmekkään että yksinekrtaiset salasanat murretaan erittäin helposti ja korostaa monimutkaisten salasanojen ja MFA'n tärkeyttä. 

Crack File Password With John

- John the ripper kykenee murtamaan sanakirjahyökkäyksillä monien salasanasuojattujen tiedostomuotojen kuten ZIP, PDF ja office tiedostojen salasanoja
- Käyttöjärjestelmien omat paketinhallinnat eivät aina sisällä täyttä Jumbo-versiota, se ladataan suoraan löhdekoodina (esim. Git) ja kompilpidaan itse komennolla make. Tarvitaan C-kääntäjä, useita ohjelmointikirjastoja kuten libssl-dev ja zlib1g-dev sekä muutama gigatavu keskusmuistia.
- Zip paketin salasanan murtaminen Johnilla on kaksivaiheinen. Ensin tiedostosta eristetään sen sisältämä tiiviste "hash", hydyntäen erityistö purkutyökalua kuten zip2john, ja tallennetaan se erilliseen tekstitiedostoon.
- Toisessa vaiheessa john-työkalu  ohjataan hyökkäämään uutettua tiivistetiedostoa vastaan. Jos salasanana on käytetty yleistä tai sanakirjasta löytyvää sanaa, ohjelma löytää ja tulostaaa sen.
- Sanakirjahyökkäykset perustuvat yleisesti käytettyjen salanojen ja listojen hyödyntämiseen eivätkä tehoa riittävän vahvasti generoituihin monimutkaisiin tai koneellisesti generoituihin tyäsin satunaisiin salasanoihin.


Huomiona
Kiinnostavaa huomata ero miten suorien salasanojen ja tiedostojen salausten murtaminen eroaa toisistaan vaikka taustalla oleva periaate on sama. Tiedostoihin kohdistuvat hyökkäykset vaatii ylimääräisen välivaiheen kuten Zip2john. 

A) Olen asentanut Kalin koneeseeni ja käytän UTM'n kautta Kali linux ARM64 QEMU 10.0 ARM virtual machine versiota. Käytössäni Macbook Pro 2026. Asensin Kalin imagen kautta. Ohessa vielä versio komennolla cat /etc/os-release cat /etc/os-release 

B) Kalin irrottaminen verkosta
Irrotin kalin verkosta valitsemalla kalin sammutuksen jälkeen että asetus oli vain host only. Tein tämän jälkeen ping -4 8.8.8.8 jonka tukoksena oli seuraava <img width="3024" height="1964" alt="image" src="https://github.com/user-attachments/assets/49bc5662-a98b-403d-8795-c90b3563b60d" />

C) Avasin kalin komentorivin ja ajointehtvänannon komennon "nmap -T4 -A localhost"

- nmap:Työkalu (Network Mapper), joka on alan standardi verkkoskannaukseen. Ilman erillistä porttimääritystä (kuten -p-) se skannaa oletuksena aina 1000 yleisintä TCP-porttia.

- -T4: Ajoitusmalli eli Timing template. Nmapin nopeusasteikko on T0–T5. T4 tarkoittaa nopeaa skannausta, joka olettaa verkkoyhteyden olevan luotettava. Se nopeuttaa tulosten saamista huomattavasti verrattuna oletukseen (T3).

- -A rankempi skannaus, joka on  "all-inclusive" -parametri, joka kytkee kerralla päälle käyttöjärjestelmän tunnistuksen (OS detection), palvelujen versiotunnistuksen (Version detection, -sV), nmapin oletusskriptit (Script scanning, -sC) sekä reitityksen jäljityksen (Traceroute). Mahdollistaa saamaan pelkän portin tilan (auki/kiinni) lisäksi selville mahdollisimman tarkasti, mikä ohjelmisto portin takana pyörii.

- localhost: Skannauksen kohde. Tarkoittaa konetta itseään (IP-osoite 127.0.0.1).
  
<img width="3024" height="1964" alt="image" src="https://github.com/user-attachments/assets/91fc5c27-ff10-4465-99a4-206b5da1b2e9" />

- Nmapin tulosteessa lukee: Not shown: 1000 closed tcp ports (reset). Tämä tarkoittaa, että Kali-koneellani ei ole tällä hetkellä yhtään palvelua kuuntelemassa verkkoliikennettä.

- D) Muutin UTM'stä asetukset että verkko on "jaettu verkko" takaisin ja käynnistin Kalin uudestaan.

- Asennetaan demonit:
Asensin kaksi perinteistä ja selkeää demonia: Apache2 (web-palvelin) ja vsftpd (FTP-tiedostonsiirtopalvelin). Komennolla:
sudo apt update && sudo apt install apache2 vsftpd -y

<img width="3024" height="1964" alt="image" src="https://github.com/user-attachments/assets/9cc7a4e9-bbae-45d7-b75e-0ccdc2d09172" />

Käynnistin demonit:
varmistin, että palvelut ovat päällä ja kuuntelevat portteja komennoilla:
sudo systemctl start apache2
sudo systemctl start vsftpd

skannattiin uudelleen:
nmap -T4 -A localhost

<img width="3024" height="1964" alt="image" src="https://github.com/user-attachments/assets/47d3de6a-2a23-47ad-b0ee-539b9f93be18" />

D) Avoimet portit: Ensimmäisessä skannauksessa kaikki 1000 porttia olivat kiinni. Demoneiden käynnistämisen jälkeen Nmap löysi kaksi avointa porttia: portin 21 (FTP) ja portin 80 (HTTP). Loput 998 porttia ovat edelleen kiinni.

Palvelujen ja versioiden tunnistus: Koska käytin  -A-parametria, Nmap ei tyytynyt vain toteamaan porttien olevan auki. Se keskusteli palveluiden kanssa ja selvitti tarkat ohjelmistoversiot: portissa 21 pyörii vsftpd 3.0.5 ja portissa 80 Apache httpd 2.4.68.

Skriptien tuoma lisätieto: Parametri -A sisältää myös oletusskriptien ajamisen. Portin 80 tulosteessa näkyy, kuinka Nmap on hakenut web-palvelimelta sivun otsikon (|_http-title: Apache2 Debian Default Page: It works).

Hyökkäyspinta-alan kasvu ja ajankäyttö: Ensimmäinen skannaus kesti todennäköisesti vain sekunnin murto-osan. Tämä skannaus kesti reilu 20 sekuntia, koska Nmap joutui todella tutkimaan avoimia portteja ja päättelemään käyttöjärjestelmää. Tulokset osoittavat konkreettisesti, miten jokainen käynnissä oleva verkkopalvelu luo koneelle uuden rajapinnan ja siten potentiaalisen hyökkäyspinta-alan.

<img width="3024" height="1964" alt="image" src="https://github.com/user-attachments/assets/522502f5-82e1-4575-b723-45e136c33f88" />

E) Avasin Kalin terminaalin ja loin ensin tavallisen tekstitiedoston komenolla "echo "Tämä on erittäin salainen viesti" > salaisuus.txt"
Pakkasin ja salasin tiedoston 7-Zipillä käyttäen salasanaa "snoopy" (joka löytyy  sanalistoista) komennolla: 7z a -psnoopy kohde.7z salaisuus.txt

<img width="3024" height="1964" alt="image" src="https://github.com/user-attachments/assets/3191b356-7641-4899-ba11-ee8fcf58a475" />

Nyt lukittu tiedosto nimeltä kohde.7z. Poistetaa. alkuperäinen tekstitiedosto, jotta se ei "paljasta" sisältöä komenolla: rm salaisuus.txt.

F) Avassin Kalin terminaalin ja loin tiivisteen sanasta "iloveyou" (joka löytyi RockYou-listalta). Komento laskee tiivisteen ja tallentaa sen tekstitiedostoon. Tässä oli aljon säätöä 

<img width="2784" height="1904" alt="image" src="https://github.com/user-attachments/assets/49bfefd5-a3a3-430a-bf5d-774645fa06dd" />

E) Salasanatiivisteen luominen ja murtaminen (SHA-256)
Tehtävän tarkoituksena oli luoda itse salasanatiiviste ja murtaa se sanakirjahyökkäyksellä. Kohtasin prosessin aikana muutamia teknisiä haasteita, jotka opettivat komentorivin tarkkaa käyttöä ja virtuaalikoneen rajoitteita.

1. Tiivisteen luonti

Korjasin syntaksin muotoon echo -n "iloveyou" | sha256sum | awk '{print $1}' > sha256_tiiviste.txt. Tämä syötti sanan oikein sha256sum-ohjelmalle ja loi tiedostoon oikean SHA-256-tiivisteen.

2. Sanalistan (rockyou.txt) hankinta

Yritin käyttää Kalin oletussanalistaa, mutta törmäsin puuttuviin tiedostoihin. Kun yritin ladata listan wget-komennolla verkosta, sain virheitä kuten zsh: unknown file attribute: h ja 404 Not Found. Nämä johtuivat komentorivin kopiointivirheistä ja vanhentuneesta URL-osoitteesta.

Latasin lopulta valmiiksi puretun sanalistan toimivasta lähteestä komennolla:
wget [https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt](https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt)

F) Tiivisteen murtaminen

Kun ajoin Hashcatin komennolla hashcat -m 1400 sha256_tiiviste.txt rockyou.txt, ohjelma palautti virheen No devices found/left. Tämä johtui siitä, että UTM-virtuaalikoneellani ei ollut Hashcatin oletuksena vaatimaa GPU-kiihdytystä tai prosessorin OpenCL-ajureita asennettuna.

Asensin Kalin pakettienhallinnasta prosessorille tarkoitetun OpenCL-ajurin komennolla sudo apt install pocl-opencl-icd. (Tämän jälkeen murtokomento meni onnistuneesti läpi).

Tulos: Ohjelma suoritti sanakirjahyökkäyksen onnistuneesti ja paljasti (Status: Cracked), että tiivisteen taustalla oleva alkuperäinen salasana oli "iloveyou".


Lähteet:
