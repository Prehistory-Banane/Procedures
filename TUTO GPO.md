## Création d'une GPO et application à un groupe de sécurité particulier

#### Dans le Server Manager
- Cliquer sur Tools
- Cliquer sur Group Policy Management
#### Dans la fenêtre Group Policy Management
- Clic droit sur Group Policy Objects, puis sur New
- Nommer la GPO  
![0](https://github.com/user-attachments/assets/7d1ebeb0-c5f1-447e-8a31-a83d4721193a)
- La GPO apparaît  
![1](https://github.com/user-attachments/assets/04689331-dc03-49ea-88c6-a46d99b8bd4a)
- Clic droit sur la GPO puis cliquer sur Edit
#### Dans la fenêtre Group Policy Management Editor
- Dérouler User Configuration
- Dérouler Administrative Templates
- Cliquer sur Control Panel
- Double-cliquer sur "Prohibit access to Control Panel and PC settings" puis sur Ok
![5](https://github.com/user-attachments/assets/ebb556b3-f8d7-4f37-8f00-b36b396aeb1a)
- La GPO est activée  
![2](https://github.com/user-attachments/assets/ecc7929a-9126-4298-af62-9767e9c3f8e9)

#### Dans la fenêtre Group Policy Management
- Cliquer sur la GPO dont on veut choisir le groupe auquelle elle s'appliquer
- Cliquer sur Add
- Taper le nom du groupe, ici "Wilders_Students"
- Cliquer sur Ok  
![3](https://github.com/user-attachments/assets/f5b10bf2-75b5-4909-8da0-2c74843193f6)
- Cliquer sur le groupe "Authendicated Users" puis sur Remove  
![6](https://github.com/user-attachments/assets/ef5b353c-637a-477e-a7cb-9f4e9b61c494)
- La GPO est prête !  
![7](https://github.com/user-attachments/assets/26eb81a6-d5a0-45e0-8a1d-5a436c06ee90)


