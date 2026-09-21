# h5-Elokuu2026

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

