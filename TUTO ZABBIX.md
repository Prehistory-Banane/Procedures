# Installation et configuration de ZABBIX sur un serveur Debian
Zabbix est une solution open-source de supervision qui permet de surveiller les performances et la disponibilité des éléments d'un SI (serveurs, réseaux, VMs, services, clients).

 ## 🔧 Prérequis

- Un serveur avec une distribution Linux (Debian, Ubuntu, CentOS, etc).
- Un serveur web (Apache ou Nginx).
- Un SGBD ou DBMS (MariaDB/MySQL ou PostgreSQL).
- Un Windows 11 où installer l'agent Zabbix.


## Étape 1 - Installation d'un serveur web avec Nginx

Commençons par installer le paquet Nginx, mais avant cela mettons à jour le cache de paquets sur notre machine :
```sh
sudo apt update -y
```

Ensuite, on passe à l'installation du paquet Nginx, ce qui est très simple puisqu'il est disponible dans les dépôts officiels.
```sh
sudo apt install nginx -y
```  
![1](https://github.com/user-attachments/assets/5b6f868a-4fae-48c0-a163-098651da190c)

Lorsque l'installation est effectuée, on peut regarder quelle version est installée à l'aide de la commande suivante (similaire à celle d'Apache ou d'autres paquets) :
```sh
sudo nginx -v
```

Suite à l'installation, le serveur Nginx est déjà démarré, on peut le vérifier avec la commande ci-dessous. Cela permettra de voir qu'il est bien actif.
```sh
sudo systemctl status nginx
```
![2](https://github.com/user-attachments/assets/47649ca7-2605-431f-ba23-6ace544a2be9)

Pour que notre serveur Web Nginx démarre automatiquement lorsque la machine Linux démarre ou redémarre, on doit exécuter la commande suivante :
```sh
sudo systemctl enable nginx
```
![3](https://github.com/user-attachments/assets/fffcd483-b783-4cf2-8153-86d12dd7579e)

Test depuis un client pour vérifier que tout fonctionne en tapant l'adresse IP du serveur dans un navigateur
![4](https://github.com/user-attachments/assets/a781e55d-855a-4918-a04e-3b54ca95f12b)


## 🔬 Étape 2 - Installation du serveur Zabbix
1. Installation du dépôt de Zabbix dans le système :
```sh
1 wget https://repo.zabbix.com/zabbix/7.2/release/debian/pool/main/z/zabbix-release/zabbix-release_latest_7.2+debian12_all.deb
2 dpkg -i zabbix-release_latest_7.2+debian12_all.deb
```  
![5](https://github.com/user-attachments/assets/0084bf85-ec1c-4339-8ec3-bed7f10d626c)  
![6](https://github.com/user-attachments/assets/c907b36f-96d4-4b61-a0d4-c8eeb5fa185e)

2. Mise à jour de la liste des paquets et upgrade éventuel :
```sh
apt update && apt upgrade -y
```  
![7](https://github.com/user-attachments/assets/93444039-dc39-4025-b36e-e2d94861cca7)

3. Installation de Zabbix server, du frontend, et de l'agent :
```sh
apt install zabbix-server-mysql zabbix-frontend-php zabbix-nginx-conf zabbix-sql-scripts zabbix-agent
```  
![8](https://github.com/user-attachments/assets/4c268fca-ccc5-4184-8211-9ed6e044ceda)
Après un long déroulement, on arrive au bout.  
![9](https://github.com/user-attachments/assets/6975a853-27fb-4727-acd9-460b6ac2641e)

## 🔬 Configuration de Zabbix
1. Installation du SGBD :
```sh
apt install mariadb-server
```  
![10](https://github.com/user-attachments/assets/281b18ea-5b64-4689-a204-5725b23d97c0)

2. Vérification du SGBD :
```sh
systemctl status mysql
```  
![11](https://github.com/user-attachments/assets/c54b1c01-2ac1-4848-87b6-38a522541ee7)

3. Création et configuration de la base de données :
```sh
1 mysql -uroot -p
2 password (ici Azerty1*)
3 MariaDB> create database zabbix character set utf8mb4 collate utf8mb4_bin;
4 MariaDB> create user zabbix@localhost identified by 'Azerty1*';
5 MariaDB> grant all privileges on zabbix.* to zabbix@localhost;
6 MariaDB> set global log_bin_trust_function_creators = 1;
7 MariaDB> quit;
```  
![12](https://github.com/user-attachments/assets/66c8e86e-0219-4a75-b086-04d32350ba95)


4. Importation du schéma et des données :
```sh
zcat /usr/share/zabbix/sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix
```  
![13](https://github.com/user-attachments/assets/a813eb4a-7ebe-4dfc-ae08-15c52fc07d34)

5. Désactivation de la possibilité de modifier la configuration de la BD par des acteurs malveillants :
```sh
1 mysql -uroot -p
2 password
3 MariaDB> set global log_bin_trust_function_creators = 0;
4 MariaDB> quit;
```  
![14](https://github.com/user-attachments/assets/8dd3e817-14b3-44fb-ab55-6afe0379cf28)

6. Edition du fichier de configuration de la BD du serveur Zabbix dans `/etc/zabbix/zabbix_server.conf` :
```sh
DBPassword=Azerty1*
```  
![15](https://github.com/user-attachments/assets/fd55303e-9609-40c5-93e2-b41686acfc95)

7. Configuration de PHP pour accéder au frontend dans `/etc/zabbix/nginx.conf` :
```sh
1 listen 8080;
2 server_name <ici tu rentreras l'adresse IPv4 de ta machine>;
```  
![16](https://github.com/user-attachments/assets/be588f92-a55f-4afb-bbf2-0740ff2262c3)


8. Démarrage du serveur et des processus de l'agent :
```sh
1 systemctl restart zabbix-server zabbix-agent nginx php8.2-fpm
2 systemctl enable zabbix-server zabbix-agent nginx php8.2-fpm
```  
![17](https://github.com/user-attachments/assets/f035c1f9-017e-417f-ba47-f7b8bff907fb)


## 🔬 Étape 3 - Configuration de Zabbix depuis la WUI
1. Depuis un client tape l'adresse du serveur dans un navigateur en ajoutant le port d'écoute `X.X.X.X:8080`
![18](https://github.com/user-attachments/assets/7d588581-24aa-4ca5-a82c-ae277c88d64b)



2. A partir des boutons `Next step`, on peut configurer le serveur. A renseigner entre autres :
- Le mdp de la base de donnée
- Le nom du serveur Zabbix
- le fuseau horaire du serveur (UTC+1 si on est à Paris par exemple)  
![19](https://github.com/user-attachments/assets/2bf75684-2e55-4759-b551-cd78d3ac9584)  
![20](https://github.com/user-attachments/assets/9f9d2b6a-ba89-4f01-b9f9-490ebf157b91)  
![21](https://github.com/user-attachments/assets/79fdf269-4877-4001-8736-6577348cb167)  


## 🔬 Étape 4 - Installation et configuration de l'Agent Zabbix
1. Télécharge l'agent Zabbix depuis le client Windows 10 à l'adresse suivante : [Agent Zabbix](https://www.zabbix.com/download_agents)
2. Lance l'installation de l'agent sur le client Windows 10
3. Durant l'installation, précise l'adresse IP du serveur Zabbix dans le champ Zabbix server IP/DNS  
![22](https://github.com/user-attachments/assets/56291c67-866d-41df-b2c3-aa88ce7107f3)


## 🔬 Étape 5 - Ajout d'un hôte et création d'un groupe
1. Pour ta 1ère connexion sur la WUI tu utiliseras les identifiants par défaut :
- Utilisateur : Admin
- Mot de passe : zabbix

2. Création de groupes d'hôtes :
- Dans le menu `Data collection/Host groups` :
  - Crée un groupe d'hôtes sous Windows en cliquant sur le bouton `Create host group`.
  - Appelle ce groupe `Windows hosts`.
3. Ajout des hôtes :  
![23](https://github.com/user-attachments/assets/cc15e25d-5469-4a1b-b7c7-c2dec9037010)

- Dans le menu `Data collection/Hosts` :
  - Ajoute ton client Windows 10 en cliquant sur le bouton `Create host`.
  - Ajoute le dans le groupe créé précédemment.
  - Ajoute l'interface `Agent`.
  - Renseigne l'adresse IP de ton client.  
![24](https://github.com/user-attachments/assets/0a5d2a1c-8e43-4d63-ae0e-3d423940b238)

## 🔬 Étape 6 - Configuration des alertes et des notifications
1. Application du template pour la supervision des hôtes Windows :
- Dans le menu `Data collection/Hosts` :
  - Clique sur le client.
  - Dans le champ `Templates`, clique sur le bouton `Select`.
  - Dans le champ `Template group`, clique sur le bouton `Select`.
  - Choisis `Template`.
  - Coche dans la liste le modèle `Windows by Zabbix agent` puis clique sur `Select`.  
![25](https://github.com/user-attachments/assets/08f31fa2-fa72-4995-a5a5-08ad162d97f3)
  - Clique sur le bouton `Update`

2. Tu vas configurer la partie notification en suivant les étapes de cette [vidéo](https://www.youtube.com/watch?v=9DT7kR8fa0o) :
```sh
Concernant la vidéo :

- Il te faudra surement activer l\'authentification à 2 facteurs dans ton web client de messagerie pour permettre à Zabbix d\'envoyer des notifications au moyen de ton adresse mail.
- Tu n\'as pas besoin d\'activer \"IMAP access\" à priori comme expliqué dans la vidéo.
- Quand tu seras à l\'étape Alerts/Media types inutile de créer un nouveau type de media en cliquant sur Create media type. Il te suffit d\'activer le \"Media type\" Email déjà existant et de le configurer comme expliquer.
```

3. Création d'une alerte spécifique lié à l'utilisation de notre RAM :
- Dans le menu `Data collection/Hosts` :
  - Clique sur `Items` qui se trouve sur la ligne de ton client.  
![26](https://github.com/user-attachments/assets/e6589dc7-b5ef-4dbd-8799-15e7a467e267)
  - Dans le champ `Name` écris "memory utilization" puis tape entrée.  
![27](https://github.com/user-attachments/assets/fd5e32b6-3b0d-4df9-8d8c-b88ca9d16488)
  - Clique sur `Memory utilization` puis sur le bouton `Clone`.  
![28](https://github.com/user-attachments/assets/d1df449d-cd95-43e4-a630-e27e2fd15c8e)
  - Donne un nom et une key à ton item pour le test (ex : Alerte RAM et AlerteRAM).
  - Clique sur `Add`.  
 ![30](https://github.com/user-attachments/assets/ee0b619e-30cb-41d5-9d5e-9eefe82e4e56)


4. Configuration du déclencheur de l'alerte précédemment créée.
- Dans le menu `Data collection/Hosts` :
  - Clique sur `Triggers` qui se trouve sur la ligne de ton client.  
![26 - Copie](https://github.com/user-attachments/assets/a93ee9a0-4040-47a9-bcb3-7cde9b1dd06e)
  - Clique sur le bouton `Create trigger` et donne lui un nom (ex : WindowsAlerteRam).
  - Clique sur `Disaster` et sur `Add` du champ `Expression`.  
![31](https://github.com/user-attachments/assets/0ca44778-02eb-4f13-a5de-95af8b071494)
  - Sélectionne dans la liste ton item `Alerte RAM`.  
![32](https://github.com/user-attachments/assets/f53bd30d-1e35-4aa2-adff-c4534b35b6d6)
  - Dans `Result` sélectionne `>=` puis la valeur qui va te permettre de déclencher l'alerte.  
![33](https://github.com/user-attachments/assets/93cf8fef-b9bb-4483-8ffe-345c6bbd13f2)
  - Clique sur `Insert` puis `Add` en bas de la fenêtre.  
![34](https://github.com/user-attachments/assets/d2d65738-3313-4457-b016-ff2bed313005)

5. Ouvre quelques applications sur ton client pour solliciter la RAM au niveau que tu as défini pour l'alerte et réceptionne ton mail. Quand l'utilisation de ta RAM va redescendre tu vas recevoir dans la foulée un mail de Zabbix t'indiquant que le problème est résolu.
