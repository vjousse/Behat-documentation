Rapide introduction à Behat
===========================

Behat est un framework `open source <http://creativecommons.org/licenses/MIT/>`_ de `développement orienté scénario (BDD -- Behaviour Driven Development) <http://en.wikipedia.org/wiki/Behavior_Driven_Development>`_ pour PHP 5.3.

Behat a été inspiré par le projet Ruby `Cucumber <http://cukes.info/>`_ et plus particulièrement par sa syntaxe (Gherkin). Il essaie de se comporter comme Cucumber en ce qui concerne les entrées (les fichiers de Features) et les sorties (les console formatters), mais de l'intérieur, il a été construit dès le départ sur les épaules de géants :

* Le conteneur à injection de dépendances de Symfony (DIC -- Dependency Injection Container)
* Le composant Event Dispatcher de Symfony
* Le composant console de Symfony
* Le composant Finder de Symfony
* Le composant traduction de Symfony

À la différence  de n'importe quel autre framework testant les applications de l'intérieur vers l'extérieur, Behat teste les applications de `l'extérieur vers l'intérieur <http://blog.dannorth.net/whats-in-a-story/>`_. Cela signifie que Behat fonctionne uniquement avec les entrées/sorties de votre application. Si vous souhaitez tester vos modèles - utilisez un framework de tests unitaires à la place. Behat a été créé pour tester les comportements (mais peut être utilisé pour tout +) ).

Installation
------------

Il existe plusieurs manières d'installer Behat sur votre système. Mais tout d'abord, vérifiez le seul et unique pré-requis pour cela : vous devez avoir PHP 5.3.1 ou supérieur d'installé. Si c'est le cas - continuez à lire, si ce n'est pas le cas - installez le d'abord.

La façon la plus simple de l'installer c'est avec PEAR :

.. code-block:: bash

    pear channel-discover pear.behat.org
    pear install behat/gherkin-beta
    pear install behat/behat-beta

Vous pouvez aussi cloner le projet avec Git :

.. code-block:: bash

    git clone git://github.com/Behat/Behat.git && cd Behat
    git submodule update --init

Une fois téléchargé, exécutez ``path/to/Behat/bin/behat`` ou simplement ``behat`` (si vous avez installez Behat via PEAR) à partir de la console.

Les bases
---------

Behat est un outil qui peut exécuter des descriptions fonctionnelles comme des tests automatisés. Le langage que Behat (et Cucumber en Ruby) comprend s'appelle Gherkin. Voici un exemple (en anglais) :

.. code-block:: gherkin

    Feature: Search courses 
      In order to ensure better utilization of courses 
      Potential students should be able to search for courses 

      Scenario: Search by topic 
        Given there are 240 courses which do not have the topic "biology" 
        And there are 2 courses A001, B205 that each have "biology" as one of the topics
        When I search for "biology" 
        Then I should see the following courses:
          | Course code |
          | A001        |
          | B205        |

Bien que Behat puisse être considéré comme un outil "de test", le but de cet outil est de supporter le BDD. Cela signifie que les "features" (des descriptions textuelles avec des scénarios) sont typiquement écrites avant n'importe quoi d'autre et vérifiées par des analystes métier, des experts du domaine, etc. : des personnes non techniques. Le code est ensuite écrit de l'extérieur vers l'intérieur, de manière à ce que les histoires passent.

Un répertoire contenant un environnement basique de test pour Behat ressemble à ceci :

.. code-block:: bash

    |-- features
       `-- steps
       |   `-- math_steps.php
       `-- support
           |-- bootstrap.php
           |-- env.php

Il peut être séparé en trois parties :

1. ``features`` - répertoire racine des tests. Les fichiers de Feature se trouvent ici.
2. ``features/steps`` - répertoire contenant les définitions des étapes (scripts PHP).
3. ``features/support`` - répertoire de support. Les scripts de support sont créés ici (environnement, définition des chemins).

Le cœur des tests dans Behat sont les features. Les features sont placées dans le répertoire ``features`` et sont de simples fichiers textes.

Behat parse les fichiers de feature et essaie de trouver la définition d'étape pour chaque étape provenant du répertoire ``features/steps`` (chemin de définition d'étape).

Chaque définition d'étape est un simple "callable" PHP ayant accès à un objet environnement partagé entre chaque étape (scénario), qui peut être configuré dans ``features/support/env.php``.

Et si la configuration de l'environnement requiert des librairies externes pour fonctionner (PHPUnit par exemple), l'inclusion de ces librairies se fait dans le fichier ``features/support/bootstrap.php``.

Feature
-------

Le fichier de feature est votre point d'entrée dans Behat. C'est par là que vous commencez à travailler sur votre projet. Voici un exemple de contenu basique d'une feature ``features/math.feature`` :

.. code-block:: gherkin

    Feature: Addition 
      In order to avoid silly mistakes 
      As a math idiot 
      I want to be told the sum of two numbers 

      Scenario: Add two numbers 
        Given I have entered 50 into the calculator
          And I have entered 70 into the calculator
         When I press add
         Then The result should be 120 on the screen

Comme vous pouvez le voir, une feature est un simple et lisible fichier texte. Chaque feature est écrite en un `DSL <http://en.wikipedia.org/wiki/Domain-specific_language>`_ nommé **Gherkin**, tout d'abord introduit dans le projet Ruby `Cucumber <http://cukes.info/>`_.

