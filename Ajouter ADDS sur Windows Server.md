# Ajouter ADDS sur Windows Server


- Dans le Server Manager, cliquer sur "Manage", puis sur "Add Roles and Features"

- Dans la fenêtre Add Roles and Features Wizard
  - Cliquer sur Next
  - Cliquer sur Next
  - Cliquer sur Active Directory Domain Services puis sur Next
  - Cliquer sur Next
  - Cliquer sur Next
  - Cliquer sur Install

- Redémarrer Windos Server.

- Cliquer sur l'icône de Notifications du Server Manager (qui doit afficher un petit triangle jaune)  
  - Cliquer sur "Promote this server to a demain controller"

- Dans la fenêtre Deployment Configuration
  - Cliquer sur "Add a new forest"
  - Dans Root domain name, ici on choisira "wilders.lan"
  - Cliquer sur Next

- Dans la fenêtre Prerequisites Check, attendre quelques instants
  - Normalement le message suivant devrait apparaître : 
  " All prerequisites checks passed successfully. Click 'Install' to begin installation
  - Cliquer sur Install

- Cliquer sur Next

- Dans la fenêtre Additional Options
  - Le Wizard va automatiquement proposer "WILDERS" comme NetBIOS domain name, c'est très bien
  - Cliquer sur Next


- Dans la fenêtre Domain Controller Options
  - Laisser tout tel quel
  - Choisir un mot de passe
  - Cliquer sur Next

- Cliquer sur Next
- Cliquer sur Next

- Dans la fenêtre Vérification de la configuration requise, patienter quelques instants
  - Le message suivant devrait apparaître :
  "Toutes les vérification de la configuration requise ont donné satisfaction."
  - Cliquer sur Install

- Windows Server devrait redémarrer

- Au redémarrage
  - Cliquer sur Other User
  - Dans User Name, écrire : Administrator@wilders.lan
  - Dans Password, écrire le mot de passe choisit précédemment
- Une fois connecté, on peut vérifier que tout fonctionne en faisant les commandes `ping wilders.lan` et `nslookup wilders.lan` sur PowerShell, pour vérifier que c'est bien le contrôleur de domaine qui répond.
