# DNS in Detail — TryHackMe

**Parcours :** Pre Security  
**Lab :** DNS in Detail  
**Environnement de révision :** Kali Linux / VirtualBox  
**Thèmes :** DNS, résolution de noms, réseaux

## English summary

This write-up covers what I learned about the Domain Name System (DNS) during the TryHackMe Pre Security learning path.

I studied how domain names are structured, the purpose of different DNS records, and how DNS resolution works.

I later revisited these concepts while preparing for the Pre Security (SEC0) certification, using `dig` on Kali Linux to perform DNS queries and analyze the results.

---

## 1. Objectif

L'objectif de ce lab était de comprendre le fonctionnement du **DNS (Domain Name System, système de noms de domaine)** et son importance dans les communications réseau.

Lorsqu'on accède à un site web, on utilise généralement un nom de domaine plutôt qu'une adresse **IP (Internet Protocol, protocole Internet)**. Par exemple, il est plus simple de retenir `tryhackme.com` qu'une adresse comme `104.26.10.229`.

Le DNS permet notamment de faire la correspondance entre ces deux informations.

Je voulais surtout comprendre ce qui se passe entre le moment où l'on saisit un nom de domaine et celui où notre machine obtient l'adresse IP nécessaire pour communiquer avec le serveur.

## 2. Comprendre le DNS

### La hiérarchie des noms de domaine

Un nom de domaine possède une structure hiérarchique. Prenons l'exemple `admin.tryhackme.com` :

| Élément | Valeur | Rôle |
|---|---|---|
| TLD (Top-Level Domain) | `.com` | Domaine de premier niveau |
| SLD (Second-Level Domain) | `tryhackme` | Domaine de second niveau |
| Sous-domaine | `admin` | Partie précédant le domaine principal |

J'ai appris à distinguer deux catégories de domaines de premier niveau :

- **gTLD (Generic Top-Level Domain)** : domaine générique de premier niveau, comme `.com`, `.org` ou `.edu`.
- **ccTLD (Country Code Top-Level Domain)** : domaine de premier niveau associé à un pays ou territoire, comme `.fr`, `.ca` ou `.uk`.

Le lab abordait également les contraintes de nommage, notamment la limite de **63 caractères pour un label** de sous-domaine et celle de **253 caractères pour un nom de domaine complet**, dans le cadre présenté.

Cette partie m'a aidé à mieux comprendre l'organisation des domaines et des sous-domaines que l'on retrouve dans les infrastructures informatiques.

### Les différents types d'enregistrements DNS

Le DNS ne sert pas uniquement à retrouver une adresse IP. Il permet aussi de publier différentes informations liées à un domaine.

| Enregistrement | Signification | Fonction |
|---|---|---|
| A | Address | Associe un nom de domaine à une adresse IPv4 (Internet Protocol version 4) |
| AAAA | Quadruple A (quatre A) | Associe un nom de domaine à une adresse IPv6 (Internet Protocol version 6) |
| CNAME | Canonical Name | Associe un nom à un autre nom de domaine |
| MX | Mail Exchange | Indique les serveurs chargés de recevoir les emails |
| TXT | Text | Contient des informations textuelles utilisées pour différents services |

#### Enregistrements A et AAAA

Les enregistrements A et AAAA servent à retrouver une adresse IP. Le type A concerne IPv4, tandis que AAAA concerne IPv6.

Exemples étudiés dans le lab :

```text
A     104.26.10.229
AAAA  2606:4700:20::681a:be5
```

Cette distinction est importante : une requête DNS peut demander un type d'enregistrement précis.

#### Enregistrement CNAME

**CNAME (Canonical Name, nom canonique)** permet de faire correspondre un nom de domaine à un autre.

L'exemple présenté dans le lab était :

```text
store.tryhackme.com
        |
        v
shops.shopify.com
```

Il faut ensuite résoudre le nom cible pour obtenir l'adresse IP permettant d'établir la communication.

#### Enregistrement MX

**MX (Mail Exchange, échange de courrier électronique)** identifie les serveurs chargés de recevoir les emails d'un domaine.

Ces enregistrements possèdent également une priorité, qui aide à déterminer dans quel ordre les serveurs doivent être contactés.

#### Enregistrement TXT

**TXT (Text, texte)** permet de stocker des informations textuelles associées à un domaine, notamment pour la vérification de propriété et certaines configurations de sécurité des emails.

Le lab présentait des exemples liés à :

- **SPF (Sender Policy Framework)** : mécanisme permettant d'indiquer quels serveurs sont autorisés à envoyer des emails pour un domaine.
- **DMARC (Domain-based Message Authentication, Reporting and Conformance)** : mécanisme permettant de définir une politique de traitement des emails qui échouent aux contrôles d'authentification et de recevoir des rapports.

Ces mécanismes participent à la protection des services de messagerie contre certaines formes d'usurpation.

## 3. Le fonctionnement de la résolution DNS

Cette partie m'a permis de comprendre le parcours d'une requête DNS. Lorsque nous recherchons un nom de domaine, plusieurs vérifications peuvent intervenir.

### Étape 1 — Vérification du cache local

La machine vérifie d'abord si elle possède déjà une réponse DNS en cache. Si cette réponse est encore valide, elle peut être réutilisée.

### Étape 2 — Consultation du résolveur DNS récursif

Si aucune réponse n'est disponible localement, la requête est transmise à un **résolveur DNS récursif (Recursive DNS Resolver)**.

