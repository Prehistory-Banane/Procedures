# Tutoriel : Installation et configuration d'un contrôleur de domaine secondaire sur Windows Server Core

## Étape 1 : Préparer le serveur Windows Server Core

### 1.1 Configurer un nom d’hôte
1. Connectez-vous à `G4-SRWIN-CORE`.
2. Vérifiez le nom actuel du serveur :
    ```powershell
    hostname
    ```
3. Changez le nom du serveur pour `G4-SRWIN-CORE` :
    ```powershell
    Rename-Computer -NewName "G4-SRWIN-CORE" -Restart
    ```
    Le serveur redémarrera pour appliquer le nouveau nom.

### 1.2 Configurer une adresse IP statique
1. Vérifiez les interfaces réseau disponibles :
    ```powershell
    Get-NetIPAddress
    ```
2. Configurez une adresse IP statique, une passerelle et des serveurs DNS :
    ```powershell
    New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 192.168.1.10 -PrefixLength 24 -DefaultGateway 192.168.1.1
    Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses 192.168.1.5
    ```
    Remplacez `192.168.1.10` par l'adresse IP de votre serveur.  
    Remplacez `192.168.1.5` par l’adresse IP du contrôleur de domaine principal (ici `SRVWIN-ADDS-GUI`).

3. Testez la connectivité réseau et DNS :
    ```powershell
    Test-Connection pharmgreen.com
    ```

## Étape 2 : Joindre le serveur au domaine

### 2.1 Joindre le domaine
1. Utilisez la commande suivante pour joindre le serveur au domaine `pharmgreen.com` :
    ```powershell
    Add-Computer -DomainName "pharmgreen.com" -Credential (Get-Credential) -Restart
    ```
    Entrez les identifiants d’un compte administrateur du domaine lorsque demandé.  
    Le serveur redémarrera automatiquement après avoir rejoint le domaine.

2. Vérifiez que le serveur est bien dans le domaine :
    ```powershell
    Get-ComputerInfo | Select-Object CsDomain
    ```

### 2.2 Placer le serveur dans l’OU appropriée
1. Par défaut, le serveur sera dans l’OU `Computers`. Utilisez **Active Directory Users and Computers** sur `SRVWIN-ADDS-GUI` pour déplacer `G4-SRWIN-CORE` dans une OU spécifique si nécessaire.

## Étape 3 : Installer le rôle AD-DS

### 3.1 Installer le rôle AD-DS
1. Connectez-vous à `G4-SRWIN-CORE` et installez le rôle **Active Directory Domain Services** :
    ```powershell
    Install-WindowsFeature AD-Domain-Services
    ```
2. Une fois terminé, vérifiez l’installation :
    ```powershell
    Get-WindowsFeature AD-Domain-Services
    ```

## Étape 4 : Promouvoir le serveur en contrôleur de domaine

### 4.1 Lancer la promotion
1. Exécutez la commande suivante pour promouvoir le serveur en tant que contrôleur de domaine :
    ```powershell
    Install-ADDSDomainController -DomainName "pharmgreen.com" -Credential (Get-Credential) -InstallDns:$true -SafeModeAdministratorPassword (ConvertTo-SecureString "MotDePasseAdminDS" -AsPlainText -Force)
    ```
    - `DomainName` : Nom du domaine existant (`pharmgreen.com`).
    - `InstallDns` : Ajoute un service DNS sur le nouveau contrôleur de domaine si nécessaire.
    - `SafeModeAdministratorPassword` : Mot de passe pour le mode restauration AD.

2. Attendez que la configuration se termine. Le serveur redémarrera automatiquement.

### 4.2 Vérifiez la promotion
1. Confirmez que le serveur est devenu un contrôleur de domaine :
    ```powershell
    Get-ADDomainController -Filter *
    ```
    Le serveur `G4-SRWIN-CORE` doit apparaître dans la liste des contrôleurs de domaine.

2. Vérifiez que le serveur est maintenant dans l’OU **Domain Controllers** :
    - Connectez-vous à `SRVWIN-ADDS-GUI`.
    - Ouvrez **Active Directory Users and Computers**.
    - Assurez-vous que l’objet `G4-SRWIN-CORE` se trouve bien dans l’OU **Domain Controllers**.

## Étape 5 : Vérification et tests

### 5.1 Tester la réplication Active Directory
1. Sur `G4-SRWIN-CORE`, testez la réplication AD :
    ```powershell
    repadmin /replsummary
    ```
2. Affichez les détails de la réplication avec :
    ```powershell
    repadmin /showrepl
    ```

### 5.2 Valider les services DNS
1. Vérifiez que le DNS est opérationnel :
    ```powershell
    dcdiag /test:dns
    ```
2. Résolvez un nom de domaine interne pour tester le DNS :
    ```powershell
    Resolve-DnsName pharmgreen.com
    ```

## Étape 6 : Finalisation
1. Sur `SRVWIN-ADDS-GUI`, vérifiez que le contrôleur de domaine secondaire est listé comme un Global Catalog :
    ```powershell
    Get-ADDomainController -Filter * | Select-Object Name, IsGlobalCatalog
    ```
2. Configurez les serveurs DNS pour qu’ils se référencent mutuellement pour assurer une redondance.

Une fois ces étapes terminées, votre serveur `G4-SRWIN-CORE` sera intégré au domaine `pharmgreen.com` et pleinement opérationnel en tant que contrôleur de domaine secondaire.

