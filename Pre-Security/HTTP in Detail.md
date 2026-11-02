# HTTP in Detail — TryHackMe

**Parcours :** Pre Security  
**Lab :** HTTP in Detail  
**Environnement de pratique complémentaire :** Kali Linux / VirtualBox  
**Thèmes :** HTTP, HTTPS, requêtes web, réponses serveur, en-têtes et cookies

## Résumé en anglais

This write-up covers what I learned about HTTP (Hypertext Transfer Protocol) during the TryHackMe Pre Security learning path.

I studied how web browsers communicate with servers, how HTTP requests and responses are structured, and the roles of methods, status codes, headers and cookies.

I later reinforced these concepts during my SEC0 certification preparation by running a local HTTP server on Kali Linux, inspecting requests and responses, and observing unencrypted HTTP traffic.

---

## 1. Objectif

L'objectif de ce lab était de comprendre comment un navigateur communique avec un serveur web grâce au protocole HTTP (Hypertext Transfer Protocol, ou protocole de transfert hypertexte).

Lorsque je consulte un site, mon navigateur envoie des requêtes au serveur pour récupérer les ressources nécessaires : pages HTML, images, fichiers ou autres contenus.

Je voulais comprendre comment ces échanges sont organisés, quelles informations sont transmises et comment interpréter les réponses d'un serveur.

## 2. Comprendre HTTP et HTTPS

### Le protocole HTTP

HTTP est un protocole utilisé pour échanger des ressources entre un client et un serveur web.

Le client, généralement un navigateur, envoie une requête. Le serveur traite cette demande et retourne une réponse.

Le fonctionnement repose donc sur un échange :

```text
Client                         Serveur web
  |                                 |
  | -------- Requête HTTP --------> |
  |                                 |
  | <-------- Réponse HTTP -------- |
  |                                 |
```

### La différence entre HTTP et HTTPS

HTTPS signifie *Hypertext Transfer Protocol Secure*.

Il s'agit de HTTP utilisé avec une connexion sécurisée par TLS (Transport Layer Security, ou sécurité de la couche transport).

La différence essentielle concerne la protection des échanges.

| HTTP | HTTPS |
|---|---|
| Données applicatives non chiffrées | Données HTTP chiffrées par TLS |
| Port 80 par défaut | Port 443 par défaut |
| Contenu potentiellement lisible sur le réseau | Contenu protégé contre la lecture par un observateur réseau |
| Pas de protection cryptographique intégrée | Authentification du serveur via certificat, sous réserve de validation correcte |

Cette distinction est particulièrement importante en cybersécurité.

Une connexion HTTP peut exposer les informations échangées à un observateur ayant accès au trafic. HTTPS protège notamment la confidentialité et l'intégrité des données transmises.

## 3. Les requêtes et les réponses HTTP

### Comprendre une URL

Une URL (Uniform Resource Locator, ou localisateur uniforme de ressource) indique comment accéder à une ressource.

Elle peut contenir plusieurs éléments :

```text
https://example.com:443/blog?id=1#commentaires
```

| Élément | Exemple | Fonction |
|---|---|---|
| Protocole | `https` | Définit la méthode d'accès |
| Hôte | `example.com` | Identifie le serveur |
| Port | `443` | Identifie le service réseau |
| Chemin | `/blog` | Désigne la ressource |
| Paramètre de requête | `id=1` | Transmet une information supplémentaire |
| Fragment | `commentaires` | Désigne une partie de la page |

Le lab présentait également la possibilité d'inclure des informations d'authentification dans certaines URL. En pratique, il faut éviter d'y placer des mots de passe, notamment en raison des risques de divulgation.

### Structure d'une requête HTTP

Voici un exemple de requête étudiée :

```http
GET / HTTP/1.1
Host: tryhackme.com
User-Agent: Mozilla/5.0
Referer: https://tryhackme.com/

```

Chaque ligne a un rôle :

- `GET` indique l'action demandée.
- `/` représente la ressource demandée, ici la racine du site.
- `HTTP/1.1` indique la version du protocole.
- `Host` précise le nom du site auquel la requête est destinée.
- `User-Agent` fournit des informations sur le logiciel client.
- `Referer` indique l'adresse de la page à l'origine de la navigation, lorsqu'elle est transmise.

La ligne vide marque la fin des en-têtes de la requête.

### Structure d'une réponse HTTP

Le serveur peut retourner une réponse comme celle-ci :

```http
HTTP/1.1 200 OK
Server: nginx/1.15.8
Content-Type: text/html
Content-Length: 98

<html>
  <body>Welcome to TryHackMe</body>
</html>
```

Dans cet exemple :

