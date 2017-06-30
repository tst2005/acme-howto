
Résumé de ce que l'on souhaite:
- un user www-data (groupe www-data) pour faire tourner le frontal web (nginx)
- un user acme (groupe acme) pour faire tourner l'outil letsencrypt (dehydrated)
- un espace accessible par l'url /.well-known/acme-challenge/
  et écrivable par l'utilisateur acme
- des certiciats et clés lisible par le frontab web
- un mecanisme pour reloader les certificats sur le frontal web lorsqu'ils changent



Créer l'user acme
	adduser --system --home /home/acme --shell /bin/bash --disabled-password acme
	groupadd --system acme
	usermod -g acme acme

Créer le repertoire accessible en ecriture par acme
	mkdir /var/www/acme-challenge
	chown acme:www-data /var/www/acme-challenge
	chmod u+rwx,go-rw+x /var/www/acme-challenge

# drwxr-x--x 2 acme www-data 4096 Jun 12 23:39 /var/www/acme-challenge/



	su - acme

Telecharger l'outil (dehydrated)
	mkdir repo
	cd repo
		git clone $GITHUB_dehydrated_URL dehydrated
Choisir une version précise (la stable la plus récente)
		cd dehydrated
			git tag
			git checkout v0.4.0
	cd ~

L'outil aime travailler avec tout ses fichiers a coté de lui
Nous utiliserons une copie statique de l'outil (pas de mise a jour automatique directe de l'outil)

	mkdir acme-util
	cp -a repo/dehydrated/dehydrated acme-util/dehydrated-v0.4.0




	# wget letsencrypt.sh

	./letsencrypt-test.sh --env

	./letsencrypt-test.sh --register --accept-terms
# INFO: Using main config file config
+ Generating account key...
+ Registering account key with ACME server...
+ Done!

	./letsencrypt-test.sh --cron

si tout va bien

modif de la config nginx, activer l'https avec les certificats ssl (acme-test/certs/...)


  380  cd /etc/
  381  git diff nginx/sites-available/wmfr
  382  git ci -m 'acme-challenge nginx config (letsencrypt)' nginx/sites-available/wmfr
  383  git st
  384  git ci -a -m 'add acme account'
  385  git st
  386  git diff
  387  exit

  405  tail -f /var/log/nginx/error.log
  407  exit

  408  cd /etc/nginx/
  409  vim nginx.conf
  410  vim nginx-prod.conf 
  411  vim prod/http.conf 
  412  vim sites-enabled/*
  413  vim sites-enabled/10-wmfr 
  414  vim include/acme-challenge
  415  vim sites-enabled/10-wmfr 
  416  vim sites-enabled/*
  417  /etc/init.d/nginx reload
  418  tail /var/log/nginx/error.log
  419  exit

  420  less /etc/nginx/sites-enabled/*jk*
  421  cd /etc/nginx/
  422  cd include/
  425  cp -a ssl ssl-options
  426  cp -a ssl ssl-wmfr
  427  cp -a ssl ssl-jk
  428  vim ssl-jk 
  429  vim ssl-options
  430  vim ssl-jk 
  431  vim ssl
  432  vim  ssl-wmfr 
  433  ls -l
  434  cd ..
  437  vim sites-enabled/*
  438  cd sites-enabled/
  439  ls -l
  440  cd ..
  441  /etc/init.d/nginx configtest
  442  less /var/log/nginx/error.log
  443  vim /etc/nginx/include/ssl
  444  /etc/init.d/nginx configtest
  445  less /var/log/nginx/error.log
  446  vim sites-enabled/*jk*
  447  vim include/ssl._disabled_
  448  vim sites-enabled/*jk*
  449  /etc/init.d/nginx configtest
  450  /etc/init.d/nginx reload
  451  /etc/init.d/nginx reload
  452  /etc/init.d/nginx restart
  453  exit

 468  cd /etc/nginx/
  470  vim include/ssl
  476  /etc/init.d/nginx configtest
  479  less /var/log/nginx/error.log

  477  /etc/init.d/nginx reload
  480  /etc/init.d/nginx restart

  481  vim include/ssl
  482  vim include/ssl-wmfr
  483  vim include/ssl-jk
  484  /etc/init.d/nginx configtest
  485  less /var/log/nginx/error.log
  486  vim /etc/nginx/include/ssl
  487  less /var/log/nginx/error.log
  488  vim /etc/nginx/include/ssl-wmfr
  489  /etc/init.d/nginx configtest
  490  /etc/init.d/nginx configtest
  491  vim /etc/nginx/include/ssl-jk
  492  vim include/ssl-
  493  vim include/ssl-options
  494  git mv include/ssl{,._disabled_}
  496  /etc/init.d/nginx configtest
  497  /etc/init.d/nginx reload
  498  vim sites-enabled/*jk*
  499  less /etc/nginx/sites-enabled/*

