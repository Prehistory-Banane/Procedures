# Gestion UO sur Windows Server


- Dans le Server Manager, cliquer sur "Tools", puis sur "Active Directory Users and Computers"

- Dans la fenêtre Active Directory Users and Computers
  - Clic droit sur le nom de notre Root Domain Name, ici "wilders.lan"
  - Cliquer sur New
  - Cliquer sur Organizational Unit
  - Lui donner un nom, ici "Wilder_students"
  - Cliquer sur Ok

- Clic droit sur l'UO Wilders_students qu'on vient de créer   
  - Cliquer sur New
  - Cliquer Group
  - Lui donner un nom, ici "Students"
  - Ici, on laissera les paramètres par défauts
  - Cliquer sur Ok

- Clic droit sur l'UO Wilders_students 
  - Cliquer sur New
  - Cliquer User

- Dans la fenêtre New Objext - User
  - Lui donner un prénom et un nom, ici "Gege" et "Slide27"
  - Choisir ses identifiants de connexion, ici "gege.slide27@wilders.lan"
  - Cliquer sur Next
  - Choisir un mot de passe (ici "Azerty1*", ce qui est un très mauvais mdp) et laisser uniquement la première options cochées (en fonction des besoins, d'autre cases pourront être cochées)
  - Cliquer sur Next
  - Cliquer sur Finish

- Dans l'UO Wilders_students, nous avons maintenant, un groupe nommé Students et un utilisateur nommé Gege Slide27
  - Double-Cliquer sur Students

- Dans la fenêtre Students Properties
  - Cliquer sur l'onglet Members
  - Cliquer sur Add

- Dans la fenêtre Select Users, Contacts, Computers, Services Account, or Groups
  - Dans l'espace sous "Enter the object names to select"
    - (On peut, sans obligation, cliquer sur Object Types pour restreindre la recherche au type d'objet rechercé)
    - (On peut, sans obligation, cliquer sur Locations pour restreindre les UO de la forêt où faire la recherche)
    - Taper le nom ou une partie du nom de l'objet qu'on veut ajouter au groupe, ici l'utilisateur Gege Slide 27
    - Cliquer sur Check Names
    - Si ce qui a été tapé est pertinent, une auto-complétion devrait vous donner le nom complet de l'objet à rajouter, ici Gege Slide27 (gege.slide27@wailders.lan)
    - Cliquer sur OK
  - Cliquer sur OK

**L'utilisateur Gege Slide27 fait donc maintenant partie du groupe Students.  
Gege Slide27 et le groupe Students sont bien dans l'UO Wilders_students.**




