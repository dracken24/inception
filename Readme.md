Étape 1 - Installation du serveur Web Nginx
Pour afficher les pages Web aux visiteurs du site, vous allez utiliser Nginx, un serveur Web hautes performances. Vous utiliserez le gestionnaire de paquets APT pour obtenir ce logiciel.

Comme il s'agit de votre première utilisation aptpour cette session, commencez par mettre à jour l'index des packages de votre serveur :

sudo apt update
Ensuite, exécutez apt installpour installer Nginx :

sudo apt install nginx
Lorsque vous y êtes invité, appuyez sur Yet ENTERpour confirmer que vous souhaitez installer Nginx. Une fois l'installation terminée, le serveur Web Nginx sera actif et fonctionnera sur votre serveur Ubuntu 22.04.

Si le ufwpare-feu est activé, comme recommandé dans notre guide de configuration initiale du serveur, vous devrez autoriser les connexions à Nginx. Nginx enregistre quelques profils d'application UFW différents lors de l'installation. Pour vérifier quels profils UFW sont disponibles, exécutez :

sudo ufw app list
Output
Available applications:
  Nginx Full
  Nginx HTTP
  Nginx HTTPS
  OpenSSH
Il est recommandé d'activer le profil le plus restrictif qui autorisera toujours le trafic dont vous avez besoin. Étant donné que vous n'avez pas configuré SSL pour votre serveur dans ce guide, vous n'aurez qu'à autoriser le trafic HTTP normal sur le port 80.

Activez ceci en exécutant ce qui suit :

sudo ufw allow 'Nginx HTTP'
Vous pouvez vérifier le changement en vérifiant l'état :

sudo ufw status
Cette sortie indique que le trafic HTTP est désormais autorisé :

Output
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere
Nginx HTTP                 ALLOW       Anywhere
OpenSSH (v6)               ALLOW       Anywhere (v6)
Nginx HTTP (v6)            ALLOW       Anywhere (v6)
Avec la nouvelle règle de pare-feu ajoutée, vous pouvez tester si le serveur est opérationnel en accédant au nom de domaine ou à l'adresse IP publique de votre serveur dans votre navigateur Web.

Si vous n'avez pas de nom de domaine pointé vers votre serveur et que vous ne connaissez pas l'adresse IP publique de votre serveur, vous pouvez la trouver en exécutant l'une des commandes suivantes :

ip addr show
hostname -I
Cela imprimera quelques adresses IP. Vous pouvez essayer chacun d'eux à tour de rôle dans votre navigateur Web.

Comme alternative, vous pouvez vérifier quelle adresse IP est accessible, telle qu'elle est vue depuis d'autres emplacements sur Internet :

curl -4 icanhazip.com
Écrivez l'adresse que vous recevez dans votre navigateur Web et vous serez redirigé vers la page de destination par défaut de Nginx :

http://server_domain_or_IP
Page par défaut de Nginx

Si vous recevez cette page, cela signifie que vous avez installé avec succès Nginx et activé le trafic HTTP pour votre serveur Web.

Étape 2 - Installation de MySQL
Maintenant que vous avez un serveur Web opérationnel, vous devez installer le système de base de données pour stocker et gérer les données de votre site. MySQL est un système de gestion de base de données populaire utilisé dans les environnements PHP.

Encore une fois, utilisez aptpour acquérir et installer ce logiciel :

sudo apt install mysql-server
Lorsque vous y êtes invité, confirmez l'installation en appuyant sur Y, puis sur ENTER.

Une fois l'installation terminée, il est recommandé d'exécuter un script de sécurité préinstallé avec MySQL. Ce script supprimera certains paramètres par défaut non sécurisés et verrouillera l'accès à votre système de base de données. Démarrez le script interactif en exécutant la commande suivante :

sudo mysql_secure_installation
Une question vous demandant si vous souhaitez configurer le VALIDATE PASSWORD PLUGIN.

Remarque : L'activation de cette fonctionnalité relève du jugement. Si activé, les mots de passe qui ne correspondent pas aux critères spécifiés seront rejetés par MySQL avec une erreur. Il est prudent de laisser la validation désactivée, mais vous devez toujours utiliser des mots de passe forts et uniques pour les informations d'identification de la base de données.

Répondez Ypar oui, ou quoi que ce soit d'autre pour continuer sans activer :

Output
VALIDATE PASSWORD COMPONENT can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD component?

