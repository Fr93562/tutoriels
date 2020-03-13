# JWT - Partie théorique


![poc](https://raw.githubusercontent.com/auth0/wp-jwt-auth/master/banner-1544x500.png)



* Auteur du projet: François M. 	
* Contexte: Parcours développeur web et mobile / simplon
* Date: 12/03/20

* Version: 1.0
* Etat du projet: Terminé
-----------------

## Résumé


Le but du tutoriel est de présenter le concept de l'authentification à l'aide du JWT pour sécuriser ses applications.


## Un moyen d'authentification parmi d'autre

Il existe de très nombreuses manières de sécuriser une application à l'aide de l'authentification:

* Uusername / password en clair: On le compare au niveau du back-end. Le principal inconvénient est qu'à chaque requête, on va renvoyer ses identifiants. 

* L'utilisation d'api key: On l'utilise dans le cas des API publiques.

* Oauth: Via un service tierce comme facebook ou google.

* Oauth2 : C'est une version plus évoluée de Oauth.

* JWT: On utilise un token d'authentification à chaque requête.


## Le JWT

### C'est quoi?

JWT signifie ```json web token```, il s'agit d'un "jeton" crypté qui est composé de trois éléments:

- Header : comme son nom l'indique
- Payload : il s'agit de la charge utile. C'est ici que sera stocké les données qu'on souhaite crypter. 
- Signature: comme son nom l'indique xd

La payload peut-être décryptée. C'est la comparaison de la payload avec sa signature qui permet de vérifier si le token est valide et qu'il n'a pas été modifié par un tierce.

Typiquement un JWT ressemble à ceci: 
 eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJsb2dnZWRJbkFzIjoiYWRtaW4iLCJpYXQiOjE0MjI3Nzk2Mzh9.gzSraSYS8EXBxLN_oWnFSRgCzcmJmMjLiuyu5CSpyHI


### Quelle utilité?

Le problème de l'authentification "classique" est principalement lié au manque de sécurité. En effet, il est nécessaire d'envoyer son ``` username / password ``` à chaque requête pour continuer la connexion. Et cette action doit être faite en clair. Donc à chaque requête, des informations sur vos logs de connexion circulent en clair.


Le JWT est l'une des réponses à ce problème:

- Il permet de différencier les données d'un utilisateur de son droit d'accès. Le jeton JWT représente ses droits auprès de l'application back-end.
- Il permet de stocker des informations utiles à l'intérieur du jeton.
- On peut le renouveler au besoin.
- Il y a une vraie liberté en terme d'implémentation: des systèmes de connexion crées à la main aux systèmes intégrés dans les framework (  ``` SpringBoot , Symfony  ...``` )


### Comment on le crée?

Pour se faire, il est nécessaire d'utiliser une librairie JWT. Il en existe de très nombreuses sur le site https://jwt.io/ . Il s'agit du site de référence pour ce moyen d'authentification. Il est nécessaire d'avoir installé la librairie pour s'en servir.

Pour créer votre token, vous aurez besoin de deux éléments:

* choisir un mot secret: C'est une suite de caractère (un string par exemple) qui sera utilisé pour crypter les données à protéger. On utilise souvent le terme de ``` secret word ``` .

* les données contenues dans le token: C'est au choix. Mais ce sera souvent ``` l'username, password, role ... ```


1 - Instancier son objet JWT

2 - Creér son token JWT en passant en argument les données à crypter (qu'on appelle le payloader ou claim ..) et le mot secret

3 - Vous avez un token JWT de crée!

### Comment on le décrypte?

Rien de plus simple!

1 - On récupère notre token depuis le header

2 - On instancie notre objet JWT

3 - On utilise la méthode de décryptage de l'objet JWT avec en argument ``` secret word ```  qui a été utilisé pour le crypter.

4 - Le token est décrypté. Il reste qu'à extraite les informations de sa payload. On les récupère comme n'importe objet java par exemple


! : A noter qu'il existe des méthodes qui facilitent la manipulation des token. Notamment avec la méthode ``` verify ``` qui existe dans certaines librairie. Je l'ai jamais utilisé.


### Utilisé par qui?

On l'utiise dans toutes les applications qui nécessitent d'être sécurisé.

De manière général, les librairies existent sur de très nombreux langages:

* Java 
* PHP 
* .NET
* NodeJS
* Javascript etc ...


## Son utilisation

Voici un exemple avec une application front-end et back-end. Ici l'application back-end est une API Rest. Donc elle est stateless. Ce qui signifie qu'elle ne stocke aucune données entre deux requêtes (donc pas de variable de session).

### Le schéma


#### Connexion

![poc](https://nsa40.casimages.com/img/2020/03/12/200312104809460747.jpg)

* L'user demande la page de connexion au front-end
* L'user rempli les informations sur le formulaire et les envoient
* L'application front envoi les données json à l'application back
* L'existence de l'user est vérifié en base de données

* Si l'user n'existe pas. La réponse sera de type HTTP 400
* sI l'user existe. La réponse sera de type HTTP 201 avec le token en réponse


#### Authentification

![poc](https://nsa40.casimages.com/img/2020/03/12/200312104809630272.jpg)

Prérequis: Connexion réussie

* L'application front demande les ressources au back. Il joint le token d'authentification dans le header
* Le back effectue les vérifications avant traitement (par le biais d'un firewall le plus souvent): il décrypte le token et vérifie si les données correspondent à un user en base. Si l'user existe, une session est ouverte avec ces droits (user, admin..)
* La requête arrive au controller et son accès dépendra de l'authentification précédente


* Si une erreur est levée. La réponse sera de type HTTP 400
* si tout est ok. La réponse sera de type HTTP 201 avec les ressources


## Comment améliorer la sécurité avec JWT?

Il existe plusieurs façons d'améliorer la sécurité avec le JWT.

* Cryptage: Il existe de très nombreux algorithmes de cryptage tel que bcrypt, sha256 ou sha512 (qui sont les plus connus). Selon le niveau de crypate, le token sera plus ou moins facile à décrypter.

* Renouveller le token: Certaines applications renouvellent le token à chaque échange. Ceci est possible soit en modifiant la payload (en incrémentant de 1 par ex) ou en mettant à jour le secretword.

* Installer une date d'expiration: Le token ne sera valide que pendant une certaine durée. Souvent utilisé dans des application dont la réponse attendue doit être rapide. A chaque approche de l'échéance, le token sera renouvellé.

## Partie pratique


https://github.com/Logik-Dev/simple-jwt 

## Les références

https://jwt.io/introduction/

https://www.attineos.com/blog/tutotrompe/tutotrompe-video-symfony (exemple avec Symfony mais la mécanique est la même)

Projet de Josselin: https://github.com/jtobelem-simplon/toh-backend-tests

Exemple from scratch: https://github.com/Fr93562/projet-fil-rouge-Simplon/blob/master/backend/demo-1/src/main/java/fr/api/trivialCode/service/Authentification.java

Exemple d'un autre langage

* Attention: C'est un projet Symfony. Le code d'authentification est généré par le framework. Le code est relativement abstrait!

Système d'authentification: https://github.com/Fr93562/projet-7-DA-PHP/blob/master/src/Security/TokenAuthenticator.php

Configuration du firewall: https://github.com/Fr93562/projet-7-DA-PHP/blob/master/config/packages/security.yaml

-----------------

### Mises à jour:

- Création du projet: 13/03/20
