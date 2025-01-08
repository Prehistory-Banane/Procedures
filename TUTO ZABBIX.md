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

Test depuis un client pour vérifier que tout fonctionne en tapant l'adresse IP du serveur dans un navigateur
![4](https://github.com/user-attachments/assets/a781e55d-855a-4918-a04e-3b54ca95f12b)


## 🔬 Installation du serveur Zabbix
1. Installation du dépôt de Zabbix dans le système :
```sh
wget https://repo.zabbix.com/zabbix/7.2/release/debian/pool/main/z/zabbix-release/zabbix-release_latest_7.2+debian12_all.deb
dpkg -i zabbix-release_latest_7.2+debian12_all.deb
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


