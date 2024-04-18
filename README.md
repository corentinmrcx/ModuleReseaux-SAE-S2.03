# Modules Réseaux : SAÉ S2.03
### MARCOUX Corentin et PERICHARD Romaric
**Enseignant de TD :** ALEXANDRE-BARFF Welcome
# Sommaire
1. [Gestion des services : systemd](#1-gestion-des-services--systemd)
2. [Serveur Web Apache](#2-serveur-web-apache)

    2.1 [Configuration de base](#21-configuration-de-base)

    2.2 [Les serveurs virtuels](#22-les-serveurs-virtuels)
3. [Serveur Web sécurisé HTTPS](#3-serveur-web-sécurisé-https)
4. [Langage de programmation PHP](#4-langage-de-programmation-php)
5. [Serveur de base de données MySQL](#5-serveur-de-base-de-données-mysql)
6. [Outil d'administration de bases de données phpMyAdmin](#6-outil-dadministration-de-bases-de-données-phpmyadmin)
7. [Installation de la plateforme collaborative MediaWiki](#7-installation-de-la-plateforme-collaborative-mediawiki)

# 1. Gestion des services : systemd
- Manuel de la commande **systemctl** : `man systemctl` 
- Affichage de la liste des services démarrés : `systemctl`
- Installation du paquet **sshd** : `sudo apt-get install openssh-server` 
- Vérification du démarrage du service **sshd** : `ssh 10.31.33.130`
- Déconexion du **sshd** : `exit`
- Arrêt du service **sshd** : `sudo systemctl stop ssh` 
- Redémarrage du service **sshd** : `sudo systemctl start ssh` 

# 2. Serveur Web Apache
## 2.1 Configuration de base
- Installation du paquet **apache2** : `sudo apt-get install apache2`
- Vérification du bon démarrage du service **apache2** : `systemctl start apache2` 
- Vérification du bon fonctionnement du serveur WEB en tapant : **http://10.31.33.130**
- Exploration du contenu du répertoire **/etc/apache2** :
    - `cd /etc/apache2`
    - `ls`
- Fonctionnement de la configuration : `cat apache2.conf`
- Manuel de la commande **a2enmod** : `man a2enmod`
- Activation des pages d'accueil des utilisateurs à l'aide du module **userdir** : `sudo a2enmod userdir`
- Redémarrage du service apache2 à l'aide de la commande **sytemctl**: `systemctl restart apache2`
- Création du répertoire **public_html** dans le répertoire d’accueil de l'utilisateur **iut** : `sudo mkdir public-html`
- Attribuer les droits d'accès à l'utilisateur du serveur WEB **www-data** pour lui permettre l'accès au **répertoire d’accueil** ainsi qu'au répertoire **public-html** : 
    - **/** : `sudo chown iut:www-data /`
    - **public_html** : `sudo chown iut:www-data public-html`
- Affecter les droits par défaut au répertoire **public-html** : `sudo chmod 755 public-html`
- Vérification du bon fonctionnement du serveur Web en tapant : **http://10.31.33.130/~iut**
- Désactivation du listage des répertoires : 
    - `cd etc/apache2/mods-available`
    - `sudo nano userdir.conf`
- Vérification de la désactivation du listages des répertoires en tapant **http://10.31.33.130/~iut**
- Création d'un fichier **bienvenue.html** dans le répertoire **public_html** : 
    - `cd home/iut/public_html`
    - `touch bienvenue.html`
    - `sudo nano bienvenue.html`
- Vérification du bon fonctionnement du serveur Web en tapant : **http://10.31.33.130/~iut/bienvenue.html**

## 2.2 Les serveurs virtuels
- Récupération du nom DNS de notre machine virtuelle : `nslookup 10.31.33.130`
    
    -> Résultat de la commande : `2A4V3-31UVM0386.ad-urca.univ-reims.fr` 
- Crée un répertoire de travail **mon serveur** : 
    - `cd /home/iut`
    - `sudo mkdir mon_serveur`
- Attribution les droits d'accès au répertoire **mon_serveur** pour l'utilisateur **www-data** : `sudo chown iut:www-data mon_serveur/`
- Création d'un lien symbolique du répertoire **mon_serveur** vers l'arborescence **/var/www** : `sudo ln -s /home/iut/mon_serveur /var/www`
- Création d'un fichier **index.html** dans le répertoire **mon_serveur** contenant une phrase simple : 
    - `cd mon_serveur`
    - `touch index.html` 
    - `sudo nano index.html`
- Déplacement dans le répertoire **/etc/apache2/sites-available** : `cd /etc/apache2/sites-available`
- Copie du fichier **000-default.conf** en **2A4V3-31UVM0386.conf** : `sudo cp 000-default.conf 2A4V3-31UVM0386.conf`
- Edition du fichier **2A4V3-31UVM0386.conf** : `sudo nano 2A4V3-31UVM0386.conf`
- Activation de notre site : `sudo a2ensite 2A4V3-31UVM0386`
- Rechargement de la configuration d'apache : `systemctl restart apache2`
- Vérification de l'accès au serveur : **http://2A4V3-31UVM0386.ad-urca.univ-reims.fr**

# 3. Serveur Web sécurisé https
- Vérification de la présence des paquets nécessaires : `dpkg -l | grep ssl`

    -> Tous les paquets nécessaires sont installés.
- Création du certificat :
    - `mkdir /etc/apache2/ssl`
    - `/usr/sbin/make-ssl-cert /usr/share/ssl-cert/ssleay.cnf /etc/apache2/ssl/apache.pem`
- Création du fichier de configuration **VirtualHost** de notre serveur ssl : `cp /etc/apache2/sites-available/default-ssl.conf /etc/apache2/sites-available/mon-serveur-ssl.conf` 
- Modification du fichier **mon-serveur-ssl.conf** : `sudo nano /etc/apache2/sites-available/mon-serveur-ssl.conf`
- Activation du module SSL d'Apache : `sudo a2enmod ssl`
- Activation de notre site : `sudo a2ensite mon-serveur-ssl.conf`
- Redémarrage du service Apache : `systemctl restart apache2`
- Vérification de l'accès aux pages : **https://2A4V3-31UVM0386.ad-urca.univ-reims.fr**

# 4. Langage de programmation PHP
- Installation du paquet **php** : `sudo apt-get install php`
- Installation du paquet **libapache2-mod-php** : `sudo apt-get install libapache2-mod-php`
- Modification du fichier **php8.1.conf** : `sudo nano /etc/apache2/mods-enabled/php8.1.conf`
- Redémarrage du service apache2 :  `systemctl restart apache2` 
- Création d'un fichier **index.php** dans le répertoire **public_html** : 
    - `cd public_html`
    - `nano index.php`
- Vérification de l'accès à la page : **http://10.31.33.130/~iut/index.php**

# 5. Serveur de base de données MySQL 
- Installation du paquet **mysql-server** : `sudo apt-get install mysql-server`
- Configuration de la sécurité : `sudo mysql_secure_installation`
- Connexion en tant que **root** : `sudo mysql`
- Création de l'utilisateur **admin** avec le mot de passe **Iutinfo#123** : `CREATE USER 'admin'@'localhost' IDENTIFIED BY 'Iutinfo#123';`
- Attribution des privilèges à l'utilisateur **admin** : `GRANT ALL PRIVILEGES ON *.* TO 'admin'@'localhost' WITH GRANT OPTION;` 
- Quitter **mysql** : `exit`
- Redémarrage du service **mysql** : `systemctl restart mysql` 
- Test du serveur : `mysqlshow -u admin -p`


# 6. Outil d'administration de bases de données phpMyAdmin
- Installation du paquet phpmyadmin : `sudo apt-get install phpmyadmin` 
    
    *__A noter__ : De nombreux problèmes dus au mot de passe qui, à chaque fois, ne respecte pas les bonnes exigences alors que l'on demande un mot de passe simple. Avec notamment de nombreuses réinstallations du paquet (`sudo apt-get purge phpmyadmin`) pour corriger les différents problèmes.*
- Vérification de la présence du fichier de configuration pour apache :
    - `cd /etc/apache2/conf-enabled`
    - `ls`
- Activation du module de gestion des chaines de caractères multi-octets de **php** : `sudo phpenmod mbstring`
- Redémarrage du service **apache2** : `systemctl restart apache2`
- Vérification de l'accès à la page : **http://10.31.33.130/phpmyadmin**

# 7. Installation de la plateforme collaborative MediaWiki
- Téléchargement de l’archive **MediaWiki** dans le répertoire personnel de l'utilisateur **iut** : `wget https://releases.wikimedia.org/mediawiki/1.41/mediawiki-1.41.1.zip`
- Installation du paquet unzip : `sudo apt-get install unzip` 
- Décompression du fichier : 
    - `cd mon_serveur`
    - `unzip mediawiki-1.41.1.zip` 
- Configuration de la base de donnée :
    ```
    sudo mysql

    CREATE DATABASE my_wiki;
    CREATE USER 'wikiuser'@'localhost' IDENTIFIED BY 'Iutinfo#123';
    ```
- Exécution du script d'installation : **http://2A4V3-31UVM0386.ad-urca.univ-reims.fr/mediawiki-1.41/index.php**
- Mise à jour des **add-ons** de php : `sudo apt-get install php8.1-intl`
- Vérification du bon fonctionnement en accédant à l'URL de notre machine virtuelle : **https://2A4V3-31UVM0386.ad-urca.univ-reims.fr**