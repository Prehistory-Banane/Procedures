## I. Prérequis de Windows LAPS
Avant de commencer à envisager la configuration de Windows LAPS, vous devez vous assurer de respecter les prérequis suivants :

- Utiliser des systèmes d'exploitation compatibles
- Mettre à jour les machines à gérer avec Windows LAPS
- Mettre à jour les contrôleurs de domaine Active Directory
- Vérifier la sauvegarde de l'Active Directory

Si tout est bon de votre côté, vous pouvez passer à la suite.

## II. Configuration de Windows LAPS
La configuration de Windows LAPS s'effectue en plusieurs étapes, sur un principe similaire à celui de LAPS legacy, si ce n'est que l'on n’aura pas besoin de déployer le client LAPS puisqu'il est désormais intégré à Windows.

### A. Mettre à jour le schéma Active Directory pour Windows LAPS
Tout d'abord, ouvrez une console PowerShell en tant qu'administrateur et exécutez cette commande pour lister les commandes disponibles dans le module LAPS. Un moyen de vérifier qu'il est bien accessible sur votre serveur.

```sh
Get-Command -Module LAPS
```

Puisque l'on s'apprête à mettre à jour le schéma Active Directory, on veille à disposer d'une sauvegarde de son environnement et surtout on utilise un compte membre du groupe "Administrateurs du schéma" si l'on ne veut pas se prendre un mur.

Toujours dans la console PowerShell, exécutez ces commandes :

```sh
Import-Module LAPS
Update-LapsADSchema -Verbose
```

Le fait d'ajouter le paramètre **"-Verbose"** est facultatif, mais cela permet d'obtenir plus de détails dans la sortie de la commande. Vous verrez passer quelques lignes qui montrent que le **schéma Active Directory a été mis à jour** : la class "computer" va bénéficier d'attributs supplémentaires.
![1](https://github.com/user-attachments/assets/12eedc23-8927-4d04-b3bc-7987e85fa4e5)

Vous pouvez fermer la console PowerShell s'il n'y a pas eu d'erreur apparente.

### B. Vérifier la présence des attributs Windows LAPS
Avant d'aller plus loin, vous devez **vérifier la présence des attributs Windows LAPS** dans votre annuaire AD. Par exemple, à partir de la console "**Utilisateurs et ordinateurs Active Directory**", que vous devez rafraîchir si elle était déjà ouverte. En passant en mode d'affichage avancé, vous devriez voir **6 nouveaux attributs** dans l'onglet "**Editeur d'attributs**" de n'importe quel objet "ordinateur" de votre annuaire :

- msLAPS-PasswordExpirationTime
- msLAPS-Password
- msLAPS-EncryptedPassword
- msLAPS-EncryptedPasswordHistory
- msLAPS-EncryptedDSRMPassword
- msLAPS-EncryptedDSRMPasswordHistory

La preuve en image :  
![2](https://github.com/user-attachments/assets/05ef2b97-a81b-4598-921f-417504da7a3c)

Par ailleurs, et c'est nouveau avec Windows LAPS, il y a un **nouvel onglet nommé "LAPS"** qui a fait son apparition. Il sera votre allié pour obtenir des informations sur le compte administrateur géré d'un ordinateur (nom du compte, mot de passe, expiration, etc.).  
![3](https://github.com/user-attachments/assets/5537cd4c-6cca-447b-8e36-193fe68c166d)

### C. Attribuer les droits d'écriture aux machines
Quand une machine va devoir effectuer une rotation du mot de passe du compte administrateur géré par Windows LAPS, elle devra sauvegarder ce mot de passe dans l'Active Directory. De ce fait, la machine doit pouvoir écrire/modifier son objet correspondant dans l'Active Directory.

La commande ci-dessous donne cette autorisation sur l'unité d'organisation "Departements" (au sein de laquelle il y a mes postes de travail). Même si ce n'est pas obligatoire, je vous recommande de préciser le [DistinguishedName](https://www.it-connect.fr/active-directory-comment-recuperer-le-distinguishedname-dun-objet/) de l'OU ciblée pour éviter les erreurs (notamment si vous avez plusieurs OUs avec le même nom).  

```sh
Set-LapsADComputerSelfPermission -Identity "OU=Departemens,DC=bartinfo,DC=com"
```

Cette commande retourne ce résultat :  
![4](https://github.com/user-attachments/assets/d1d7cafa-70f0-4770-b4f9-a54820511642)

Vous pouvez passer à la suite.

### D. Configurer la GPO Windows LAPS
**La suite consiste à configurer Windows LAPS à partir d'une stratégie de groupe**. Cette GPO va permettre de définir la politique de mots de passe à appliquer sur le compte administrateur géré, l'emplacement de sauvegarde du mot de passe (Active Directory / Azure Active Directory), mais aussi le nom du compte administrateur à gérer avec Windows LAPS.

Tout d'abord, nous devons **importer les modèles d'administration (ADMX) de Windows LAPS** (c'est nécessaire s'il y a déjà un magasin central sur votre domaine, car Windows n'ira pas lire le magasin local). Ce processus n'est pas automatique. Sur le contrôleur de domaine, vous devez récupérer deux fichiers :

- C:\Windows\PolicyDefinitions\LAPS.admx qui correspond aux modèles d'administration de Windows LAPS



