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

	find /etc -printf '%T+ %p\n' | sort

rakenteena on<br/>
find // Etsi<br/>
/etc // Hakemisto josta etsitään
-printf // Tulostaa hakutuloksen muotoiltuna 
%p // Tulostaa hakutuloksen standard outputtiin ("näytölle") rivittämättömänä
'%p' // ?Shell ei pääse komentotulkiksi (hipsukoiden ollessa tiukin sääntö) ? 
'%p\n' // Rivinvaihdon merkkinnä on \n (eli vaihda riviä rivittämällä hakutulos helpommin luettavaan muotoon)
'%T' // Aikaleima [ISO-8601](https://fi.wikipedia.org/wiki/ISO_8601) muodossa
| // Vasemman puolen lopputuotos on [putkimerkin](https://www.linux.fi/wiki/Putki) oikean puolen syöte
sort // Laittaa aakkosjärjestykseen, jonka ansiosta nähdään helposti mitkä tiedostot ovat viimeksi muokattuja

## 
