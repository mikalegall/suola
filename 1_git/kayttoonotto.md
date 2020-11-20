# Versionhallinnan peruskomennot

Git-versionhallinnan tarkkailemassa paikallisessa hakemistossa kaikkien muuttuneiden tiedostojen valmistelu (lisäämällä ne kaikki seuraavaan lähetykseen mukaan) versionhallinnan jaetulle etäpalvelimelle lähetettävksi tapahtuu komenno

  git add .

Muutostapahtumaan liitetään tieto sisällön muutoksesta samoin kuin vaikkapa postipakettiin liimattaisiin päälle "sisältää särkyvää" tarra, tai päivittäistavarakaupassa säilykkeeseen tulee hintalappu. Informatiivinen lisäys auttaa sen lukijaa. Versionhallinnassa vakiintunut tapa on antaa muutostieto käskymuotona (siis ei imperfektissä mitä tapahtui) lyhyesti englannin kielellä. Ei siis "vaihdoin painikkeen taustavärin punaiseksi" vaan

	git commit -m"Change button background to red"

Ylipitkänä "Change the background color of the button to red" on tarpeeton sillä asian ymmärtää paljon lyhyemminkin. Lopulta muuttuneiden tiedostojen muutokset työnnetään versionhallinnan jaetulle yhteiskäyttöpalvelimelle nähtäväksi

	git push

josta ne on muiden vedettävissä omalle tietokoneelleen

	git pull


## Tapahtumahistoria

Komento 'git log' näyttää viimeisimpiä komentoja esim. lisämääreellä 'git log -3'

	$ git log
	commit 9597762ae55f38f34e9a25a5fae5ceb610b7f8d5 (HEAD -> main, origin/main, origin/HEAD)
	Author: Mika L
	Date:   Mon Nov 16 13:13:15 2020 +0200

		Use markdown also for URL's

	commit 065e104f5bf6f011e8dd7c55f9d78f5c8f13b327
	Author: Mika L
	Date:   Mon Nov 16 12:59:52 2020 +0200

		add source and give credit

	commit 1908b1efb867d80c458832cf4c0c7728aeec7d4f
	Author: mikalegall <123456+mikalegall@users.noreply.github.com>
	Date:   Mon Nov 16 12:54:29 2020 +0200

		Initial commit

	/HELIA/Palvelinten hallinta/GIT/suola (main)$


## Muutosten kumoaminen peruuttamattomasti (git reset –hard)

Typotin lokaalin commitin puskematta sitä etärepoon. Otin tämän tiedoston sisällön copy+pastella leikepöydälle.

	$ git status
	On branch main
	Your branch is up to date with 'origin/main'.

	Changes not staged for commit:
	  (use "git add <file>..." to update what will be committed)
	  (use "git restore <file>..." to discard changes in working directory)
			modified:   README.md

	no changes added to commit (use "git add" and/or "git commit -a")

	/HELIA/Palvelinten hallinta/GIT/suola (main)$ git add .

	/HELIA/Palvelinten hallinta/GIT/suola (main)$ git commit -m"fix link, add goit log"
	[main 8e59418] fix link, add goit log
	 1 file changed, 25 insertions(+), 3 deletions(-)

	/HELIA/Palvelinten hallinta/GIT/suola (main)$


Tarkistin edellisen validin commitin tunnisteen 'git log -2' saaden vastaukseksi '9597762ae55f38f34e9a25a5fae5ceb610b7f8d5' ja ajoin sillä tunnisteella varustettuna komennon 'git reset --hard 9597762ae55f38f34e9a25a5fae5ceb610b7f8d5', jolla sain kaikki nykyiset muutokset kumottua ja palautettua työtiedoston tilaan, jossa oltiin ennen sähläystä. 

	$ git log -2
	commit 8e5941853722023ea10f85362e6b2cb2faa5e59b (HEAD -> main)
	Author: Mika L
	Date:   Mon Nov 16 17:12:21 2020 +0200

		fix link, add goit log

	commit 9597762ae55f38f34e9a25a5fae5ceb610b7f8d5 (origin/main, origin/HEAD)
	Author: Mika L
	Date:   Mon Nov 16 13:13:15 2020 +0200

		Use markdown also for URL's

	/HELIA/Palvelinten hallinta/GIT/suola (main)$ git reset --hard 9597762ae55f38f34e9a25a5fae5ceb610b7f8d5
	HEAD is now at 9597762 Use markdown also for URL's


	$ git reset --hard 9597762ae55f38f34e9a25a5fae5ceb610b7f8d5
	HEAD is now at 9597762 Use markdown also for URL's

	 /HELIA/Palvelinten hallinta/GIT/suola (main)$

