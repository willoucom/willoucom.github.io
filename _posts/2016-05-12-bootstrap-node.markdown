---
layout: post
title:  "Bootstrap d’un Node"
date:   2016-05-12 13:30:44 +0000
categories: Chef
---

Le Bootstrapping consiste à configurer un Node de façon à ce que le Chef-server puisse le piloter.

Bien qu’il soit possible d’utiliser Chef-manage pour gérer le serveur, je préfère utiliser l’outil Knife qui évite de jongler entre deux outils. Je ne traiterai donc pas l’utilisation de Chef-manage, sauf pour vérifier que les commandes ont fonctionné.

## Objectifs
L’objectif de ce guide est de Bootstrap un Node, de lui affecter un Role et d’utiliser un Cookbook de test.

## Bootstrap son premier Node
Dans Chef, pour qu’un Node puisse être géré par le serveur, il faut installer quelques outils. Le moyen le plus simple et le plus efficace est le Bootstrapping.

Tout d’abord, vérifiez que vous avez un accès ssh sur la machine, avec l’utilisateur root ou un utilisateur qui peut passer root avec sudo.

Ensuite lancez la commande suivante :

```
$ knife bootstrap monserveur.domaine.ext
```

Si vous devez utiliser sudo :

```
$ knife bootstrap monserveur.domaine.ext --ssh-user utilisateur --sudo
```

Vous devriez avoir un retour similaire à ceci :

```
$ knife bootstrap monserveur.willou.com

Connecting to monserveur.willou.com
monserveur.willou.com -----> Installing Chef Omnibus (-v 12)
monserveur.willou.com downloading https://omnitruck-direct.chef.io/chef/install.sh
monserveur.willou.com   to file /tmp/install.sh.11835/install.sh
monserveur.willou.com trying wget...
monserveur.willou.com debian 8.4 x86_64
monserveur.willou.com Getting information for chef stable 12 for debian...
monserveur.willou.com downloading https://omnitruck-direct.chef.io/stable/chef/metadata?v=12&p=debian&pv=8.4&m=x86_64
monserveur.willou.com   to file /tmp/install.sh.11842/metadata.txt
monserveur.willou.com trying wget...
monserveur.willou.com sha1    ee9f52e9fabec20979c1b79e91d57d055a87896f
monserveur.willou.com sha256  6ee8e2f463593f5a78aa3c085348803933e9424c637da37e92dce2db792beaf9
monserveur.willou.com url     https://packages.chef.io/stable/debian/8/chef_12.9.38-1_amd64.deb
monserveur.willou.com version 12.9.38
monserveur.willou.com downloaded metadata file looks valid...
monserveur.willou.com downloading https://packages.chef.io/stable/debian/8/chef_12.9.38-1_amd64.deb
monserveur.willou.com   to file /tmp/install.sh.11842/chef_12.9.38-1_amd64.deb
monserveur.willou.com trying wget...
monserveur.willou.com Comparing checksum with sha256sum...
monserveur.willou.com Installing chef 12
monserveur.willou.com installing with dpkg...
monserveur.willou.com Selecting previously unselected package chef.
(Reading database ... 169259 files and directories currently installed.)
monserveur.willou.com Preparing to unpack .../chef_12.9.38-1_amd64.deb ...
monserveur.willou.com Unpacking chef (12.9.38-1) ...
monserveur.willou.com Setting up chef (12.9.38-1) ...
monserveur.willou.com Thank you for installing Chef!
monserveur.willou.com Starting the first Chef Client run...
monserveur.willou.com Starting Chef Client, version 12.9.38
monserveur.willou.com Creating a new client identity for scw using the validator key.
monserveur.willou.com resolving cookbooks for run list: []
monserveur.willou.com Synchronizing Cookbooks:
monserveur.willou.com Installing Cookbook Gems:
monserveur.willou.com Compiling Cookbooks...
monserveur.willou.com [2016-04-24T11:06:26+00:00] WARN: Node scw has an empty run list.
monserveur.willou.com Converging 0 resources
monserveur.willou.com
monserveur.willou.com Running handlers:
monserveur.willou.com Running handlers complete
monserveur.willou.com Chef Client finished, 0/0 resources updated in 04 seconds
```

Ensuite vérifiez que le Node est bien enregitré, soit en utilisant le manager, dans l’onglet “Nodes”, mais aussi avec Knife :

```
$ knife node list
monserveur.domaine.ext
```

## Affecter un Role à un serveur
Tout d’abord, il faut créer un Role, encore une fois avec l’outil Knife :

```
$ knife role create roletest
```

Votre éditeur va s’ouvrir et un document au format json vous sera proposé :

```
{
  "name": "roletest",
  "description": "",
  "json_class": "Chef::Role",
  "default_attributes": {},
  "override_attributes": {},
  "chef_type": "role",
  "run_list": [],
  "env_run_lists": {}
}
```

Comme il s’agit d’un premier essai, enregistrez simplement le document et quittez l'éditeur. Vous aurez un retour comme ceci : ```Created role[roletest]```

Le Role est automatiquement synchronisé entre votre poste de travail et le Chef-server. Il faut ensuite l’affecter à votre Node.

Lançez la commande suivante :

```
$ knife node edit monserveur.domaine.ext
```

Ceci ouvrira votre éditeur texte avec un json comme ceci :

```
{
  "name": "monserveur.domaine.ext",
  "chef_environment": "_default",
  "normal": {
    "tags": []
  },
  "policy_name": null,
  "policy_group": null,
  "run_list": []
}
```

