# 🌐 Network Fundamentals — TryHackMe

**Learning Path :** Pre Security  
**Module :** Network Fundamentals  
**Domaine :** Réseaux / Networking  
**Langue :** Français 🇫🇷

## 🇬🇧 English Summary

This write-up summarizes the **Network Fundamentals** module from TryHackMe's *Pre Security* learning path. It covers how devices communicate, IP and MAC addressing, local network topologies, the OSI model, packets and frames, TCP and UDP, as well as basic network protection technologies. It focuses on the concepts I studied; practical results are not claimed unless documented.

---

## 📌 Objectif

Comprendre comment les appareils communiquent au sein d'un réseau, comment les données circulent entre eux et quels protocoles permettent ces échanges.

Ce module m'a permis d'aborder plusieurs notions essentielles : l'adressage IP, les réseaux locaux, le modèle OSI, les paquets et les trames, les protocoles TCP et UDP, ainsi que les équipements qui assurent la communication et la sécurité des réseaux.

L'objectif est de comprendre ce qui se passe réellement lorsqu'une machine communique avec une autre, avant d'approfondir leur administration et leur sécurisation.

---

## 🔍 1. Comprendre le fonctionnement d'un réseau

### Réseau local et Internet

Un réseau informatique est un ensemble d'appareils connectés qui peuvent communiquer et échanger des données.

J'ai notamment étudié la différence entre :

- **LAN (Local Area Network)** : un réseau local, comme celui d'une maison ou d'une entreprise.
- **WAN (Wide Area Network)** : un réseau étendu reliant plusieurs réseaux sur de grandes distances.
- **Internet** : un ensemble de réseaux interconnectés.

### Adressage IP et MAC

Pour communiquer, les appareils doivent pouvoir être identifiés.

- **IP (Internet Protocol)** : une adresse logique utilisée pour identifier une interface réseau et acheminer les paquets.
- **MAC (Media Access Control)** : une adresse utilisée au niveau de la liaison de données pour identifier une interface sur un réseau local.

Une distinction importante : l'adresse IP intervient dans l'acheminement des données entre réseaux, tandis que l'adresse MAC intervient dans leur transmission sur le réseau local.

### ICMP et Ping

Le protocole **ICMP (Internet Control Message Protocol)** permet notamment de transmettre des messages de diagnostic réseau.

L'outil `ping` l'utilise pour tester si une machine répond aux requêtes d'écho.

Exemple étudié :

```bash
ping 8.8.8.8
```

Cette commande envoie des requêtes **ICMP Echo Request** et attend des réponses **Echo Reply**.

Une réponse permet de constater que la destination répond à ce test. En revanche, une absence de réponse ne signifie pas nécessairement que la machine est hors ligne : les messages ICMP peuvent être filtrés.

---

## 🖧 2. Réseaux locaux et topologies

Dans cette partie, j'ai découvert que la manière dont les équipements sont connectés influence directement la fiabilité et la maintenance d'un réseau.

### Les topologies

**Topologie en étoile (Star)**

Les machines sont connectées à un équipement central, généralement un switch.

- **Avantage :** il est relativement simple d'ajouter des appareils et de gérer les connexions.
- **Inconvénient :** si l'équipement central tombe en panne, les communications qui en dépendent sont interrompues.

**Topologie en bus (Bus)**

Les appareils partagent un même support de communication.

Cette organisation nécessite moins de câblage, mais une défaillance du support principal peut perturber l'ensemble du réseau.

**Topologie en anneau (Ring)**

Les appareils sont reliés suivant une boucle. Les données circulent entre les différents équipements selon le fonctionnement du réseau.

### Les équipements réseau

J'ai également étudié plusieurs équipements :

- **Switch :** permet de connecter plusieurs appareils dans un réseau local et d'acheminer les trames vers les ports appropriés.
- **Router :** permet d'acheminer les paquets entre différents réseaux.
- **Firewall :** contrôle les communications selon des règles de sécurité.

### DHCP : attribution automatique des adresses

Le protocole **DHCP (Dynamic Host Configuration Protocol)** permet notamment d'attribuer automatiquement une configuration IP à un appareil.

Le processus étudié se déroule en quatre étapes :

1. **Discover :** le client recherche un serveur DHCP.
2. **Offer :** le serveur propose une configuration.
3. **Request :** le client demande à utiliser la configuration proposée.
4. **Acknowledgement (ACK) :** le serveur confirme l'attribution.

Ce mécanisme évite de devoir configurer manuellement chaque appareil d'un réseau.

---

## 📦 3. Le modèle OSI : comprendre le voyage des données

Le modèle **OSI (Open Systems Interconnection)** représente les communications réseau à travers sept couches.

| Couche | Nom | Rôle principal |
|---|---|---|
| 7 | Application | Services réseau accessibles aux applications |
| 6 | Présentation | Représentation et transformation des données |
| 5 | Session | Gestion des sessions de communication |
| 4 | Transport | Transport des données entre applications |
| 3 | Réseau | Adressage logique et routage |
| 2 | Liaison de données | Communication sur une liaison locale |
| 1 | Physique | Transmission des bits sur le support |

