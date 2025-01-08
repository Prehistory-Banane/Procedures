# Installation et configuration de ZABBIX sur un serveur Debian
Zabbix est une solution open-source de supervision qui permet de surveiller les performances et la disponibilité des éléments d'un SI (serveurs, réseaux, VMs, services, clients).

 ## 🔧 Prérequis

- Un serveur avec une distribution Linux (Debian, Ubuntu, CentOS, etc).
- Un serveur web (Apache ou Nginx).
- Un SGBD ou DBMS (MariaDB/MySQL ou PostgreSQL).
- Un Windows 11 où installer l'agent Zabbix.


## Installation d'un serveur web avec Nginx

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