Press y|Y for Yes, any other key for No: 
Si vous répondez « oui », il vous sera demandé de sélectionner un niveau de validation du mot de passe. Gardez à l'esprit que si vous entrez 2pour le niveau le plus fort, vous recevrez des erreurs lorsque vous tenterez de définir un mot de passe qui ne contient pas de chiffres, de lettres majuscules et minuscules et de caractères spéciaux :

Output
There are three levels of password validation policy:

LOW    Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary              file

Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 1
Que vous choisissiez ou non de configurer le VALIDATE PASSWORD PLUGIN, votre serveur vous demandera de sélectionner et de confirmer un mot de passe pour l' utilisateur racine MySQL . Cela ne doit pas être confondu avec la racine du système . L' utilisateur racine de la base de données est un utilisateur administratif disposant de privilèges complets sur le système de base de données. Même si la méthode d'authentification par défaut pour l'utilisateur racine MySQL dispense l'utilisation d'un mot de passe, même lorsqu'il est défini , vous devez définir ici un mot de passe fort comme mesure de sécurité supplémentaire. Nous en reparlerons dans un instant.

Si vous avez activé la validation du mot de passe, vous verrez la force du mot de passe root que vous avez entré et votre serveur vous demandera si vous souhaitez continuer avec ce mot de passe. Si vous êtes satisfait de votre mot de passe actuel, appuyez Ysur "oui" à l'invite :

Output
Estimated strength of the password: 100 
Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : y
Pour le reste des questions, appuyez Yet appuyez sur la ENTERtouche à chaque invite. Cela supprimera certains utilisateurs anonymes et la base de données de test, désactivera les connexions root à distance et chargera ces nouvelles règles afin que MySQL respecte immédiatement les modifications que vous avez apportées.

Lorsque vous avez terminé, testez si vous parvenez à vous connecter à la console MySQL :

sudo mysql
Cela se connectera au serveur MySQL en tant qu'utilisateur root de la base de données administrative , ce qui est déduit par l'utilisation de sudolors de l'exécution de cette commande. Vous devriez recevoir la sortie suivante :

Output
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 10
Server version: 8.0.28-0ubuntu4 (Ubuntu)

Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
Pour quitter la console MySQL, écrivez ce qui suit :

exit
Notez que vous n'avez pas eu besoin de fournir un mot de passe pour vous connecter en tant qu'utilisateur root , même si vous en avez défini un lors de l'exécution du mysql_secure_installationscript. En effet, lorsqu'il est installé sur Ubuntu, la méthode d'authentification par défaut pour l'utilisateur administratif MySQL est auth_socket, plutôt qu'avec une méthode qui utilise un mot de passe. Cela peut sembler être un problème de sécurité au premier abord, mais cela rend le serveur de base de données plus sécurisé puisque les seuls utilisateurs autorisés à se connecter en tant qu'utilisateur root de MySQL sont les utilisateurs du système avec sudodes privilèges de connexion depuis la console ou via une application fonctionnant avec les mêmes privilèges. . Concrètement, cela signifie que vous ne pourrez pas utiliser l' utilisateur racine de la base de données administrative pour vous connecter depuis votre application PHP.

Pour une sécurité accrue, il est préférable d'avoir des comptes d'utilisateurs dédiés avec des privilèges moins étendus configurés pour chaque base de données, surtout si vous prévoyez d'avoir plusieurs bases de données hébergées sur votre serveur.

Remarque : certaines anciennes versions de la bibliothèque PHP native de MySQL mysqlnd ne prennent pas en charge caching_sha2_authentication , la méthode d'authentification par défaut pour les utilisateurs créés MySQL 8. Pour cette raison, lors de la création d'utilisateurs de base de données pour les applications PHP sur MySQL 8, vous devrez peut-être vous assurer qu'ils sont configuré pour utiliser mysql_native_passwordà la place. Nous vous montrerons comment procéder à l'étape 6 .

Votre serveur MySQL est maintenant installé et sécurisé. Ensuite, vous installerez PHP, le dernier composant de la pile LEMP.

Étape 3 - Installation de PHP
Nginx est installé pour servir votre contenu et MySQL est installé pour stocker et gérer vos données. Vous pouvez maintenant installer PHP pour traiter le code et générer du contenu dynamique pour le serveur Web.

Alors qu'Apache intègre l'interpréteur PHP dans chaque requête, Nginx nécessite un programme externe pour gérer le traitement PHP et agir comme un pont entre l'interpréteur PHP lui-même et le serveur Web. Cela permet de meilleures performances globales dans la plupart des sites Web basés sur PHP, mais cela nécessite une configuration supplémentaire. Vous devrez installer php8.1-fpm, qui signifie "PHP fastCGI process manager" et utilise la version actuelle de PHP (au moment de la rédaction), pour dire à Nginx de transmettre les requêtes PHP à ce logiciel pour traitement. De plus, vous aurez besoin php-mysqlde , un module PHP qui permet à PHP de communiquer avec des bases de données basées sur MySQL. Les packages PHP principaux seront automatiquement installés en tant que dépendances.

