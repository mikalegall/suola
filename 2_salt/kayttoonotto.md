# Salt peruskäyttö
Tero Karvisen [Configuration Management Systems](http://terokarvinen.com/2020/configuration-management-systems-palvelinten-hallinta-ict4tn022-autumn-2020/#h4-aikajana) opetuksesta versionhallinnassa kooditiedostona ylläpidetyn infrastruktuurin idempotency tilat

## Linuxille paketinhallinnasta löytyvien ohjelmien asennus
YAML syntaksilla kahden välilyönnin väliaskelein (ei sarkain-tabulaattorilla)

	asenna_paljon_sovelluksia_paketinhallinnasta_etakoneelle:
	pkg.installed:
		- pkgs:
		- httpie
		- htop
		- tree
		- curl
		- atool

## 