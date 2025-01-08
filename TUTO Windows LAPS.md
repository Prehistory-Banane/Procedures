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

Tout d'abord, nous devons **importer les modèles d'administration (ADMX) de Windows LAPS** (c'est nécessaire s'il y a déjà un magasin central sur votre domaine, car Windows n'ira pas lire le magasin local). Ce processus n'est pas automatique. 
Il existe deux cas de figure :

#### 1. Le magasin central existe déjà et dans ce cas, nous devons récupérer deux fichiers sur le contrôleur de domaine :

- C:\Windows\PolicyDefinitions\LAPS.admx qui correspond aux modèles d'administration de Windows LAPS  
![5](https://github.com/user-attachments/assets/b2b81619-1b82-4fd3-834f-1af4e828b164)

_**Si jamais vous utilisez Windows en français**_, vous aurez aussi besoin de copier le fichier de langue FR du fichier ADMX du magasin local
- C:\Windows\PolicyDefinitions\fr-FR\LAPS.adml  
![6](https://github.com/user-attachments/assets/33829adc-b0e1-4d3b-b607-0f8179a520ba)

Ces fichiers sont à déposer dans le **magasin central de votre partage SYSVOL** ("_PolicyDefinitions_") : à la **racine** pour le fichier ADMX et dans le répertoire "**fr-FR**" pour le fichier de langue (_si nécessaire_).  
![11](https://github.com/user-attachments/assets/7b50ce1d-051b-4051-8659-c1b4a471711e)


#### 2. Le magasin central n'existe pas encore, nous allons devoir le créer nous-même :
Pour créer un magasin central pour les modèles d’administration de stratégie de groupe, allons dans C:\Windows
Ensuite nous devons entrer dans le dossier "SYSVOL".  
![7](https://github.com/user-attachments/assets/40cb606e-efd8-4458-b02c-95d759caab23)

Ensuite, entrons dans le dossier "Policies".  
![8](https://github.com/user-attachments/assets/92edfa6f-9eb5-497b-9a4c-815e7821de12)

Dans ce dossier, nous trouverons des dossiers avec des identifiants uniques qui correspondant aux identifiants des objets de stratégie de groupe (GPO) créés sur notre domaine Active Directory.  
![9](https://github.com/user-attachments/assets/66fbab37-843d-4cf9-8c9b-9b3437f4ab58)

Pour créer le magasin central, copions simplement le dossier "C:\Windows\PolicyDefinitions" dans ce dossier "Policies".  
Etant donné que la réplication du dossier SYSVOL entre les différents contrôleurs de domaine est gérée nativement par Active Directory, les modèles d'administration que vous ajouterez dans ce nouveau dossier "PolicyDefinitions" seront automatiquement répliqués vers vos autres contrôleurs de domaine (pour le même domaine AD).  
![10](https://github.com/user-attachments/assets/701c2721-f37d-4a0f-86a1-e4c6a39fc471)

Une fois cette opération indispensable effectuée, une nouvelle GPO peut être créée à partir de la console "**Gestion de stratégies de groupe**". Elle contiendra des paramètres de configuration ordinateur.  
Désormais, vous allez devoir configurer plusieurs paramètres dans cette GPO... Ces paramètres se situent dans :
```sh
Configuration ordinateur > Stratégies > Modèles d'administration > Système > LAPS
```

_**ATTENTION**_ : les paramètres de Windows LAPS se situent bien à cet endroit (sous "Système"). L'autre conteneur "LAPS" situé au même niveau que "Système" dans l'arborescence correspond au LAPS Legacy.


#### Configurer le répertoire de sauvegarde de mot de passe
Commencez par configurer le paramètre "**Configurer le répertoire de sauvegarde de mot de passe**" qui est indispensable pour activer Windows LAPS sur la machine. Vous devez passer ce paramètre sur l'état "**Activé**" et choisir "**Active Directory**". S'il s'agirait d'une configuration basée sur Azure Active Directory, on ferait un choix différent bien entendu.  
![12](https://github.com/user-attachments/assets/067068e2-d279-49d6-b9ce-566f2a98a365)

#### Paramètres du mot de passe
Le second paramètre à configurer se nomme "**Paramètres du mot de passe**" et il permet de personnaliser la complexité du mot de passe pour le compte administrateur géré par Windows LAPS. Vous devez activer ce paramètre et définir la politique de mot de passe. Pour avoir un mot de passe fort, je vous recommande de choisir :

- **Complexité du mot de passe** : Lettres majuscules + lettres minuscules + chiffres + spéciaux
- **Longueur du mot de passe** : 16 caractères
- **Âge du mot de passe (jours)** : 30 (soit par défaut)  
![13](https://github.com/user-attachments/assets/baac7325-6494-4ec6-9e18-6e3cadf2d02d)

#### Configurer la taille de l'historique des mots de passe chiffrés
Ce paramètre est facultatif, mais il me semble intéressant puisqu'il permet **d'activer l'historique des mots de passe**. En le passant sur l'état "**Activé**" et en mettant la taille de l'historique à "**1**", on s'assure de pouvoir lire le mot de passe actuel et le mot de passe précédent. S'il y a un "bug" et que le mot de passe est mis à jour dans l'AD, mais pas sur le poste (sait-on jamais...), on ne perd pas l'accès, car on peut toujours lire le précédent mot de passe.  
![14](https://github.com/user-attachments/assets/e245fd10-620b-4f65-bb4a-5b0254a216c7)

#### Activer le chiffrement du mot de passe
Le quatrième paramètre à activer se nomme "**Activer le chiffrement du mot de passe**", même si c'est le comportement par défaut, il vaut mieux le forcer. Comme son nom l'indique, il permet de dire si oui ou non, le mot de passe stocké dans l'Active Directory doit être chiffré.

#### Nom du compte administrateur à gérer
Par défaut, Windows LAPS va **gérer le compte "Administrateur" natif et intégré à Windows**. Ce n'est pas nécessaire de lui préciser, il le fera de lui-même (il peut identifier ce compte grâce au SID qui est déjà connu). Si l'on souhaite gérer un autre compte avec un nom personnalisé, il est indispensable d'activer le paramètre "**Nom du compte administrateur à gérer**" et de préciser le nom du compte administrateur que vous souhaitez gérer avec LAPS.  
![15](https://github.com/user-attachments/assets/d559ce06-7d2e-4a29-9f7b-d9c47af639cf)

**Au final, notre stratégie de groupe est configurée de cette façon :**  
![16](https://github.com/user-attachments/assets/eea23bbc-2c00-41c1-a87c-0badc855e34d)