1. par convention, chaque fichier ``*.feature`` contient une seule feature.
2. une ligne commençant par le mot clé ``Feature:`` (ou le mot clé correspondant traduit) suivi par 3 lignes de texte indenté librement constitue le début d'une feature.
3. une feature contient habituellement une liste de scenarios. Vous pouvez écrire ce que vous voulez jusqu'au premier scénario : ce texte constituera la description de la feature.
4. chaque scénario débute avec les mots clé ``Scenario:`` ou ``Scenario Outline:`` (ou leurs équivalents traduits). Chaque scénario est constitué de plusieurs étapes, qui doivent débuter par un des mots clé ``Given``, ``When``, ``Then``, ``But`` or ``And`` (ou leurs équivalent traduits). Behat traite ces étapes de la même manière, mais vous, vous ne devriez pas !

Définition des étapes
---------------------

Pour chaque étape Behat va chercher une définition d'étape correspondante. Une définition d'étape est écrite en PHP. Chaque définition d'étape est constituée d'un mot clé, d'une expression régulière et d'un callback. Ci dessous un exemple de fichier ``features/steps/math.php``

.. code-block:: php

    <?php 

    $steps->Given('/^I have entered (\d+) into the calculator$/', function($world, $arg1) { 
        throw new Behat\Behat\Exception\Pending('Write code later'); 
    });

1. ``$steps`` est objet global de type DefinitionDispatcher, disponible dans tous les fichiers de définition d'étapes. Appeler ``->Given`` dessus va permettre de définir une nouvelle étape ``Given`` (ceci est aussi valable pour les étapes et mots clé ``When``/``Then``/``And``).
2. ``'/^I have entered (\d+) into the calculator$/'`` - l'expression régulière correspondant à l'étape. Touts les patrons de recherche (``(\d+)``) sont transformés en arguments de la fonction de callback (``$arg1``).
3. Le premier argument de la fonction de callback (``$world``) est toujours réservé pour un objet environnement. Un objet environnement est créé avant chaque exécution d'un scénario et est partagé entre chaque étape du scénario.
4. Le corps de la définition de l'étape est du simple code PHP. Une étape **échouée** est une étape dont l'exécution engendre une exception. Si l'exécution d'une étape n'engendre pas d'exception, l'étape **passe**.

Environnement
-------------

Behat crée un objet d'environnement pour chaque scénario et passe une référence à ce dernier dans chaque définition d'étape.

Donc, si vous souhaitez calculer/accumuler ou juste partager des variables entre les définitions d'étapes, utilisez ``$world`` pour cela.

Mais comment faire si souhaitez que des définitions soient connectées dans chaque "world" ? Utilisez le configurateur  d'environnement pour cela :

.. code-block:: php

    <?php
    // features/support/env.php

    require 'paths.php'; 

    // Create WebClient behavior 
    $world->client = new \Goutte\Client; 
    $world->response = null; 
    $world->form = array(); 

    // Helpful closures 
    $world->visit = function($link) use($world) { 
        $world->response = $world->client->request('GET', $link); 
    };

Ce fichier sera exécuté sur chaque création d'objet environnement. La variable ``$world`` est elle même un objet environnement, qui fonctionne comme un emplacement de variable pour toutes les valeurs et les paramètres de vos scénarios.

Mais comment faire si vous avez besoin d'utiliser des librairies tierces dans ``env.php``? C'est inefficace de les inclure avant chaque scénario, c'est pourquoi Behat supporte le "bootstrapping scripting" :

.. code-block:: php

    <?php
    // features/support/bootstrap.php

    require_once 'PHPUnit/Autoload.php';
    require_once 'PHPUnit/Framework/Assert/Functions.php';

Ce fichier sera evalué par Behat avant même que les tests de feature soient lancés ;-)

CLI
---

Behat est fournit avec un puissant outil en ligne de commande, appelé ... behat.

Pour voir la version current de Behat, exécutez :

.. code-block:: bash

    behat -V

Pour voir les autres commandes disponibles, exécutez :

.. code-block:: bash

    behat -h

Maintenant vous savez tout ce que vous avez besoin de savoir sur Behat. Vous pouvez commencer à utiliser BDD dans vos projets dès maintenant ou continuer à lire la documentation complète.
