---
layout: post
title:  "Installation de Chef-DK et Knife"
date:   2016-05-11 14:08:44 +0000
categories: Chef
---

Knife est l’outil qui vous permettra de  gérer votre Chef-server ainsi que vos Nodes. Il s’installe et s’utilise sur votre poste de travail.

## Objectifs
L’objectif de ce guide est de configurer un Chef-repo sur votre machine. Vous aurez besoin d’un Chef-server, vous pouvez utiliser un serveur que vous aurez [installé vous-même]({% post_url 2016-05-05-installation-chef %}) ou bien un serveur de démo fourni par Chef à l'adresse :  [https://manage.chef.io/](https://manage.chef.io/){:target="_blank_"}

## Installation de Chef-DK
Chef-DK (Chef Development Kit) se télécharge sur le site de Chef ( [https://downloads.chef.io/chef-dk](https://downloads.chef.io/chef-dk){:target="_blank_"} )

Choisissez la version qui correspond à votre système d’exploitation, son utilisation est très proche quelque soit le système utilisé.

Une fois Chef-dk installé, ouvrez un terminal _( sous Windows, un raccourci “Chef Development Kit” se trouvera dans le menu démarrer )_.

Lancez la commande ```knife -v``` pour vérifier que l’outil est bien installé.

## Création du Chef-repo
Il existe plusieurs façons de créer un Chef-repo.

#### En utilisant Chef-manage
Si vous avez installé Chef-manage sur votre serveur, vous pouvez télécharger un starter kit qui contient tout ce qu’il faut pour démarrer.

Il se trouve sur l’interface web dans la rubrique _Administration > Organisation > Starter Kit_

__Attention, le fait de lancer le téléchargement va réinitialiser votre clé ainsi que celle de votre organisation.__

Vous trouverez à l’intérieur du fichier zip un répertoire ```chef-repo``` qu’il vous faudra extraire à l’emplacement de votre choix.

#### Manuellement
Créez un répertoire ```chef-repo``` à l’emplacement de votre choix.

À l’intérieur de celui-ci, créez un répertoire nommé ```.chef``` (le point est important), et placez à l’intérieur de celui-ci votre clé personnelle ainsi que la clé de votre organisation (nommée validator).

Ainsi qu’un fichier ```knife.rb``` qui contient les informations suivantes :

```
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "wilfried"
client_key               "#{current_dir}/wilfried.pem"
validation_client_name   "monorganisation-validator"
validation_key           "#{current_dir}/monorganisation-validator.pem"
chef_server_url          "https://monserveurchef/organizations/monorganisation"
cookbook_path            ["#{current_dir}/../cookbooks"]
```
Remplacez  :

```
node_name   => votre nom d’utilisateur
client_key  => nom de votre fichier de clé
validation_client_name   => nom de l'organisation suivi de -validator
validation_key => nom du fichier de clé de l'organisation
chef_server_url => Adresse de votre organisation sur votre serveur chef
```

## Pour Windows
Dans les environnements Windows, il n’existe pas d’éditeur par défaut en ligne de commande.
Ajoutez en bas du fichie ```knife.rb``` la ligne suivante :

```
knife[:editor] = "notepad"
```

Vous pouvez bien entendu modifier l’éditeur que vous voulez utiliser. Si il n’est pas dans le path système il faut donner son emplacement exact sous la forme :

```
"C:\\progra~2\\notepa~1\\notepad++.exe"
```

Vous aurez peut être besoin d’installer Git sur votre système, installez Github for Windows [https://desktop.github.com/](https://desktop.github.com/){:target="_blank_"} ) et pensez bien à ajouter Git dans le Path de Windows.

## Git
Pour pouvoir sauvegarder et gérer correctement le Chef-repo, il faut l’initialiser en tant que dépot git. Le plus simple est d’utiliser Git en ligne de commande :

```
$ git init && git add . && git commit -m “first”
```

Créez un fichier ```.gitignore``` et ajoutez ```.chef``` pour ne pas pousser vos clés dans votre dépot.

## Vérification que tout fonctionne
Placez vous dans le répertoire chef-repo depuis votre terminal, et lancez la commande ```knife user list```
Vous devriez obtenir quelque chose comme ceci :

```
$ knife user list
wilfried
```

## Conclusion et perspectives
Votre poste de travail est maintenant configuré, votre Chef-repo est crée et un lien a été effectué entre votre poste de travail et le Chef-server.

J’en profite pour vous rappeler que les clés doivent être conservées avec le plus grand soin, celles-ci permettent d’accéder à votre Chef-server et ne doivent jamais être rendues publique. De même si vous êtes plusieurs à utiliser Chef, vous pouvez très facilement créer des utilisateurs avec leur propre clé, n’hésitez donc pas.

Pour la suite, il reste à Bootstrap son premier Node, lui donner un Role et commencer à utiliser les Cookbooks.