Ajoutez votre nouveau Role dans la section “run_list” sous la forme :

```
"role[roletest]"
```

Votre fichier ressemblera à ceci :

```
{
  "name": "monserveur",
  "chef_environment": "_default",
  "normal": {
    "tags": []
  },
  "policy_name": null,
  "policy_group": null,
  "run_list": [
	 "role[roletest]"
  ]
}
```

Enregistrez le fichier et fermez votre éditeur, vous aurez le retour suivant :
```
Saving updated run_list on node monserveur
```

## Télecharger un Cookbook
Un Cookbook est, comme son nom l’indique, un livre de recettes qui permet de configurer un élément du serveur, par exemple l’installation ou la configuration d’un logiciel ou du système.

Bien qu’il soit possible d’affecter directement un Cookbook à un Node, je vous conseille de toujours passer par des Roles, la configuration de Nodes identique est grandement simplifié avec cette méthode, puisqu’il vous suffit d’affecter le même Role à plusieurs Nodes. Enfin il est possible d’affecter plusieurs Roles à un Node.

Nous allons récupérer le Cookbook ```ntp``` sur [supermarket.chef.io](supermarket.chef.io){:target="_blank_"} qui permet d’installer un client ntp sur le serveur et de le configurer.

Récupérons le Cookbook :

```
$ knife cookbook site install ntp
```

Vous allez récupérer le Cookbook ntp ainsi que ses dépendances :

```
Installing ntp to D:/Chef/chef-repo/cookbooks
Checking out the master branch.
Creating pristine copy branch chef-vendor-ntp
Downloading ntp from Supermarket at version 1.11.0 to D:/Chef/chef-repo/cookbooks/ntp.tar.gz
Cookbook saved: D:/Chef/chef-repo/cookbooks/ntp.tar.gz
Removing pre-existing version.
Uncompressing ntp version 1.11.0.
Removing downloaded tarball
No changes made to ntp
Checking out the master branch.
Installing windows to D:/Chef/chef-repo/cookbooks
Checking out the master branch.
Creating pristine copy branch chef-vendor-windows
Downloading windows from Supermarket at version 1.39.2 to D:/Chef/chef-repo/cookbooks/windows.tar.gz
Cookbook saved: D:/Chef/chef-repo/cookbooks/windows.tar.gz
Removing pre-existing version.
Uncompressing windows version 1.39.2.
Removing downloaded tarball
No changes made to windows
Checking out the master branch.
Installing chef_handler to D:/Chef/chef-repo/cookbooks
Checking out the master branch.
Creating pristine copy branch chef-vendor-chef_handler
Downloading chef_handler from Supermarket at version 1.3.0 to D:/Chef/chef-repo/cookbooks/chef_handler.tar.gz
Cookbook saved: D:/Chef/chef-repo/cookbooks/chef_handler.tar.gz
Removing pre-existing version.
Uncompressing chef_handler version 1.3.0.
Removing downloaded tarball
No changes made to chef_handler
Checking out the master branch.
```

## Transférer un Cookbook
Maintenant que le Cookbook est sur votre poste de travail, il faut l’envoyer sur le serveur, pour ce faire, il faut utiliser la commande suivante :

```
$ knife cookbook upload ntp --include-dependencies
```

la commande vas retourner ceci :

```
Uploading ntp          [1.11.0]
Uploading windows      [1.39.2]
Uploading chef_handler [1.3.0]
Uploaded 3 cookbooks.
```

## Affecter un Cookbook à un Role
Il vous faut maintenant affecter une recette incluse dans un Cookbook à notre Role de test.

Lancez la commande suivante :

```
$ knife role edit roletest
```

Ajoutez dans run_list la Recipe ntp :

```
"run_list": [
  "recipe[ntp]"
],
```

Enregistrez le fichier et fermez votre éditeur.

## Tester

Maintenant, connectez-vous sur votre premier Node et lancez la commande :

```
$ chef-client
```
Vous obtiendrez le résultat suivant :

```
Starting Chef Client, version 12.9.38
resolving cookbooks for run list: ["ntp"]
Synchronizing Cookbooks:
  - ntp (1.11.0)
  - windows (1.39.2)
  - chef_handler (1.3.0)
[snip]
Running handlers:
Running handlers complete
Chef Client finished, 3/10 resources updated in 09 seconds
```

Vous avez maintenant ntp d’installé et de configuré sur votre serveur.

Si vous voulez tester avec un autre node, voici la commande “magique” à lancer depuis votre poste de travail

```
$ knife bootstrap monautreserveur.domaine.ext -r 'role[roletest]'
```

Vous pouvez maintenant utiliser cette commande pour chacun de vos serveurs.

L’option -r permet de spécifier le ou les Roles de votre serveur.

## Conclusion et perspectives
Pendant ce guide vous avez appris quelques fonctions de base de Knife et avez utilisé Chef pour gérer l’installation et la configuration de ntp sur un ou plusieurs serveurs.

Si vous installez d’autres Cookbooks et que vous créez de nouveaux Roles vous aurez une base suffisante pour gérer une bonne partie des tâches répétitives lors de l’installation d’un nouveau serveur.

Dans un prochain guide, je vous montrerai comment créer vos propres Recipes depuis un Cookbook existant, et par la suite votre propre Cookbook.

En attendant, vous pouvez aller lire la Recipe du Cookbook ntp qui se trouve dans ```chef-repo\cookbooks\ntp\recipes\default.rb```