Lopulta liitin vanhan tiedoston päälle leikepöydältä muutokseni ja puskin uuden version etärepoon


## Eroavaisuuksien selvittäminen (git diff)

Kun ohjelmakoodi kosahtaa tuotannossa, ja muutosta jäljitetään, voidaan kahta tallennettua tekstisisältöä vertailla komennolla 'git diff'. Ensin selvitin kahden viimeisimmän commitin tunnisteet (git log -2)

	Administrator@ACADEMY-7115W4W MINGW64 ~/Downloads/HELIA/Palvelinten hallinta/GIT/suola (main)
	$ git log -2
	commit dc0473d7a6d41c374b7e6a3c6dbb15fd2397b238 (HEAD -> main, origin/main, origin/HEAD)
	Author: Mika L
	Date:   Mon Nov 16 17:34:49 2020 +0200

		try to do some format

	commit e988e8fd19e268fe62edb6881a9a2749f87a7933
	Author: Mika L
	Date:   Mon Nov 16 17:31:50 2020 +0200

		fix link, add git log

ja sitten vertailin niitä 'git diff dc0473d7a6d41c374b7e6a3c6dbb15fd2397b238 e988e8fd19e268fe62edb6881a9a2749f87a7933'

	/HELIA/Palvelinten hallinta/GIT/suola (main)$ git diff dc0473d7a6d41c374b7e6a3c6dbb15fd2397b238 e988e8fd19e268fe62edb6881a9a2749f87a7933
	diff --git a/README.md b/README.md
	index b438b66..5a40858 100644
	--- a/README.md
	+++ b/README.md
	@@ -2,7 +2,6 @@
	 Tero Karvisen [opissa](http://terokarvinen.com/2020/configuration-management-systems-palvelinten-hallinta-ict4tn022-autumn-2020/ "Configuration Management Systems") saadut etätehtävät MarkDownilla tallennettuna ([Cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet))

	 ##Tapahtumahistoria
	-
	 Komento 'git log' näyttää viimeisimpiä komentoja esim. lisämääreellä 'git log -3'

			$ git log
	@@ -28,7 +27,6 @@ Komento 'git log' näyttää viimeisimpiä komentoja esim. lisämääreellä 'gi


	 ##git reset –hard
	-
	 Typotin lokaalin commitin puskematta sitä etärepoon. Otin tämän tiedoston sisällön copy+pastella leikepöydälle.

			$ git status

	/HELIA/Palvelinten hallinta/GIT/suola (main)$

## Syyllisen esiin kaivaminen (git blame)

Kuten vakuutusyhtiöiden korvauspäätöksissä, täytyy toimenpiteiden kohdistua johonkin. Siispä epätarkoituksenmukaisen koodirivin pätkän tallentaja kaivetaan esiin versiointihistoriasta rivin tarkkuudella. Tässä tynkä esimerkissä ollaan kiinnostuneita rivien 2-3 muokkaajasta 'git blame -L 2,3 README.md'


	/HELIA/Palvelinten hallinta/GIT/suola (main)$ git blame -L 2,3 README.md
	e988e8fd (Mika L 2020-11-16 17:31:50 +0200 2) Tero Karvisen [opissa](http://terokarvinen.com/2020/configuration-management-systems-palvelinten-hallinta-ict4tn022-autumn-2020/ "Configuration Management Systems") saadut etätehtävät MarkDownilla tallennettuna ([Cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet))
	e988e8fd (Mika L 2020-11-16 17:31:50 +0200 3)

	/HELIA/Palvelinten hallinta/GIT/suola (main)$


