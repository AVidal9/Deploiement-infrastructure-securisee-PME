# Plan d’adressage IP et segmentation réseau

Le réseau est organisé en plusieurs VLANs afin d’assurer la sécurité, la séparation des flux et une meilleure administration.

| VLAN    |	Description    	| Réseau	          | Adresse IP                                      	| Serveurs/Clients      |
| ----    | -----------     | ------            | --------------------                              | ----------------      |
| VLAN 10	| Serveurs  	    | 192.168.10.0/24	  | 192.168.10.1 (Srv-DC01) 192.168.10.2 (Srv-ADCS01) |	Serveurs              |
| VLAN 20	| Infographistes	| 192.168.20.0/24	  | DHCP	                                            | Postes infographistes |
| VLAN 30	| Commerciaux	    | 192.168.30.0/24	  | DHCP	                                            | Postes commerciaux    |
| VLAN 40	| Comptabilité	  | 192.168.40.0/24  	| DHCP	                                            | Postes comptables     |
| LAN     | PfSense	        | Réseau de gestion	| 192.168.10.254                                    |	Passerelle LAN        |	

![Schema Reseau](Images/Pare-feu-PfSense.png)


## Descriptif de chaque rôle installé     
### 1. Active Directory Domain Services (AD DS)    

Le rôle Active Directory Domain Services (AD DS) est au cœur de l’infrastructure client/serveur de Zeety.
Il permet de centraliser la gestion des comptes utilisateurs, des ordinateurs et des ressources réseau au sein d’un domaine unique nommé zeety.com.    

Grâce à AD DS :

Chaque utilisateur dispose d’un compte unique pour accéder à son poste et aux ressources du réseau.

Les droits d’accès et les autorisations sont définis par groupe et par unité d’organisation, ce qui simplifie l’administration.

La sécurité est renforcée, car toutes les connexions sont authentifiées par le contrôleur de domaine (Srv-DC01).

Les sauvegardes et restaurations des comptes sont centralisées, garantissant une meilleure continuité de service.

Ce rôle constitue la base de toute l’infrastructure Windows Server de Zeety.

### 2. Domain Name System (DNS)    

Le rôle DNS (Domain Name System) est indispensable au bon fonctionnement du domaine zeety.com.
Il permet de traduire les noms de machines en adresses IP afin que les ordinateurs puissent se retrouver et communiquer sur le réseau interne.

Exemples :

srv-dc01.zeety.com → 192.168.10.1

srv-adcs01.zeety.com → 192.168.10.2

Ce service assure :

La résolution rapide et fiable des noms internes, évitant les erreurs de communication.

L’intégration étroite avec Active Directory, puisque le DNS stocke les enregistrements de tous les postes et serveurs du domaine.

Une meilleure stabilité réseau, car les ressources sont identifiées par nom plutôt que par adresse IP.

Le rôle DNS est hébergé sur Srv-DC01.

### 3. Dynamic Host Configuration Protocol (DHCP)    

Le rôle DHCP est chargé d’attribuer automatiquement les adresses IP aux postes clients du réseau.
Au lieu de configurer manuellement chaque machine, le serveur DHCP distribue une adresse IP et les paramètres réseau (passerelle, DNS, domaine) dès la connexion.

Ce service permet :

Un gain de temps dans la gestion du parc informatique.

Une réduction des erreurs de configuration (erreurs d’adresses IP, doublons, etc.).

Une meilleure flexibilité, notamment pour les postes portables ou les nouveaux employés.

Le serveur Srv-DC01 gère plusieurs plages DHCP, une par VLAN (infographistes, commerciaux, comptabilité), afin de maintenir la segmentation réseau et la sécurité.

### 4. Active Directory Certificate Services (ADCS)     

Le rôle ADCS (Active Directory Certificate Services) permet à Zeety de disposer d’une autorité de certification interne.
Cette autorité émet et gère des certificats numériques utilisés pour sécuriser les échanges au sein du réseau.

Les certificats servent à :

Chiffrer les communications (ex. : HTTPS, authentification, messagerie interne).

Garantir l’identité des serveurs et des utilisateurs, en s’assurant que les connexions proviennent bien de sources fiables.

Renforcer la confiance et la conformité dans les échanges de données sensibles (comme les notes de frais et heures supplémentaires).

Le rôle ADCS est installé sur Srv-ADCS01, qui héberge l’autorité racine Zeety-RootCA.
Ce serveur est isolé dans le VLAN Serveurs et protégé pour éviter toute compromission.
