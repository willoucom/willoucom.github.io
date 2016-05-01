---
layout: post
title:  "Présentation de Chef"
date:   2016-05-01 10:08:44 +0000
categories: Chef
---

Chef est un outil d’orchestation qui permet de gérer les configurations de différents serveurs et services.

L’orchestration consiste à piloter la configuration d’un Node (un serveur) et à s’assurer que cette configuration est bien conforme à un ensemble de règles, appelées Recipes.

L’objectif de ce type d’outils est de déployer la même configuration sur un ensemble de serveurs, par exemple pour industrialiser et automatiser la mise en production de nouveaux serveurs.

## Objectifs
L’objectif de ce guide est de comprendre les principes de bases de l’outil afin de mieux comprendre dans quelle mesure il pourrait être mis en application.

Le guide permet de présenter les différents éléments qui composent Chef et leurs différentes imbrications.

Ce guide est à destination des développeurs ou administrateurs systèmes curieux.

A la fin du guide sont présentés deux cas simples d’utilisation.

## Vocabulaire

#### La Workstation
Une Workstation est l’outil du devop, c’est sur celle-ci que toute les opérations sont réalisées. Toute la configuration de Chef y est répliquée, elle est nommée “Chef-repo” et peut être poussé dans un dépôt Git (ou autre).

L’outil de base pour gérer Chef est appelé Knife, qui doit être installé sur la Workstation, de manière à pouvoir piloter Chef.
Chaque Workstation est identifiée sur le serveur par un identifiant utilisateur ; il n’y a pas de différence entre un utilisateur et une Workstation.

#### Les Nodes
Un Node (noeud) est un serveur (physique ou virtuel), un équipement réseau ou un conteneur.

Dans la logique de Chef, tout ce qui peut être orchestré est un Node.
Sur chaque Node est installé un client appelé Chef-client, qui va récupérer la configuration sur le Chef-server, et l’appliquer sur le Node.

#### Le Chef-server
Le Chef-server est un point central, également appellé hub, qui permet de propager les changements de configuration. Un Node va venir se connecter sur le Chef-server, il faut donc qu’il soit accessible par les Nodes.

#### Les Cookbooks et les Roles
Les Cookbooks sont des ensembles de commandes. Créer un Cookbook est relativement simple, le langage utilisé est le Ruby. Avec de bonnes bases de scripting, il est possible de réaliser quelques Cookbooks gérant l’installation et la configuration de serveurs.

Par convention, on utilise un Cookbook par service. Par exemple, on créera un Cookbook “Apache” qui va gérer la configuration d’Apache.
Un Cookbook peut être dépendant d’un autre Cookbook. Par exemple le Cookbook “Apache” va dépendre du Cookbook “yum” qui se charge d’installer le serveur Apache.

Les Roles sont un ensemble de Cookbook que l’on peut appliquer à un Node. Par exemple le Role “serveur lamp7” va contenir les Cookbooks “Linux”, “Apache”, “MySQL” et “PHP 7” qui vont respectivement maintenir la configuration du système Linux, veiller à ce que les logiciels Apache, MySQL et PHP soient installés à la bonne version, et que leur configuration soit correcte.

## Cas d’utilisation

#### Installer un logiciel sur tout mon parc de serveurs
Vous avez besoin d’installer un logiciel sur l’ensemble de votre parc, par exemple votre agent de sauvegarde ou de supervision.

Dans ce cas vous pouvez affecter un Cookbook sur le Role “Default” et propager la configuration sur l’ensemble de votre parc.  

#### Vérifier la configuration du serveur SSH
Vous voulez vérifier que vous avez bien changé le port SSH sur vos serveurs et que l’utilisateur root n’est plus autorisé à se connecter, et également que le port est bien ouvert dans iptables.

Par la même occasion, vous voulez en profiter pour supprimer les clés SSH inutiles sur votre compte administrateur et vérifier que vous êtes bien présents dans les sudoers.

Dans ce cas, vous créez un Cookbook “Configuration SSH” qui va :

- Ouvrir le port dans iptables
- Ajouter votre clé ssh dans ~/.ssh/authorized_keys et supprimer les clés inutiles
- Ajouter votre utilisateur dans /etc/sudoers
- Modifier la configuration de ssh et relancer le serveur ssh


## Conclusion et perspectives
Vous venez de prendre connaissance du vocabulaire de base utilisé dans chef.<br/>
Vous avez également vu quelques exemples d’utilisation simple.<br/>
Comme vous l’aurez compris, Chef est un outil pour lequel le vocabulaire est aussi important que le fonctionnement.

Nous allons par la suite voir les cas d’utilisation suivants :

- Installer Chef-server
- Utiliser Chef-client
- Créer un Cookbook