### Encapsulation

Un concept important de ce lab est l'encapsulation.

Lorsque des données sont envoyées, les différentes couches ajoutent les informations nécessaires à leur transmission.

Par exemple, pour une communication TCP sur IP et Ethernet :

**Données → Segment TCP → Paquet IP → Trame Ethernet → Bits**

À la réception, les informations d'encapsulation sont traitées dans le sens inverse.

Ce fonctionnement permet de mieux comprendre pourquoi les protocoles ont des responsabilités différentes.

Un routeur travaille notamment au niveau de la couche 3, tandis qu'un switch Ethernet classique intervient principalement au niveau de la couche 2.

---

## 🔄 4. Packets & Frames : TCP et UDP

### Paquets et trames

Un paquet IP contient notamment les adresses IP source et destination.

Une trame Ethernet encapsule le paquet pour permettre sa transmission sur une liaison locale. Elle contient notamment les adresses MAC source et destination.

Ces deux éléments interviennent donc à des niveaux différents du modèle OSI.

### TCP — Transmission Control Protocol

TCP est un protocole de transport orienté connexion qui permet notamment une transmission fiable et ordonnée des données.

L'établissement d'une connexion TCP repose sur le **Three-Way Handshake** :

1. **SYN :** le client demande l'établissement d'une connexion.
2. **SYN-ACK :** le serveur accuse réception et répond à la demande.
3. **ACK :** le client confirme la réponse du serveur.

Une fois cette étape terminée, les deux machines peuvent échanger leurs données.

### UDP — User Datagram Protocol

Contrairement à TCP, UDP ne nécessite pas l'établissement préalable d'une connexion.

Il n'assure pas lui-même la retransmission des données perdues ni leur livraison dans l'ordre.

Il est notamment utilisé dans des applications où une faible latence est importante.

### Les ports

Les ports permettent de distinguer les services ou applications qui communiquent sur une machine.

Quelques exemples étudiés :

| Port | Service courant |
|---|---|
| 21 | FTP |
| 22 | SSH |
| 53 | DNS |
| 80 | HTTP |
| 443 | HTTPS |

Il faut également distinguer un port ouvert, un port fermé et un port filtré : ces états peuvent apporter des informations utiles lors d'un diagnostic réseau.

---

## 🛡️ 5. Étendre et sécuriser un réseau

La dernière partie du module introduit plusieurs technologies utilisées pour connecter et protéger les réseaux.

### Port Forwarding

La redirection de ports permet de diriger certaines connexions reçues par un équipement réseau vers un service situé sur un réseau interne.

Cette technique peut rendre un service accessible depuis l'extérieur, mais elle augmente également son exposition si elle est mal configurée.

### Firewalls

Un pare-feu applique des règles pour autoriser ou bloquer certaines communications.

Il peut notamment filtrer le trafic selon les adresses IP, les ports et les protocoles.

### VPN — Virtual Private Network

Un VPN permet d'établir une connexion logique entre des appareils ou des réseaux, généralement à travers une infrastructure partagée comme Internet.

Selon la technologie et la configuration utilisées, il peut protéger les communications par chiffrement.

---

## 🎯 Résultat et apprentissages

Ce module m'a permis de mieux comprendre les mécanismes fondamentaux des communications réseau.

Les notions que je retiens particulièrement sont :

- La différence entre les adresses IP et MAC.
- Le rôle des switches et des routeurs.
- L'organisation des sept couches du modèle OSI.
- La différence entre un paquet et une trame.
- Le fonctionnement du Three-Way Handshake TCP.
- Les différences entre TCP et UDP.
- Le rôle du DHCP, des ports, des pare-feu et des VPN.

**Ce que je retiens :** avant de pouvoir sécuriser un réseau, il faut comprendre comment il fonctionne.

Savoir identifier les différentes étapes d'une communication permet de mieux analyser les problèmes de connectivité, les erreurs de configuration et, à terme, les comportements suspects sur une infrastructure.

Ce module constitue donc une base importante pour la suite de mon apprentissage en administration systèmes et réseaux et en cybersécurité.

---

## 📚 Protocoles, outils et concepts étudiés

- **Diagnostic :** `ping`, ICMP.
- **Adressage :** IPv4, IPv6, MAC.
- **Configuration réseau :** DHCP.
- **Modèles réseau :** OSI, encapsulation.
- **Transport :** TCP, UDP, Three-Way Handshake.
- **Équipements :** Switch, Router, Firewall.
- **Services et technologies :** DNS, HTTP, HTTPS, SSH, VPN, Port Forwarding.

---

*Write-up personnel basé sur le module Network Fundamentals de TryHackMe — Pre Security Learning Path. Synthèse des notions étudiées, sans reproduction intégrale du cours.*
