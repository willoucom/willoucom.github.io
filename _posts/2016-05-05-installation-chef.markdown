---
layout: post
title:  "Installation de Chef-server"
date:   2016-05-05 14:08:44 +0000
categories: Chef
---

Vous voulez avoir à disposition un Chef-server pour faire des tests et évaluer la solution. En suivant ce guide vous aurez un serveur opérationnel en quelques minutes.

## Objectifs
Ce guide s’adresse à tout ceux qui ont des connaissances basiques en administration Linux et qui veulent tester quelques Cookbooks trouvés sur le Supermarket afin de valider l’utilisation de cet outil pour leurs besoins. A la fin de ce guide, vous aurez un Chef-server fonctionnel, avec Chef-manage installé afin de vous aidez lors de la configuration initiale.

A noter que Chef-manage est un outil permettant de gérer le Chef-server via une interface web, cet outil est payant au delà de 25 Nodes ce qui sera largement suffisant pour effectuer des tests ou pour gérer une très petite infrastructure.

Je vous conseille de lire la [présentation de Chef]({% post_url 2016-05-01-presentation-chef %}) avant de commençer 


## Installation Chef-server
Tout d’abord, allez télécharger le programme d’installation sur le site officiel en fonction de votre distribution de Linux au moment de la rédaction de cet article, la dernière version Stable est la 12.5.0.

[https://downloads.chef.io/chef-server/](https://downloads.chef.io/chef-server/){:target="_blank_"}

Télécharger le programme d’installation directement sur le serveur, avec wget ou curl, vous fera gagner du temps.

#### Debian/Ubuntu
Lancez la commande :

`$ dpkg -i chef-server-core*.deb`

#### RedHat/Centos
Lancez la commande :

`$ rpm -i chef-server-core*.rpm`

## Configuration de Chef-server
Une fois votre Chef-server installé, il faut le configurer, l’outil __chef-server-ctl__ est prévu pour ça.
La commande de base _reconfigure_ va se charger de vérifier que la configuration de Chef-server est correcte et installer les dépendances si besoin. A noter que _chef-server-ctl reconfigure_ utilise chef pour reconfigurer chef.
A ce stade il n’est pas nécessaire de modifier la configuration, celle proposée par défaut est suffisante pour un petit environnement de test.

Lancez la commande :

`$ chef-server-ctl reconfigure`

Sur certaines versions d’Ubuntu, cette étape échoue sur une erreur

> Parent directory /usr/lib/systemd/system does not exist.

Lancez la commande :

`$ mkdir /usr/lib/systemd/system`

## Installation de Chef-manage
Pour pouvoir appréhender facilement Chef, il est utile de pouvoir visualiser les différents éléments via une interface web, bien que tout soit disponible via la ligne de commande, c’est une solution simple qui évite de devoir apprendre toute les commandes. Toute la gestion du serveur s’effectue via la commande __chef-server-ctl__, nous allons l’utiliser. Toutefois, chaque module utilise son propre outil, nous utiliserons __chef-manage-ctl__ pour gérer l’interface web.

Lancez les commandes :

`
$ chef-server-ctl install chef-manage
$ chef-server-ctl reconfigure
$ chef-manage-ctl reconfigure
`

## Création du premier compte utilisateur
L’outil __chef-server-ctl__ sert également à gérer les utilisateurs, le nom de la commande est user-create
Remplacez les paramètres comme suit :

- *USER_NAME* = Nom du compte
- *FIRST_NAME* = Prénom
- *LAST_NAME* = Nom
- *EMAIL* = Adresse email
- *PASSWORD* = Mot de passe

`chef-server-ctl user-create USER_NAME FIRST_NAME LAST_NAME EMAIL 'PASSWORD' --filename USER_NAME.pem`

Le mot de passe sera visible dans l’history, pensez à nettoyer votre historique

`history -c`

__Vous allez récupérer une clé qui porte le nom de l’utilisateur, sauvegardez-la en lieu sûr.__

## Création de votre organisation
Par défaut Chef-server vous permet de gérer plusieurs organisations, une Organisation est un ensemble de Nodes, Cookbooks et Utilisateurs. Vous pouvez créer autant d’organisations que vous voulez pour faire vos tests, supprimer une organisation supprimera tout ce qui lui est lié.

L’outil __chef-server-ctl__ sert également à gérer les organisations, le nom de la commande est org-create
Remplacez les paramètres comme suit :

- *ORGANISATION_SHORTNAME* = Nom court de l’organisation (sans aucun espace ni caractère spécial)
- *ORGANISATION_FULLNAME* = Nom long de l’organisation (peut être le même que ORGANISATION_SHORTNAME)
- *USER_NAME* = Nom du premier utilisateur, qui sera par défaut administrateur, doit être un utilisateur existant

`chef-server-ctl org-create ORGANISATION_SHORTNAME 'ORGANISATION_FULLNAME' --association_user USER_NAME --filename ORGANISATION_SHORTNAME-validator.pem`

__Vous allez récupérer une clé qui porte le nom de l’organisation, sauvegardez-la en lieu sûr.__

## Connexion à Chef-manage

Vous pouvez maintenant vous connecter au manager sur l'adresse :

`http://ip_de_votre_serveur:80`

__L'utilisateur et le mot de passe sont ceux que vous avez utilisé pour la création de votre premier utilisateur__

## Conclusion et perspectives
Vous avez maintenant un Chef-server fonctionnel. Vous avez utilisé l’outil chef-server-ctl qui vous permet de contrôler votre serveur, et ainsi vous avez installé un module, crée un utilisateur et une organisation.

Vous pouvez vous connecter sur le manager de chef avec votre navigateur web sur le port http de votre serveur.
Prenez un peu de temps pour consulter les différents onglets. Les onglets Nodes et Policy sont les plus utiles, par défaut vous n’avez aucun Node d’enregistré, et les Policy ne sont pas encore configurées.

Les clés utilisateur et organisation vous permettront de vous connecter à votre serveur depuis l’outil Knife directement depuis votre poste de travail.
