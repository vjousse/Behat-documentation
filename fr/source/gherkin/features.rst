Features
========

Par convention, chaque fichier ``.feature`` contient une seule feature. Une ligne commençant par le mot clé ``Feature:`` suivie d'une identation quelconque marque le début d'une feature. Une feature contient habituellement une liste de scénarios. Vous pouvez écrire ce que vous voulez jusqu'au premier scénario qui commence par le mot clé ``Scenario:`` (ou son équivalent traduit) sur une nouvelle ligne. Vous pouvez utiliser :doc:`tags` afin de regrouper des features et des scénarios ensemble, indépendamment de votre fichier et de votre structure de répertoire.

Chaque scénario consiste en une liste d'étapes, qui doivent commencer par un des mots clé ``Given``, ``When``, ``Then``, ``But`` ou ``And`` (ou leurs équivalents traduits). Behat les traite de la même manière, mais vous, vous ne devriez pas. Voici un exemple :

.. code-block:: gherkin

    Feature: Serve coffee
      In order to earn money
      Customers should be able to 
      buy coffee at all times

      Scenario: Buy last coffee
        Given there are 1 coffees left in the machine
        And I have deposited 1$
        When I press the coffee button
        Then I should be served a coffee

En plus de :doc:`scenarios`, une feature peut contenir des :doc:`backgrounds` & :doc:`outlines`.

Définition des étapes
---------------------

Pour chaque étape, Behat va chercher une **définition d'étape** correspondante. Une définition d'étape s'écrit en PHP. Chaque définition d'étape est un callable PHP, couplé à une expression régulière spécifique. Ci-dessous un exemple :

.. code-block:: php

    <?php
    // features/steps/coffee_steps.php

    $steps->Then('/^I should be served coffee$/', function($world) {
        assertEquals('coffee', $world->dispensedDrink);
    });

Les définitions d'étapes peuvent aussi prendre des paramètres :

.. code-block:: php

    <?php
    // features/step/coffeeSteps.php

    $steps->Given('/^there are (\d+) coffees left in the machine$/', function($world, $number) {
        $world->machine = new Machine((int) $number);
    });

Cette définition d'étape utilise une expression régulière avec un groupe de correspondance : ``(\d+)`` (il correspond à n'importe quelle suite de nombres). La valeur de chaque groupe de correspondance est envoyé comme chaîne de caractères à la fonction de callback. Vous devez faire attention d'avoir le même nombre de groupe de correspondance que d'arguments de la fonction de callback. Puisque les arguments de la fonction de callback sont toujours des chaînes de caractères, vous devez réaliser les conversions nécessaires dans la fonction de callback, ou utiliser :doc:`../behat/transformations`.

À noter que le premier argument de la fonction de callback est toujours ``$world``. Il est partagé entre chaque objet (ou container) :doc:`../behat/environment` des définitions d'étapes.

Lorsque Behat affiche les résultats de l'exécution des features il soulignera chaque argument des étapes de manières à visualiser plus facilement quelle partie de l'étape a été reconnue comme argument. Il affichera aussi le chemin et la ligne de la définition d'étape correspondante. Cela permet d'aller d'un fichier de feature à n'importe quelle définition d'étapes facilement.

Lancer les features
-------------------

Il existe plusieurs moyens de lancer vos features. Cette page liste les plus communes. N'importe laquelle de ces techniques vous permet aussi de définir des options de lignes de commande communes dans un fichier ``behat.yml``.

Utiliser la commande CLI (Command Line Interface)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

En partant du fait que vous avez installé Behat via un package PEAR, lancez ceci dans un invite de commandes pour voir les options possibles pour lancer des features :

.. code-block:: bash

    behat -h

Par exemple :

.. code-block: bash

    behat features/authenticate_user.feature:44 --format html > features.html

va lancer le scénario défini à la ligne 44 de la feature authenticate_user, le format utilisé sera HTML et le tout est redirigé vers un fichier features.html pour être visualisé dans un navigateur.

.. code-block: bash

    behat features --name "Failed login"

va lancer le(s) scénario(s) nommés "Failed login"

Vous pouvez aussi utiliser :doc:`tags` pour spécifier quoi lancer.
