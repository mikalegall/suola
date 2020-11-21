# Salt peruskäyttö
Tero Karvisen [Configuration Management Systems](http://terokarvinen.com/2020/configuration-management-systems-palvelinten-hallinta-ict4tn022-autumn-2020/#h4-aikajana) opetuksesta versionhallinnassa kooditiedostona ylläpidetyn infrastruktuurin idempotency tilat

## H4a: Linux-paketinhallinnasta löytyvien ohjelmien asennus Linux etäkoneelle
YAML syntaksilla kahden välilyönnin väliaskelein (ei sarkain-tabulaattorilla) init.sls tiedosto hakemistopolussa /srv/salt/h4a/init.sls

	asenna_paljon_sovelluksia_paketinhallinnasta_etakoneelle:
	  pkg.installed:
	    - pkgs:
	      - httpie
	      - htop
	      - tree
	      - curl
	      - atool

![alt text](https://github.com/mikalegall/suola/blob/main/img/h4a_1_iac_versionhallintaan_tallennettavat_tiedostot.jpg "init.sls ja top.sls sijainnit")

YAML syntaksilla kahden välilyönnin väliaskelein (ei sarkain-tabulaattorilla) top.sls hakemistopolussa /srv/salt/top.sls

	base:
	  '*':
	    - moduuli1.tervehdys
	  'developmentServer':
	    - h4a
	  'testServer':
	    - apache2asennus
	  'proructionServer':
	    - moduuli2.asentelua

Manuaalinen asennus kohdennettuna heti kun etäkone on yhteydessä internettiin (tai sisäsverkossa saa yhteyden Master-palvelimelle) 

	sudo salt ’developmentServer’ state.apply 'h4a'

![alt text](https://github.com/mikalegall/suola/blob/ac563e7e3bb9612fa9fb05ffe5a032934a4ff414/img/h4a_2_asennettu.jpg "sudo salt ’developmentServer’ state.apply 'h4a'")

tai kaikkien koneiden osalta (kohdekone tarkistaa onko käskynjakoluettelossa sille kuuluvia osioita) idempotency tarkistus

	sudo salt ’*’ state.highstate

![alt text](https://github.com/mikalegall/suola/blob/main/img/h4a_2_asennettu.jpg "sudo salt ’*’ state.highstate")

## H4c: Tietyllä ajanjaksolla muuttuneiden tiedostojen havaitseminen
Otsikon mukainen lähestymistapa on ns. valkohattu hakkereille käytännöllinen myös [cracker:eita jäljitettäessä](https://rakenne.wordpress.com/2020/10/29/linux/#readteam) (esim. log- tai asennustiedostot). Komennon

	find /etc -printf '%T+ %p\n' | sort | tail

rakenteena on<br/>
find // Etsi<br/>
/etc // Hakemisto josta etsitään<br/>
-printf // Tulostaa hakutuloksen muotoiltuna<br/> 
%p // Tulostaa hakutuloksen standard outputtiin ("näytölle") rivittämättömänä<br/>
'%p' // ?Shell ei pääse komentotulkiksi (hipsukoiden ollessa tiukin sääntö) ? <br/>
'%p\n' // Rivinvaihdon merkkinnä on \n (eli vaihda riviä rivittämällä hakutulos helpommin luettavaan muotoon)<br/>
'%T' // Aikaleima [ISO-8601](https://fi.wikipedia.org/wiki/ISO_8601) muodossa<br/>
| // Vasemman puolen lopputuotos on [putkimerkin](https://www.linux.fi/wiki/Putki) oikean puolen syöte<br/>
sort // Laittaa aakkosjärjestykseen, jonka ansiosta nähdään helposti mitkä tiedostot ovat viimeksi muokattuja<br/>
| // Vasemman puolen lopputuotos on [putkimerkin](https://www.linux.fi/wiki/Putki) oikean puolen syöte<br/>
tail // Näyttää saamansa syötteen (esim. tiedosto tai hakutulos) viimeiset 10 riviä (head olisi 10 ensimmäistä riviä) 

## H4b: Linux etäkoneelle ohjelman asennus, joka ei läydy Linux-paketinhallinnasta
Perusprosessi on suorittaa manuaalisesti etäkoneelle vaihe-vaiheelta kaikki se käsin, jota etäkoneelle halutaan myöhemmin automatisoidusti esim. Salt toimiesta asentaa ([kilpailijoinaan mm. Chef ja Puppet](https://www.edureka.co/blog/chef-vs-puppet-vs-ansible-vs-saltstack/)). Tausta-ajatuksena siis se, että jokaisessa vaiheessa jäljitetään (aiemmin mainitulla "find /etc -printf '%T+ %p\n' | sort") tapahtuneet muutokset ja lopulta mallinnetaan sama lopputulos hallintatyökalun kautta.<br/>Koostamalla suorituskäskyt hallintatyökalun ymmärtämälle kielelle versionhallintaan saavutetaan samalla tilanne, jossa ylläpidossa (ns. Support toiminto) tiedetään täydellä varmuudella mitä millekin etäkoneelle on milloinkin asennettu. Lisäksi häiriönhallinnssa (ITIL Incident Management) kyetään tarvittaessa myös jopa ympäristö palauttamaan aiempaan (häiriöttömään) tilaan (rollback mahdollisuus sovellusten lisäksi myös infralle).

### Esimerkkiasennuksena Microsoft Visual Studio Code
Osoitteessa https://linuxize.com/post/how-to-install-visual-studio-code-on-ubuntu-18-04/ on (tilanteessa 20.11.2020) alla oleva ohje miten asentaa manuaalisesti VS code Ubuntulle.

	wget -q https://packages.microsoft.com/keys/microsoft.asc -O- | sudo apt-key add -
	sudo add-apt-repository "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main"
	sudo apt update
	sudo apt install code
	code

Rivi 1:<br/>
wget // Noutaa (tässä tapauksessa Microsoftin julkisen avaimen) ilmoitetusta osoitteesta lataamalla lähteen paikalliselle kiintolevylle<br/>
-q // Hiljainen tila eli poistaa tapahtumatuotoksen tulostamisen standard outputtiin<br/>
-O- // Liittynee katenointiin tulevalle putkitukselle<br/>
| // Vasemman puolen lopputuotos on [putkimerkin](https://www.linux.fi/wiki/Putki) oikean puolen syöte<br/>
sudo // "super user do" eli aja seuraava komento pääkäyttäjän oikeuksilla<br/>
apt-key // Avaintenhallinta työkalu<br/>
add // Lisää avain<br/>
&#45; // Ota syöte standard inputista (tässä tapauksessa tätä edeltävästä putkituksesta)<br/>
Eli rivin 1 tapahtuma on "Nouda annetusta nettiosoitteesta julkinen avain ja lisää se luotettujen avainten joukkoon.<br/>
HUOM! Lisäämällä avaimen avainrenkaaseen ilmoitat luottavasi (tässä tapauksessa Microsoftille) kyseiselle taholle pääkäyttäjäoikeudet ja annat luvan suorittaa kometoja koneellasi pääkäyttäjän roolissa!

Manuaalisesti avaimen nouto kohdekoneen kiintolevylle

	wget https://packages.microsoft.com/keys/microsoft.asc

Jatkossa ei enää tämän vuoksi tarvitse avata yhteyttä turvattomaan internettiin, kun se saadaan ensin talteen Masterille, voidaan se sieltä jaella edelleen orjille. Tietokoneella olevat avaimet

	apt-key list

![alt text](https://github.com/mikalegall/suola/blob/main/img/h4b_2_key.list_1.jpg "Luotettujen avainten tarkastelu ennen asennusta")

Manuaalisesti noudetun avaimen lisääminen manuaalisesti luotettavien avainten joukkoon

	sudo apt-key add microsoft.asc
	apt-key list

![alt text](https://github.com/mikalegall/suola/blob/main/img/h4b_2_key.list_2.jpg "Luotettujen avainten tarkastelu asennuksen jälkeen")

Hakutoiminnolla jäljittäminen mitä tiedostoa edellinen komento muutti (etc-hakemisto on ohjelmien asennusten konfigurointiin)

	sudo find /etc -printf '%T+ %p\n' | sort | tail

![alt text](https://github.com/mikalegall/suola/blob/main/img/h4b_4_find_1.jpg "Hakutulos viimeksi muuttuneista tiedostoista /etc hakemistossa")

Ilman .d päätettä oleva /etc/apt/trusted.gpg on binääritiedosto joka ei suurimmalta osin ole ihmisluettavassa muodossa, mutta siitä löytyy viitteitä Microsoftista ihmisluettavassa muodossa. 

![alt text](https://github.com/mikalegall/suola/blob/main/img/h4b_3_binaarimossoa.jpg "Sotkua")


.d päätteinen hakemisto viittaa daemonin käyttämään yksittäisten muutosten hakemistoon. Siellä ei kuitenkaan ole avainrenkaissa havaittavissa muutosta.

![alt text](https://github.com/mikalegall/suola/blob/main/img/h4b_5_trusted.gpg.d_1.jpg "trusted.gpg.d avainrenkaat")

Tarkoituksenmukaisnen hallintatapa etätietokoneilla on kuitenkin konfigurointimuutosten hallinnointi .d hakemistojen kautta eristettyinä yksittäisinä muutoksina, joten lopputuloksessa etäkoneelle tullaan jo valmiiksi netistä noudettu microsoft.asc asettamaan .d hakemistoon (/etc/apt/trusted.gpg.d/), josta daemon käyttöönottaa yksittäisiksi eristetyt muutokset. Poistetaan ensin äsken asennettu avain yksilöimällä se sen hexa arvolla

	sudo apt-key del BC52 8686 B50D 79E3 39D3  721C EB3E 94AD BE12 29CF

![alt text](https://github.com/mikalegall/suola/blob/main/img/h4b_2_key.list_3.jpg "Luotettujen avainten tarkastelu poiston jälkeen")

Luodaan sen jälkeen .d päätteiseen daemonin tarkastamaan konfigurointihakemistoon yksittäinen luotettu avain Microsoftille "gpg" päätteellä, jonka jälkeen sen hallinnointikin on selkeämpää jälkikäteen, etenkin henkilöstön vaihdoksia silmälläpitäen. Komento annetaan siinä hakemistossa, jossa juuri noudettu "microsoft.asc" tiedosto sijaitsee (tai viitataan tarkalla polulla juuresta alkaen)

	sudo apt-key --keyring /etc/apt/trusted.gpg.d/microsoft.gpg add microsoft.asc

	sudo find /etc -printf '%T+ %p\n' | sort | tail

![alt text](https://github.com/mikalegall/suola/blob/main/img/h4b_4_find_2.jpg "Hakutulos viimeksi muuttuneista tiedostoista avaimen tekemisen jälkeen")


	apt-key list

![alt text](https://github.com/mikalegall/suola/blob/main/img/h4b_2_key.list_4.jpg "Luotettujen avainten tarkastelu avaimen tekemisen jälkeen")

Luottamuksen Microsoftiin synnyttyä (ei ehkä kaikkein viisainta, mutta aina ei ole valinnanvaraakaan) voidaan jälkikäteen muidenkin toimesta se nyt helposti havaita, ettei se jää salasuhteeksi vain /etc/apt/trusted.gpg binääritiedoston sekamelskaan

	ls -la /etc/apt/trusted.gpg.d

Tildellä (matomerkki ~) varustetun väliaikaisen temp-tiedoston voi varmuuden vuoksi poistaa, ettei Mikkis jää salakavalasti koneelle kummittelemaan mikäli myöhemmin tilanteen muuttuessa tulemme suhteen yksipuolisesti päättämään

	 sudo rm /etc/apt/trusted.gpg.d/microsoft.gpg~


Rivi 2:<br/>
sudo // "super user do" eli aja seuraava komento pääkäyttäjän oikeuksilla<br/>
add-apt-repository // Lisää turvalliseen paketinhallinta työkaluun APT uusi "ei niin turvallinen" etäsijainti paikallisesti tallennetuksi murupolkuun /etc/apt/sources.list<br/>
"deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main" // Lisättävän sijainnin yksilöinti


Microsoft Visual Studio Coden sijainnin lisäys paikalliselle (asennus)kohdekoneelle

	sudo add-apt-repository "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main"

APT-paketinhallintatyökalun (tieto)varastojen (repo) sijaintien tarkistus

	sudo find /etc -printf '%T+ %p\n' | sort | tail


![alt text](https://github.com/mikalegall/suola/blob/main/img/h4b_4_find_3.jpg "Hakutulos viimeksi muuttuneista tiedostoista repositoryyn lisäyksen jälkeen")


Manuaalisen lisäyksen jälkeen APT-paketinhallintatyökalun (tieto)varastojen (repo) sijaintien tarkistus

	less /etc/apt/sources.list

 osoittaa VS Coden ilmestyneen paikalliseen lähdeluetteloon. Samassa yhteydessä oleva .save päätteinen tiedosto

	less /etc/apt/sources.list.save

lienee vain väliaikainen temp-työtiedosto, koska siinä on alkuperäisen tiedoston sisältö ilman Microsoftia.<br/>
Tarkoituksenmukaisnen hallintatapa etätietokoneilla on kuitenkin konfigurointimuutosten hallinnointi .d hakemistojen kautta eristettyinä yksittäisinä muutoksina, joten lopputuloksessa etäkoneelle tullaan Microsoftin asettama lisätieto erottamaan .d hakemistoon (/etc/apt/sources.list.d/), josta daemon käyttöönottaa yksittäisiksi eristetyt muutokset. Leikataan "sources.list" tiedostosta kokonaan pois leikepöydälle muistiin Microsoftia koskevat rivit ja lisätään ne omaan tiedostoonsa

	sudoedit /etc/apt/sources.list
	#copy (remove)
	sudoedit /etc/apt/sources.list.d/microsoft.list
	sudo rm /etc/apt/sources.list.save


Rivi 3:<br/>
sudo // "super user do" eli aja seuraava komento pääkäyttäjän oikeuksilla<br/>
apt // Paketinhallintajärjestelmä<br/>
update // Saattaa ajantasalle välimuistin (cache) noutamalla listatuista repoista tiedot niissä olevista asennuspaketeista

	sudo apt update


Rivi 4:<br/>
sudo // "super user do" eli aja seuraava komento pääkäyttäjän oikeuksilla<br/>
apt // Paketinhallintajärjestelmä<br/>
install // Asenna<br/>
code // Asennettavan ohjelman nimi<br/>

	sudo apt install code


Rivi 5:<br/>
code // Käynnistää asennetun VS code ohjelman

![alt text](https://github.com/mikalegall/suola/blob/main/img/h4b_6_lopputulos_1.jpg "Manuaalisen asennuksen lopputulos")

Manuaalisen asennuksen vaiheiden tarkoituksenmukaistamisen jälkeen (muutokset ja niiden sijainnit otettuna talteen) poistetaan kaikki tähän liittyen tehdyt asennukset ja tehdään koneesta Salt Slave

![alt text](https://github.com/mikalegall/suola/blob/main/img/h4b_7_purge.jpg "Purge")


### Käsityön automatisointi Salt Master palvelimella
Nyt voi tapahtumat kääntää Salt kielelle, Salt Master-palvelimella, ja monistaa toiminto lukemattomille samanlaisille koneille versionhallinnassa olevien tiedostojen kautta automatisoituna.<br/>

	sudoedit /srv/salt/h4b/init.sls


	#Anna root oikeudet avaimen haltijalle
	/etc/apt/trusted.gpg.d/microsoft.gpg:
	  file.managed:
		- source: salt://h4b/microsoft.gpg

	#Lisä ulkoinen repo
	/etc/apt/sources.list.d/microsoft.list:
	  file.managed:
		- source: salt://h4b/microsoft.list

	#Päivitä repojen tiedot ja asenna ohjelma
	code:
	  pkg.installed:
		- refresh: True


Lisätään vielä etäkoneen idempotency

	sudoedit /srv/salt/top.sls


	base:
	  'ubuntu20Desktop'
		- h4b

Masterin asennusmoduliin "h4b" tulee siirtää tiedostot microsoft.gpg ja microsoft.list, josta ne kopioidaan Slave koneille

![alt text](https://github.com/mikalegall/suola/blob/main/img/h4b_8_salt.jpg "Saltilla konfigurointia")

	sudo salt 'ubuntu20Desktop' state.apply 'h4b'

![alt text](https://github.com/mikalegall/suola/blob/main/img/h4b_6_lopputulos_2.jpg "Automatisoidun asennuksen lopputulos")

[Poc](https://fi.wikipedia.org/wiki/Konseptitodistus):issa tehtiin Azureen Linux Ubuntu 18 Server ja Home PC:lle (Win10) VirtualBoxiin Ubuntu 20 Desktop