Pour installer les packages php8.1-fpmet php-mysql, exécutez :

sudo apt install php8.1-fpm php-mysql
Lorsque vous y êtes invité, appuyez sur Yet ENTERpour confirmer l'installation.

Vos composants PHP sont maintenant installés. Ensuite, vous configurerez Nginx pour les utiliser.

Étape 4 - Configuration de Nginx pour utiliser le processeur PHP
Lors de l'utilisation du serveur Web Nginx, nous pouvons créer des blocs de serveur (similaires aux hôtes virtuels dans Apache) pour encapsuler les détails de configuration et héberger plusieurs domaines sur un seul serveur. Dans ce guide, nous utiliserons votre_domaine comme exemple de nom de domaine.

Info : Pour en savoir plus sur la configuration d'un nom de domaine avec DigitalOcean, lisez notre introduction au DNS DigitalOcean .

Sur Ubuntu 22.04, Nginx a un bloc de serveur activé par défaut et est configuré pour servir des documents à partir d'un répertoire à /var/www/html. Bien que cela fonctionne bien pour un seul site, cela peut devenir difficile à gérer si vous hébergez plusieurs sites. Au lieu de modifier /var/www/html, nous allons créer une structure de répertoires /var/wwwpour le site Web your_domain , en laissant /var/www/htmlen place le répertoire par défaut à servir si une demande client ne correspond à aucun autre site.

Créez le répertoire Web racine pour votre_domaine comme suit :

sudo mkdir /var/www/your_domain
Ensuite, attribuez la propriété du répertoire avec la $USERvariable d'environnement, qui fera référence à votre utilisateur système actuel :

sudo chown -R $USER:$USER /var/www/your_domain
Ensuite, ouvrez un nouveau fichier de configuration dans le répertoire de Nginx sites-availableà l'aide de votre éditeur de ligne de commande préféré. Ici, nous utiliserons nano:

sudo nano /etc/nginx/sites-available/your_domain
Cela créera un nouveau fichier vierge. Insérez la configuration simple suivante :

/etc/nginx/sites-available/votre_domaine
server {
    listen 80;
    server_name your_domain www.your_domain;
    root /var/www/your_domain;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}
Voici ce que font chacune de ces directives et blocs d'emplacement :

listen— Définit sur quel port Nginx écoutera. Dans ce cas, il écoutera sur le port 80, le port par défaut pour HTTP.
root— Définit la racine du document où les fichiers servis par ce site Web sont stockés.
index— Définit dans quel ordre Nginx priorisera les fichiers d'index pour ce site Web. Il est courant de répertorier index.htmlles fichiers avec une priorité plus élevée que index.phples fichiers pour permettre la configuration rapide d'une page de destination de maintenance dans les applications PHP. Vous pouvez ajuster ces paramètres pour mieux répondre aux besoins de votre application.
server_name— Définit pour quels noms de domaine et/ou adresses IP ce bloc serveur doit répondre. Faites pointer cette directive vers le nom de domaine ou l'adresse IP publique de votre serveur.
location /— Le premier bloc d'emplacement comprend une try_filesdirective qui vérifie l'existence de fichiers ou de répertoires correspondant à une demande d'URL. Si Nginx ne trouve pas la ressource appropriée, il renverra une erreur 404.
location ~ \.php$- Ce bloc d'emplacement gère le traitement PHP réel en pointant Nginx vers le fastcgi-php.conffichier de configuration et le php8.1-fpm.sockfichier, qui déclare quel socket est associé à php8.1-fpm.
location ~ /\.ht— Le dernier bloc d'emplacement traite des .htaccessfichiers que Nginx ne traite pas. En ajoutant la deny alldirective, si des .htaccessfichiers se retrouvent dans la racine du document, ils ne seront pas servis aux visiteurs.
Lorsque vous avez terminé l'édition, enregistrez et fermez le fichier. Si vous utilisez nano, vous pouvez le faire en appuyant sur CTRL+Xpuis sur Yet ENTERpour confirmer.

Activez votre configuration en vous connectant au fichier de configuration du sites-enabledrépertoire de Nginx :

sudo ln -s /etc/nginx/sites-available/your_domain /etc/nginx/sites-enabled/
Ensuite, dissociez le fichier de configuration par défaut du /sites-enabled/répertoire :

