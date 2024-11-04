## Procédure DHCP  
  
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

#### Étape 3 : Installer et configurer le service DHCP

- Dans le **Server Manager**, cliquer sur **Manage** en haut à droite, puis sur **Add Roles and Features**.
- Sélectionner **DHCP Server** dans la liste et valider.
- Cliquer sur **Tools**, puis sur **DHCP**.
- Dérouler le serveur puis clic droit sur **IPV4**, cliquer ensuite sur **New Scope**
- Dans le **New Scope Wizard** entrer les données suivantes :
    -  Start IP address: `172.20.0.100`
    -  End IP address: `172.20.0.200`
    -  Subnet Mask: `255.255.255.0`
    -  Cliquer sur **Next** puis **Finish**

#### Étape 4 : Configurer une attribution statique pour une machine cliente particulière

- Dérouler **Scope <Nom_du_Scope>** puis clic droit sur **Reservations** et cliquer sur **New Reservation**
- Entrer les données suivantes :
    - Reservation name: <Nom_du_Client>
    - IP address: `172.20.0.10`
    - MAC address: <Adress_MAC_du_Client>
- Cliquer sur **Add**, puis sur **Close**. 

#### Étape 5 : Test et validation de la procédure
1. Test de l'allocation dynamique d'IP :
    - Lancer une machine cliente configurée en **Internal Network**
    - Faire la commande `ipconfig` pour vérifier qu'une adresse IP a bien été attribuée dans la plage `172.20.0.100` - `172.20.0.200`.
2. Test de la réservation de l'adresse IP statique :
    - Lancer la machine cliente abec l'adresse MAC réservée.
    - Faire la commande `ipconfig` pour vérifier que l'adresse IP `172.20.0.10 `a bien été attribuée
    - Faire les commandes `ipconfig/release` puis `ipconfig/renew` pour vérifier que l'adresse IP `172.20.0.10` est bien conservée malgré la demande de renouvellement.