Ce serveur est généralement fourni par le **FAI (Fournisseur d'Accès à Internet)**, mais il est possible d'en utiliser un autre. Le résolveur vérifie lui aussi son cache.

### Étape 3 — Interrogation des serveurs racine

Si le résolveur ne dispose pas de la réponse, il peut interroger un **serveur DNS racine (Root DNS Server)**.

Celui-ci permet de retrouver les serveurs responsables du domaine de premier niveau demandé. Par exemple, pour `www.tryhackme.com`, il oriente vers les serveurs associés à `.com`.

### Étape 4 — Consultation du serveur TLD

Le serveur **TLD (Top-Level Domain Server, serveur du domaine de premier niveau)** fournit les informations nécessaires pour retrouver les serveurs DNS faisant autorité pour le domaine.

### Étape 5 — Réponse du serveur DNS autoritaire

Le **serveur DNS autoritaire (Authoritative DNS Server)** possède les enregistrements DNS du domaine dont il est responsable.

Il fournit les informations demandées, qui sont ensuite transmises au résolveur récursif, puis au client.

### Le cache DNS et le TTL

Le **TTL (Time To Live, durée de vie)** indique, en secondes, combien de temps une réponse DNS peut être conservée en cache.

Ce mécanisme évite de répéter inutilement les mêmes recherches et contribue à améliorer les performances.

J'ai compris qu'une modification DNS n'est donc pas nécessairement visible immédiatement partout : certaines réponses peuvent rester en cache jusqu'à l'expiration de leur TTL.

## 4. Mise en pratique — Révisions SEC0

*Cette partie correspond à un exercice complémentaire de préparation à la certification Pre Security (SEC0), distinct du lab DNS in Detail.*

Après avoir étudié les enregistrements DNS, j'ai repris ces notions dans un terminal Kali Linux afin de mieux comprendre leur utilisation.

### Interroger les enregistrements A avec dig

Commande utilisée pendant les révisions :

```bash
dig example.com A +short
```

Décomposition de la commande :

- `dig` (**Domain Information Groper**) : outil permettant d'interroger le DNS.
- `example.com` : nom de domaine recherché.
- `A` (**Address**) : type d'enregistrement demandé, correspondant aux adresses IPv4.
- `+short` : affiche une version simplifiée de la réponse.

### Résultats obtenus

Les résultats relevés pendant cet exercice étaient :

```text
104.20.23.154
172.66.147.243
```

La requête a retourné deux adresses IPv4 pour un même domaine. Un nom de domaine n'est donc pas nécessairement associé à une seule adresse IP.

Selon l'infrastructure, plusieurs adresses peuvent notamment être utilisées pour répartir le trafic ou permettre l'accès à différents serveurs. Les réponses DNS peuvent aussi évoluer au fil du temps.

### Analyse des résultats

Cette manipulation m'a permis de faire le lien entre la théorie et l'utilisation d'un outil réseau.

J'ai notamment retenu que :

- Une requête de type A permet de rechercher les adresses IPv4 associées à un domaine.
- Une réponse DNS peut contenir plusieurs adresses.
- `dig` permet d'interroger le DNS depuis un terminal.
- L'option `+short` simplifie l'affichage, mais ne présente pas toutes les informations de la réponse.

Cette commande ne permet pas, à elle seule, de visualiser toutes les étapes de la résolution récursive.

Pour examiner davantage d'informations, comme le TTL, je peux utiliser `dig` sans l'option `+short`.

## 5. Bilan et apprentissages

Ce lab m'a permis de mieux comprendre un service que l'on utilise constamment sans forcément y prêter attention.

Avant, je savais que le DNS permettait de retrouver une adresse IP à partir d'un nom de domaine. Maintenant, je comprends mieux les différents serveurs impliqués, le rôle des enregistrements et l'intérêt de la mise en cache.

Les révisions SEC0 m'ont ensuite permis d'utiliser `dig` pour interroger des enregistrements DNS et interpréter les adresses retournées.

Je retiens également l'importance du DNS en administration système et réseau. Une mauvaise configuration peut empêcher l'accès à un service, tandis que l'analyse des enregistrements DNS peut apporter des informations utiles sur une infrastructure.

Pour moi, comprendre ces mécanismes est une étape nécessaire avant d'aller plus loin dans l'analyse et la sécurisation des réseaux.

## 6. Outils et notions abordés

**Outils utilisés**

- Kali Linux
- VirtualBox
- `dig` (Domain Information Groper)

**Notions étudiées**

- DNS (Domain Name System)
- IP (Internet Protocol), IPv4 (version 4) et IPv6 (version 6)
- TLD (Top-Level Domain), SLD (Second-Level Domain)
- gTLD (Generic Top-Level Domain), ccTLD (Country Code Top-Level Domain)
- Enregistrements A (Address), AAAA (Quadruple A), CNAME (Canonical Name), MX (Mail Exchange) et TXT (Text)
- SPF (Sender Policy Framework) et DMARC (Domain-based Message Authentication, Reporting and Conformance)
- Résolveur DNS récursif, serveurs DNS racine et autoritaires
- TTL (Time To Live)

---

**Plateforme :** TryHackMe  
**Parcours :** Pre Security  
**Lab :** DNS in Detail

*Ce write-up comprend un exercice complémentaire de préparation à la certification SEC0.*