sudo unlink /etc/nginx/sites-enabled/default
Note : Si jamais vous avez besoin de restaurer la configuration par défaut, vous pouvez le faire en recréant le lien symbolique, comme ceci :

sudo ln -s /etc/nginx/sites-available/default /etc/nginx/sites-enabled/
Cela indiquera à Nginx d'utiliser la configuration la prochaine fois qu'il sera rechargé. Vous pouvez tester votre configuration pour les erreurs de syntaxe en exécutant ce qui suit :

sudo nginx -t
Si des erreurs sont signalées, revenez à votre fichier de configuration pour vérifier son contenu avant de continuer.

Lorsque vous êtes prêt, rechargez Nginx pour appliquer les modifications :

sudo systemctl reload nginx
Votre nouveau site Web est maintenant actif, mais la racine Web est toujours vide. Créez un fichier à cet emplacement afin de pouvoir tester que votre nouveau bloc de serveur fonctionne comme prévu :/var/www/your_domainindex.html

nano /var/www/your_domain/index.html
Inclure le contenu suivant dans ce fichier :

/var/www/votre_domaine/index.html
<html>
  <head>
    <title>your_domain website</title>
  </head>
  <body>
    <h1>Hello World!</h1>

    <p>This is the landing page of <strong>your_domain</strong>.</p>
  </body>
</html>
Allez maintenant dans votre navigateur et accédez au nom de domaine ou à l'adresse IP de votre serveur, comme indiqué dans la server_namedirective de votre fichier de configuration de bloc de serveur :

http://server_domain_or_IP
Vous recevrez une page comme celle-ci :

Bloc serveur Nginx

Si vous recevez cette page, cela signifie que votre bloc de serveur Nginx fonctionne comme prévu.

Vous pouvez laisser ce fichier en place comme page de destination temporaire pour votre application jusqu'à ce que vous configuriez un index.phpfichier pour le remplacer. Une fois que vous avez fait cela, n'oubliez pas de supprimer ou de renommer le index.htmlfichier à partir de la racine de votre document, car il aurait priorité sur un index.phpfichier par défaut.

Votre pile LEMP est maintenant entièrement configurée. Dans l'étape suivante, vous allez créer un script PHP pour tester que Nginx est en fait capable de gérer .phples fichiers de votre site Web nouvellement configuré.

Étape 5 – Tester PHP avec Nginx
Votre pile LEMP devrait maintenant être complètement configurée. Vous pouvez le tester pour valider que Nginx peut correctement transmettre .phpdes fichiers à votre processeur PHP.

Vous pouvez le faire en créant un fichier PHP de test dans la racine de votre document. Ouvrez un nouveau fichier appelé info.phpdans la racine de votre document dans votre éditeur de texte préféré :

nano /var/www/your_domain/info.php
Ajoutez les lignes suivantes dans le nouveau fichier. Ceci est un code PHP valide qui renverra des informations sur votre serveur :

/var/www/votre_domaine/info.php
<?php
phpinfo();
Lorsque vous avez terminé, enregistrez et fermez le fichier.

Vous pouvez maintenant accéder à cette page dans votre navigateur Web en visitant le nom de domaine ou l'adresse IP publique que vous avez configuré dans votre fichier de configuration Nginx, suivi de/info.php :

http://server_domain_or_IP/info.php
Vous recevrez une page Web contenant des informations détaillées sur votre serveur :

PHPInfo Gratuit 22.04

Après avoir vérifié les informations pertinentes sur votre serveur PHP via cette page, il est préférable de supprimer le fichier que vous avez créé car il contient des informations sensibles sur votre environnement PHP et votre serveur Ubuntu. Vous pouvez utiliser rmpour supprimer ce fichier :

sudo rm /var/www/your_domain/info.php
Vous pouvez toujours régénérer ce fichier si vous en avez besoin ultérieurement.

Étape 6 - Test de la connexion à la base de données à partir de PHP (facultatif)
Si vous souhaitez tester si PHP est capable de se connecter à MySQL et d'exécuter des requêtes de base de données, vous pouvez créer une table de test avec des données factices et interroger son contenu à partir d'un script PHP. Avant cela, vous devez créer une base de données de test et un nouvel utilisateur MySQL correctement configuré pour y accéder.

Remarque : certaines anciennes versions de la bibliothèque PHP native de MySQL mysqlnd ne prennent pas en charge caching_sha2_authentication , la méthode d'authentification par défaut pour MySQL 8, vous devrez peut-être vous assurer qu'elles sont configurées pour l'utiliser mysql_native_passwordà la place.

