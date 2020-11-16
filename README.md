# IaC idempotency (Salt)
Tero Karvisen [opissa](http://terokarvinen.com/2020/configuration-management-systems-palvelinten-hallinta-ict4tn022-autumn-2020/ "Configuration Management Systems") saadut etätehtävät MarkDownilla tallennettuna ([Cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet))

##Tapahtumahistoria

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


##git reset –hard

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