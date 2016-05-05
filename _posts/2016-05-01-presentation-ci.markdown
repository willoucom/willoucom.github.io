---
layout: post
title:  "Intégration Continue"
date:   2016-05-01 11:15:32 +0000
categories: CI
---

L'intégration Continue (CI pour Continuous Integration) est un modèle d'optimisation de tâches complexes. Dans la plupart des cas, cela consiste à faire exécuter des jeux de tests et des compilations sur un serveur dédié.

## Objectifs
Ce guide a pour objectif de vous donner les bases de vocabulaire et de concepts utilisés en Intégration Continue. Il existe à ce jour une très grande quantité d'outils d'Intégration Continue, il serait illusoire de vouloir tous les citer. Cependant des noms sortent du lot. Je traiterai séparément chaque outil.
Pour bien appréhender ce guide, il est nécessaire de posséder des bases sur Git et de comprendre comment fonctionnent les branches.

## Qu'est ce que la CI
La CI ( intégration continue ) consiste en une suite d'action automatique qui ont pour but d'intégrer des modifications dans un flux de production (appellé Workflow ou Pipeline en fonction des outils).

#### Vocabulaire
Tout d'abord un peu de vocabulaire qui vous servira pour la suite.

**Workflow ou Pipeline** : se dit d'une suite d'étapes (step). Les étapes peuvent être éxécutées de façon séquentielles ou en parallèle, ou un mélange des deux.

**Step** : se dit d'une étape. Chaque étape possède un état (échec ou réussite)

**Worker** : se dit d'un agent éxécutant les différentes étapes

**Artefact** : se dit du produit d'une étape, par exemple le résultat d'une compilation.

**Fixture** : se dit des données permettant de définir un état figé et standard dans un jeu de test.

## Workflow

Si nous devions tracer de façon générique un Workflow, il se présenterait de la façon suivante :

#### Nouvelle fonctionnalité
Le développeur crée une nouvelle fonctionnalité, par exemple un formulaire de changement de mot de passe. Il travaille dans une branche "change_password".

Il va tester son code de son coté, puis il va PUSH son code sur le dépot GIT.

Bien entendu, il aura pris soin de rédiger les tests unitaires et fonctionnels de sa fonctionnalité.

#### Construction
L'action de PUSH a été détectée par l'outil de CI.

L'outil de CI va récupérer le contenu de la branche et va lancer la première étape qui est la construction. Dans ce projet Php, nous utilisons Composer pour gérer les packages. L'outil de CI va donc invoquer composer et construire les Artefacts.

Comme le site utilise une base de données, l'outil de CI va créer une base temporaire et charger les fixtures.

#### Tests
Un jeu de tests unitaires a été rédigé, celui-ci est donc exécuté par l'outil de CI qui va capturer le résultat des tests.

Comme le projet est complexe, une partie des tests seront effectués en parallèle afin de réduire la durée globale des tests.

#### Compte rendu
La construction et les tests sont terminés, un rapport est produit et publié sur un espace prévu à cet effet.

Si des tests ont échoué, des alertes seront envoyées et le résultat sera marqué comme valide ou invalide en fonction de la gravité des erreurs.

#### Intégration
En fonction du compte rendu, l'intégration du code produit sera effectuée dans la branche principale du projet, soit de façon automatique, soit sous la forme de pull/merge request.

## Les outils
Comme indiqué précédemment, il existe beaucoup d'outils. Certains sont spécifiques, d'autres sont plus génériques. Voici un petit éventail des outils les plus utilisés.

#### Jenkins
Jenkins est l'outil de CI le plus connu, le plus utilisé, et probablement le plus ancien.

Il a la réputation d'être extrêmement complexe à prendre en main mais d'être le plus polyvalent de tous.

L'interface est vieillissante et anti-ergonomique, cependant depuis l'arrivée du module workflow, renommé pipeline depuis Jenkins 2.0, l'utilisation a été totalement revue et est maintenant à la portée de tous. Il est bien entendu toujours possible de créer ses Jobs (ancienne méthode) via l'interface, ou bien en utilisant Ant ou Maven.

Je vous conseillerais de bien lire la [documentation sur les pipelines](https://Jenkins.io/solutions/pipeline/){:target="_blank_"}, l'utilisation est relativement simple, la syntaxe est en [groovy](http://groovy-lang.org/){:target="_blank_"} et est très proche du php/java/javascript.


Le principal atout de Jenkins est d'être totalement multi-plateforme, il est codé en java et tourne sous linux/mac/windows quasiment sans problèmes. Si le système d'exploitation sur lequel est installé le worker est capable d'éxécuter une commande, alors Jenkins pourra utiliser cette commande. il est ainsi possible d'utiliser aussi bien des scripts shell que des scripts php pour les différentes étapes de construction.

#### Travis
Travis CI est un outil de CI de plus en plus utilisé par les projets OpenSource sur GitHub, il s'intègre très facilement aux projets existant en reliant le compte github avec le compte Travis.

L'utilisation s'effectue par le bias d'un script Travis nommé .travis.xml et qui sera placé à la racine du projet. Celui-ci décrit un ensemble de tâches, la [documentation](https://docs.travis-ci.com/){:target="_blank_"} est extrêmement bien faite et je vous la recommande

#### GitlabCI
GitlabCI est un projet récent. il est basé et inclus dans le projet GitLab qui est un gestionaire de dépot Git.

L'utilisation de GitlabCI si on utilise Gitlab est totalement transparente, il suffit de configurer des runners dans gitlabci et de déposer un fichier .gitlab-ci.yml à la racine du projet.

La syntaxe est très simple et ressemble beaucoup à celle de Travis CI, la [documentation](http://doc.gitlab.com/ce/ci/quick_start/README.html){:target="_blank_"} est simple et très bien illustrée

## Comment choisir son outil ?
La plupart des outils cités se valent, mais certains ont des pré-requis et des spécificités.

#### Tout terrain
Vous voulez un système tout terrain, prenez Jenkins, il est possible de tout faire avec, la prise en main est un peu complexe au début, mais l'outil est très performant.

Il est capable de se connecter à n'importe quel outil de gestion de sources, est capable de construire un projet dans n'importe quel langage, il peut intégrer docker si besoin, et est en mesure de déployer le code partout.

L'écosystème de modules est vaste et peut couvrir presque tout les usages, si vous ne trouvez pas votre bonheur, il vous est possible d'utiliser un script à la place.

De plus si vous utilisez déjà des scripts de tests ou de déploiement, pas besoin de les réécrire, jenkins saura les lancer pour vous.

#### Projet OpenSource
Vous voulez de la CI pour votre projet sans vous compliquer la vie, alors utilisez Travis CI. Il est simple à mettre en place, est très stable et ne vous coûtera rien. Il vous imposera par contre d'utiliser Github pour stocker vos sources.

En dehors de cette limitation, il est parfait.

#### Projet interne
Vous avez des projets interne, et une machine (ou une VM) à disposition, alors installez Gitlab, qui constitue une solution “clé en main” facile à installer et à déployer. En quelques minutes vous aurez un outil puissant et fiable pour gérer vos sources et qui se chargera des constructions.

La maintenance de l'outil est faible, il demandera de temps en temps à se mettre à jour, ce qui ne vous demandera pas beaucoup de temps.
Et si vous commençez à manquer de ressources pour vos constructions, il est possible d'ajouter très facilement des serveurs additionnels.