Nous allons créer une base de données nommée example_database et un utilisateur nommé example_user , mais vous pouvez remplacer ces noms par des valeurs différentes.

Tout d'abord, connectez-vous à la console MySQL en utilisant le compte root :

sudo mysql
Pour créer une nouvelle base de données, exécutez la commande suivante depuis votre console MySQL :

CREATE DATABASE example_database;
Vous pouvez maintenant créer un nouvel utilisateur et lui accorder tous les privilèges sur la base de données personnalisée que vous avez créée.

La commande suivante crée un nouvel utilisateur nommé example_user, en utilisant mysql_native_passwordcomme méthode d'authentification par défaut. Nous définissons le mot de passe de cet utilisateur comme password, mais vous devez remplacer cette valeur par un mot de passe sécurisé de votre choix.

CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
Nous devons maintenant donner à cet utilisateur l'autorisation sur la example_databasebase de données :

GRANT ALL ON example_database.* TO 'example_user'@'%';
Cela donnera à l'utilisateur example_user tous les privilèges sur la base de données example_database tout en empêchant cet utilisateur de créer ou de modifier d'autres bases de données sur votre serveur.

Quittez maintenant le shell MySQL avec la commande suivante :

exit
Vous pouvez tester si le nouvel utilisateur dispose des autorisations appropriées en vous connectant à nouveau à la console MySQL, cette fois en utilisant les informations d'identification de l'utilisateur personnalisé. Remarquez le -pdrapeau dans cette commande, qui vous demandera le mot de passe utilisé lors de la création de l' utilisateur example_user :

mysql -u example_user -p
Après vous être connecté à la console MySQL, confirmez que vous avez accès à la base de données example_database :

SHOW DATABASES;
Cela renverra la sortie suivante :

Output
+--------------------+
| Database           |
+--------------------+
| example_database   |
| information_schema |
+--------------------+
2 rows in set (0.000 sec)
Ensuite, nous allons créer une table de test nommée todo_list . Depuis la console MySQL, exécutez l'instruction suivante :

CREATE TABLE example_database.todo_list (
	item_id INT AUTO_INCREMENT,
	content VARCHAR(255),
	PRIMARY KEY(item_id)
);
Insérez quelques lignes de contenu dans le tableau de test. Vous voudrez peut-être répéter la commande suivante plusieurs fois, en utilisant des valeurs différentes :

INSERT INTO example_database.todo_list (content) VALUES ("My first important item");
Pour confirmer que les données ont bien été enregistrées dans votre table, exécutez :

SELECT * FROM example_database.todo_list;
Votre sortie devrait s'afficher comme suit :

Output
+---------+--------------------------+
| item_id | content                  |
+---------+--------------------------+
|       1 | My first important item  |
|       2 | My second important item |
|       3 | My third important item  |
|       4 | and this one more thing  |
+---------+--------------------------+
4 rows in set (0.000 sec)

Après avoir confirmé que vous disposez de données valides dans votre table de test, vous pouvez quitter la console MySQL :

exit
Vous pouvez maintenant créer le script PHP qui se connectera à MySQL et demandera votre contenu. Créez un nouveau fichier PHP dans votre répertoire racine Web personnalisé à l'aide de votre éditeur préféré. Nous utiliserons nanopour cela :

nano /var/www/your_domain/todo_list.php
Le script PHP suivant se connecte à la base de données MySQL et interroge le contenu de la todo_listtable, affichant les résultats dans une liste. S'il y a un problème avec la connexion à la base de données, il lèvera une exception.

Ajoutez le contenu suivant à votre todo_list.phpscript :

/var/www/votre_domaine/todo_list.php
<?php
$user = "example_user";
$password = "password";
$database = "example_database";
$table = "todo_list";

try {
  $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
  echo "<h2>TODO</h2><ol>"; 
  foreach($db->query("SELECT content FROM $table") as $row) {
    echo "<li>" . $row['content'] . "</li>";
  }
  echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}
Enregistrez et fermez le fichier lorsque vous avez terminé l'édition.

Vous pouvez maintenant accéder à cette page dans votre navigateur Web en visitant le nom de domaine ou l'adresse IP publique configurée pour votre site Web, suivi de/todo_list.php :

http://server_domain_or_IP/todo_list.php
Vous devriez recevoir une page comme celle-ci, montrant le contenu que vous avez inséré dans votre tableau de test :

Exemple de liste de tâches PHP

Cela signifie que votre environnement PHP est prêt à se connecter et à interagir avec votre serveur MySQL.