# Salt peruskäyttö
Tero Karvisen [Configuration Management Systems](http://terokarvinen.com/2020/configuration-management-systems-palvelinten-hallinta-ict4tn022-autumn-2020/#h4-aikajana) opetuksesta versionhallinnassa kooditiedostona ylläpidetyn infrastruktuurin idempotency tilat

## Linuxille paketinhallinnasta löytyvien ohjelmien asennus etäkoneelle
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

## 