- `HTTP/1.1` indique la version du protocole.
- `200 OK` indique que la requête a réussi.
- `Server` fournit une information sur le logiciel serveur.
- `Content-Type` précise le type de contenu retourné.
- `Content-Length` indique la taille du corps de la réponse, en octets.
- Le contenu HTML constitue le corps de la réponse.

HTML signifie *Hypertext Markup Language*, ou langage de balisage hypertexte.

J'ai retenu qu'une réponse HTTP ne contient pas uniquement la page demandée : elle fournit aussi des informations permettant au client de comprendre comment traiter cette réponse.

## 4. Les méthodes HTTP

Les méthodes HTTP permettent au client de préciser l'action qu'il souhaite effectuer sur une ressource.

Le lab présentait quatre méthodes principales.

| Méthode | Fonction | Exemple |
|---|---|---|
| GET | Récupérer une ressource | Consulter un article |
| POST | Envoyer des données | Créer un compte |
| PUT | Créer ou remplacer l'état d'une ressource | Mettre à jour des informations |
| DELETE | Demander la suppression d'une ressource | Supprimer une image |

Le choix de la méthode dépend du fonctionnement de l'application et de son interface de programmation.

J'ai surtout retenu la distinction entre récupérer une information avec `GET` et envoyer des données avec `POST`.

Les méthodes sont également importantes lors de l'analyse d'une application web, car elles indiquent le type d'opération demandé au serveur.

## 5. Les codes de statut HTTP

Chaque réponse HTTP contient un code de statut permettant de connaître le résultat de la requête.

### Les grandes catégories

| Codes | Catégorie | Signification |
|---|---|---|
| 100–199 | Information | La requête est en cours de traitement |
| 200–299 | Succès | La requête a réussi |
| 300–399 | Redirection | Une autre action ou destination est indiquée |
| 400–499 | Erreur côté client | La requête ne peut pas être satisfaite dans les conditions présentes |
| 500–599 | Erreur côté serveur | Le serveur a rencontré un problème |

### Les principaux codes étudiés

| Code | Signification | Exemple |
|---|---|---|
| 200 OK | Requête réussie | Page récupérée |
| 201 Created | Ressource créée | Nouveau compte créé |
| 301 Moved Permanently | Redirection permanente | Page déplacée |
| 302 Found | Redirection temporaire | Redirection vers une autre adresse |
| 400 Bad Request | Requête incorrecte | Paramètre invalide |
| 401 Unauthorized | Authentification requise ou invalide | Accès sans authentification valide |
| 403 Forbidden | Accès refusé | Permissions insuffisantes |
| 404 Not Found | Ressource introuvable | Page inexistante |
| 405 Method Not Allowed | Méthode non autorisée | Méthode non acceptée par la ressource |
| 500 Internal Server Error | Erreur interne du serveur | Échec du traitement |
| 503 Service Unavailable | Service indisponible | Maintenance ou surcharge |

Ces codes sont utiles pour diagnostiquer un problème.

Par exemple, un code `404` ne signifie pas la même chose qu'un code `500`. Le premier indique que la ressource demandée n'a pas été trouvée, tandis que le second signale une erreur interne au serveur.

## 6. Les en-têtes HTTP

Les en-têtes, ou *headers*, transmettent des informations complémentaires entre le client et le serveur.

### En-têtes de requête

| En-tête | Rôle |
|---|---|
| Host | Identifie le site demandé |
| User-Agent | Identifie le logiciel client |
| Content-Length | Indique la taille du corps envoyé |
| Accept-Encoding | Indique les formats de compression acceptés |
| Cookie | Transmet des cookies au serveur |

### En-têtes de réponse

| En-tête | Rôle |
|---|---|
| Set-Cookie | Demande au navigateur de stocker un cookie |
| Cache-Control | Définit des règles de mise en cache |
| Content-Type | Précise le type de contenu retourné |
| Content-Encoding | Indique l'encodage ou la compression appliquée au contenu |

J'ai compris que les en-têtes sont essentiels pour interpréter correctement une communication HTTP.

Ils peuvent aussi fournir des informations utiles pendant une analyse technique, par exemple le type de contenu transmis ou la manière dont une session est maintenue.

## 7. Le fonctionnement des cookies

HTTP est un protocole sans état (*stateless*).

Cela signifie qu'il ne conserve pas, à lui seul, le contexte des requêtes précédentes.

Les cookies permettent notamment de maintenir certaines informations entre plusieurs requêtes.

Le fonctionnement présenté dans le lab repose sur deux en-têtes :

1. `Set-Cookie` : le serveur demande au navigateur d'enregistrer un cookie.
2. `Cookie` : le navigateur peut renvoyer ce cookie dans les requêtes suivantes, selon les règles applicables.

