## Procédure DNS  
  
#### Étape 1 : Mettre le réseau de la VM en "Réseau Interne"  
  
**Sur Virtualbox :**  
  - Sélectionner la VM Windows Server, cliquer sur **Settings**, puis sur **Network**.
  - Activer l'Adapter 2, dans le menu **Attached to**, choisir **Internal Network**, dans le menu **Promiscuous Mode**, choisir **Allow All**.

#### Étape 2 : Configurer le réseau

**Sur le serveur Windows Server :**
- Dans le menu **Network and Sharing Center** du **Control Panel**, cliquer sur **Change Adapter Settings**, puis clic droit sur le réseau et selectionner **Properties**.
- Cliquer sur le protocol **IPV4** puis sur **Properties**
- Cliquer sur "Use the following IP adress" et entrer les données suivantes :
    - IP address: `172.20.0.5`
    - Subnet Mask: `255.255.255.0`
- Cliquer sur **OK**, puis sur **Close**.

#### Étape 3 : Créer une zone de recherche directe

- Dans le **DNS Manager**, clic droit sur **Forward Lookup Zones**, puis cliquer sur **New zone**.
- Dans le **New Zone Wizard** 
  - cliquer sur **Next** deux fois, 
  - puis choisir un nom en `nom.lan` dans **Zone Name**, 
  - puis cliquer sur **Next** deux fois 
  - et enfin sur **Finish**.


#### Étape 4 : Créer une zone de recherche inversée

- Dans le **DNS Manager**, clic droit sur **Reverse Lookup Zones**, puis cliquer sur **New zone**.
- Dans le **New Zone Wizard** 
  - cliquer sur **Next** deux fois, 
  - cocher IPV4 ou IPV6 en fonction des besoins, puis cliquer sur **Next**.
  - Cocher **Netword ID** et entrer le Net ID de l'adresse IP de votre réseau, comme par exemple `172.20.0`, 
  - puis cliquer sur **Next** trois fois 
  - et enfin sur **Finish**.

#### Étape 5 : Enregistrement A ou AAAA
- Dans le **DNS Manager**, dérouler **Forward Lookup Zones**, puis clic droit sur `nom.lan` et cliquer sur **New Host (A or AAAA)**.
- Dans **New Host** 
  - Choisir un nom pour l'host, par exemple `host1`, 
  - Entrer l'adresse IP de l'hôte, par exemple `172.20.0.101`.
  - Cocher **Creat associated pointer (PTR) record**, 
  - puis cliquer sur **Add Host**.
 
--> Penser à faire cette étape pour les machines et clients ET la machine serveur.

#### Étape 5 : Enregistrement CNAME
- Dans le **DNS Manager**, dérouler **Forward Lookup Zones**, puis clic droit sur `nom.lan` et cliquer sur **New alias (CNAME)**.
- Dans **New Host** 
  - Choisir un nom pour l'alias, dans l'idéal le même que la partie avant le `.lan` de `nom.lan`, 
  - Dans **Fully qualified domain Name (FQDN) for target host**, cliquer sur **Browse**.
  - Double-cliquer plusieurs sous **Records** jusqu'à trouver le serveur enregistré, 
  - puis cliquer sur **Ok**.
