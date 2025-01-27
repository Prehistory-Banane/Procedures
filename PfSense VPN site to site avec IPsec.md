# 🔥🧱IPsec : Installation d'un VPN site à site sur Pfsense🧱🔥


## 1. Présentation
Un VPN (Virtual Private Network) Site-to-Site (aussi appellé LAN-to-LAN) est un VPN qui permet de joindre deux réseaux de type LAN distants de manière à faire en sorte qu'ils puissent communiquer comme s'ils étaient sur le même réseau et qu'un simple routeur les séparait.  
On peut trouver ce genre de VPN entre des agences et un siège d'entreprise par exemple.  
Les agences doivent pouvoir se connecter aux ressources du siège de manière transparente malgré leur distance.  
On établit alors un VPN au travers Internet afin de joindre les deux réseaux mais également de manière à sécuriser ces flux au travers un chiffrement.  


## 2. Architecture
Pour illustrer la mise en place de notre VPN Site-to-Site entre nos routeurs, nous allons utiliser le pare-feu Pfsense et se baser sur le schéma de test suivant :  
![VPN_site_to_site](https://github.com/user-attachments/assets/e23dc0ac-62d8-4d13-813e-c3e59ef93f01)  

Nous aurons donc une interface WAN (10.0.0.X/24) et LAN (172.24.0.X/24 et 10.15.0.X/27) sur chacun de nos Pfsense, le but final sera donc que les deux clients (10.15.0.32 et 172.24.0.0) puissent communiquer entre eux via ce tunnel.  

## 3. Mise en place

On commence donc par accéder à l'interface d'administration de notre premier Pfsense (`10.15.0.32` dans mon cas). On se rend directement dans le menu "VPN" puis dans "IPsec" :  
On clique  sur le "+add P1" pour ajouter une nouvelle configuration IPsec.  
On se retrouve alors avec beaucoup d'options, toutes ont leur importance et leur pertinence mais il n'est pas utile de toute les modifier.  
Nous allons ici nous contenter de faire un VPN simple et fonctionnel, la compréhension, la modification et l'utilisation des différentes options dépend de votre cadre d'utilisation.  
On commence par remplir l'IP de notre partenaire VPN. Étant sur mon Pfsense `10.0.0.4`, je mets l'IP `10.0.0.3` dans **Remote Gateway**.  
On génère une clé de chiffrement (clé qu'il faudra recopier sur l'autre Pfsense)  
Puis on clique sur "**Save**" puis sur "**Apply changes**"
![2](https://github.com/user-attachments/assets/ae7d7c10-26e0-459f-a684-bf6ca569a565)

Puis on clique sur le bouton "+add P2".  
On arrive sur une nouvelle page de configuration sur laquelle nous allons remplir les champs suivants :  
**Local Network** dans lequel je sélectionne le bon Réseau.  
**Remote Network** dans lequel nous allons mettre la plage IP du LAN distant.  
A nouveau, il n'est pas nécessaire de modifier les autres paramètres si vous ne savez pas à quoi ils correspondent.  
Puis on clique sur "**Save**" puis sur "**Apply changes**"  
![3](https://github.com/user-attachments/assets/db16e439-20be-41e4-8cd7-3592ee42f3c5)  
![4](https://github.com/user-attachments/assets/9d330e5c-9454-419f-ac95-f7463db172f4)  


Si on redéveloppe le tableau principal avec le "+", nous aurons quelque chose qui ressemble à cela :  
![1](https://github.com/user-attachments/assets/6ed1714f-0109-4dae-8f3e-bc0cb2d7a428)  


Nous avons donc un résumé de notre configuration VPN.  
Il faut maintenant effectuer exactement la même configuration du coté de notre deuxième Pfsense en adaptant bien entendu les IP et les plages IP précisées et ne cochant la case "Enable PFsense" uniquement après avoir saisi les configurations.  

Sur nos deux routeurs, on va alors aller dans "Firewall" puis "rules" pour aller dans l'onglet "IPsec" et cliquer sur le "+" à droite du tableau pour ajouter une règle qui va autoriser tous les flux à arriver depuis l'interface IPsec (ceci est bien entendu à adapter selon vos besoins) la configuration de votre règle doit ressembler à cela :  
![6](https://github.com/user-attachments/assets/19473d26-12f7-4266-92bf-4dbeda671147)

Une fois ces deux configurations faites, on peut attendre une bonne minute que les pare-feu négocient la construction du VPN.  


## 3. Test du VPN
Afin de tester le fonctionnement de nos Pfsense, nous pouvons dans un premier temps nous rendre dans la partie "Status" puis "IPsec" :  
![5](https://github.com/user-attachments/assets/ffebd6e4-0608-4975-bbbc-22443272f086)  
Le message en vert dans "Status" indique ici que le VPN est fonctionnel.  
On peut également effectuer un ping entre les deux machines clientes pour vérifier leur bonne communication.  


_[Sources : cette procédure est basée sur un tuto d'[IT-Connect](https://www.it-connect.fr/vpn-site-to-site-ipsec-entre-deux-pfsense/), auquel j'ai apporté des modifications en fonction de mes besoins.]_