Les cookies peuvent servir à conserver des préférences ou à gérer une session authentifiée.

Ils ne contiennent pas nécessairement un mot de passe : une application peut utiliser un identifiant de session.

Le lab présentait également l'utilisation des outils de développement du navigateur, notamment l'onglet Réseau (*Network*), pour examiner les requêtes et les cookies échangés.

Du point de vue de la cybersécurité, cette partie m'a permis de comprendre pourquoi la gestion des cookies et des sessions est importante dans la sécurité des applications web.

## 8. Mise en pratique — Révisions SEC0

*Cette partie correspond à des manipulations complémentaires réalisées pendant ma préparation à la certification Pre Security (SEC0), et non aux exercices directement effectués dans la room HTTP in Detail.*

### Mise en place d'un serveur HTTP local

Pendant mes révisions, j'ai travaillé sur un serveur HTTP local depuis Kali Linux, exécuté dans VirtualBox.

L'objectif était d'observer une communication HTTP et de retrouver les éléments étudiés dans le lab.

J'ai notamment examiné une requête envoyée au serveur et sa réponse.

### Analyse de la requête et de la réponse

Dans les échanges observés, j'ai retrouvé les éléments suivants :

```http
GET / HTTP/1.0
Host: localhost
```

La méthode `GET` indique une demande de récupération de ressource.

L'en-tête `Host` identifie l'hôte ciblé. Il ne remplace pas la méthode HTTP : les deux informations ont des rôles différents.

Le serveur a retourné une réponse indiquant :

```http
HTTP/1.0 200 OK
```

Le code `200 OK` confirme que la requête a été traitée avec succès.

J'ai également pu retrouver le texte suivant dans le contenu transmis :

```text
bonjour Kali !
```

Cette observation m'a permis de vérifier concrètement la structure d'un échange HTTP.

### Observation du trafic réseau

Pendant les révisions, j'ai aussi utilisé `tcpdump` pour observer le trafic réseau.

`tcpdump` est un outil de capture et d'analyse des paquets réseau depuis un terminal.

Dans le cas d'un échange HTTP non chiffré, il est possible de retrouver des informations applicatives dans les données capturées, notamment les requêtes et le contenu transmis.

Cette manipulation m'a aidé à comprendre pourquoi HTTP seul n'est pas adapté à la transmission d'informations sensibles.

### Ce que cette pratique m'a apporté

Les notions étudiées dans le lab sont devenues plus concrètes lorsque j'ai pu examiner les échanges depuis Kali Linux.

J'ai pu :

- Identifier une méthode HTTP dans une requête.
- Distinguer la méthode `GET` de l'en-tête `Host`.
- Interpréter une réponse `200 OK`.
- Retrouver du contenu transmis en clair.
- Faire le lien entre les échanges HTTP et l'observation du trafic réseau.

Ces exercices m'ont également permis de commencer à raisonner comme un administrateur réseau ou un analyste sécurité : observer les échanges, identifier les informations utiles et expliquer ce qui se passe.

## 9. Bilan et apprentissages

Ce lab m'a permis de mieux comprendre le fonctionnement des communications web.

Avant, je connaissais HTTP et HTTPS principalement comme les protocoles utilisés pour consulter des sites. Maintenant, je comprends mieux la structure des requêtes, les méthodes, les codes de statut et le rôle des en-têtes.

J'ai aussi découvert l'importance des cookies dans la gestion des sessions.

Les manipulations effectuées pendant mes révisions SEC0 m'ont permis de retrouver ces notions dans de véritables échanges réseau.

Je retiens surtout qu'il est important de savoir lire une requête et une réponse HTTP avant de chercher à analyser le fonctionnement ou la sécurité d'une application web.

Ces bases me seront utiles aussi bien en administration système et réseau qu'en cybersécurité, notamment pour le diagnostic de services web et l'analyse du trafic.

## 10. Outils et notions abordés

**Outils utilisés pendant les révisions**
- Kali Linux
- VirtualBox
- Serveur HTTP local
- `tcpdump`

**Notions étudiées**
- HTTP (Hypertext Transfer Protocol)
- HTTPS (Hypertext Transfer Protocol Secure)
- TLS (Transport Layer Security)
- URL (Uniform Resource Locator)
- IP (Internet Protocol)
- HTML (Hypertext Markup Language)
- Méthodes GET, POST, PUT et DELETE
- Codes de statut HTTP
- En-têtes HTTP
- Cookies et sessions
- Communications client-serveur
- Trafic HTTP non chiffré

---

**Plateforme :** TryHackMe  
**Parcours :** Pre Security  
**Lab :** HTTP in Detail

*Ce write-up comprend des exercices complémentaires réalisés pendant ma préparation à la certification SEC0.*
