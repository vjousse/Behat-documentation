Gherkin DSL
===========

Gherkin est le langage que Behat est capable de comprendre. C'est un `language métier, spécifique au domaine (DSL) <http://martinfowler.com/bliki/BusinessReadableDSL.html>`_ qui permet de décrire le comportement du système sans détailler la façon dont ce comportement est implémenté.

Gherkin sert à deux choses : la documentation et les tests automatisés. Une troisième fonctionnalité bonus est aussi présente : Gherkin vous parle quand il affiche du rouge, en vous disant quel code vous devriez écrire.

Le parser de Gherkin utilise le composant Translation de Symfony et les traductions au format xliff, des grammaires existent donc pour un grand nombre de langages (42 au moment de la rédaction de cette documentation) afin que votre équipe puisse utiliser des mots clé dans sa propre langue.

Pour vérifier si Behat & Gherkin supportent votre langue, par exemple le français, lancez :

.. code-block:: bash

    behat --usage --lang=fr

Il existe quelques conventions.

* Un seul fichier source Gherkin contient la description d'une seule feature.
* Les fichiers source ou une extension en ``.feature``

La syntaxe de Gherkin
---------------------

Comme Python et YAML, Gherkin est un language "orienté ligne" qui utilise l'indentation pour définir la structure. Les fins de ligne déterminent les fin d'instructions (fin d'étape par exemple). La plus part des lignes commencent par un mot clé.

Le parser sépare l'entrée en features, scénarios et étapes. Lorsque qu'une feature est exécutée, la partie après le mot clé de chaque étape est passée à un callback PHP.

Un fichier source Gherkin ressemble généralement à ceci:

.. code-block:: gherkin

    Feature: Some terse yet descriptive text of what is desired
      In order to realize a named business value
      As an explicit system actor
      I want to gain some beneficial outcome which furthers the goal
    
      Scenario: Some determinable business situation
        Given some precondition
          And some other precondition
         When some action by the actor
          And some other action
          And yet another action
         Then some testable outcome is achieved
          And something else we can check happens too
    
      Scenario: A different situation
          ...

La première ligne permet de débuter la feature. Les lignes 2-4 sont du texte non parsé qui est censé décrire la valeur ajoutée (métier) de cette feature. La ligne 6 débute le scénario. Les lignes 7-13 sont les étapes du scénario. La ligne 15 débute le prochain scénario et ainsi de suite.
