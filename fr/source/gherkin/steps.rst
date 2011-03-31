Étapes et définitions des étapes
================================

Les :doc:`features` sont composées d'étapes, aussi appelées Givens (étant donné), Whens (lorsque) et Thens (alors).

Techniquement, Behat ne fait pas de distinction entre ces 3 types d'étapes. En revanche, on ne peut que fortement vous encourager à le faire ! Ces mots ont été soigneusement sélectionnés pour leur signification, et vous devriez connaître ce qu'ils représentent dans le monde BDD.

Robert C. Martin a écrit `un très bon article <http://blog.objectmentor.com/articles/2008/11/27/the-truth-about-bdd>`_ au sujet du concept BDD "Given-When-Then" où il considère ces 3 étapes comme une machine (un automate) à états finis.

Étapes
------

Given (étant donné)
~~~~~~~~~~~~~~~~~~

La but des "givens" est de **placer le système dans un état connu** avant que l'utilisateur (ou un système externe) ne commence à interagir avec (dans l'étape When). Il faut éviter de parler des interactions utilisateur dans cette étape. Si vous êtes familié avec les usecases, cette étape peut être vue comme les "préconditions".

Exemples :

* Créer des enregistrements (instances du modèle) / définir l'état de la base de données.
* It’s ok to call into the layer “inside” the UI layer here (in symfony: talk to the models). (@TODO)
* Connecter un utilisateur (c'est une exception à la recommendation de pas parler d'interaction utilisateur. Les choses qui "ont eu lieu avant" sont ok).

Et pour tous ceux qui utilisent symfony parmi vous, il est recommandé d'utiliser l'étape Given avec une table composée de plusieurs enregistrements de la base de données au lieu d'utiliser des fixtures. De cette manière vous pourrez lire et comprendre le scénario sans avoir à chercher d'informations autre part (dans les fixtures en l'occurence).

When (lorsque)
~~~~~~~~~~~~~

Le but des étapes de type When est de **décrire l'action clé** que l'utilisateur effectue (ou, en utilisant la métaphore de Robert C. Martin, l'état de transition).

Exemples :

* Interagir avec une page web (BehatBunle implément les étapes web, qui trouvent leur place principalement dans les étapes When).
* Interagir avec d'autres éléments de l'interface utilisateur.
* Vous développez une librairie ? Mettez ici un type d'action qui produit un effet observable quelque part.

Then (alors)
~~~~~~~~~~~

Le but des étapes de type When est d'**observer les résultats**. Les observations devraient êtres liées à la valeur ajoutée/bénéfice décrit dans la feature. Les observations devraient aussi porter sur un certain type de sortie, c'est à dire quelque chose qui est produit par le système (rapport, interface utilisateur, message) et pas quelque chose qui est enfoui au fin fond du système (et qui ne présente aucune valeur ajoutée).

Exemples :

* Vérifier que quelque chose relatif aux étapes Given et When est (ou n'est pas) présent dans la sortie
* Vérifier qu'un système externe a reçu le message attendu (est-ce qu'un email avec le contenu attendu a été envoyé ?)

.. note::

    Même s'il peut être tentant dans l'étape When d'uniquement vérifier l'état de la base de données, résistez à la tentation. Vous devriez vérifier uniquement des résultats observables par l'utilisateur (ou un système externe) et les enregistrelent de la bases de données ne le sont généralement pas.

And (et), But (mais)
~~~~~~~~~~~~~~~~~~~

SI vous avez plusieurs Givens, Whens ou Thens vous pouvez écrire :

.. code-block:: gherkin

    Scenario: Multiple Givens
      Given one thing
      Given an other thing
      Given yet an other thing
      When I open my eyes
      Then I see something
      Then I don't see something else

Ou alors vous pouvez le rendre plus lisible en écrivant :

.. code-block:: gherkin

    Scenario: Multiple Givens
      Given one thing
        And an other thing
        And yet an other thing
       When I open my eyes
       Then I see something
        But I don't see something else

Pour Behat les étapes commençant par And et But représentent exactement le même type d'étape que toutes les autres.

Définitions d'étapes
--------------------

Les définitions d'étapes sont décrites dans des fichiers PHP situés dans ``features/steps/*.php``. Voici un exemple simple :

.. code-block:: php

    <?php
    // features/steps/elephants_steps.php

    $steps->Given('/^I have (\d+) elephants in my belly$/', function($world, $count) {
        // Some PHP code here
    });

A step definition is a simple callback tied to custom regex. Step definitions can take 1 or more arguments, identified by groups in the Regexp (and an equal number of arguments to the callback plus 1). "plus 1" is a ``$world`` :doc:`../behat/environment` object argument. :doc:`../behat/environment` is a container object, that gets shared between single scenario/background steps.

Then there are Steps. Steps are declared in your ``features/*.feature`` files. Here is an example:

.. code-block:: gherkin

    Given I have 93 elephants in my belly

A step is analogous to a callback invocation. In this example, you’re “calling” the step definition above with one argument – the string “93”. Behat matches the Step against the Step Definition’s Regexp and takes all of the matches from that match and passes them to the callback (after ``$world``, ofcourse).

Step Definitions start with an `adjective <http://www.merriam-webster.com/dictionary/given>`_ or an `adverb <http://www.merriam-webster.com/dictionary/when>`_, and can be expressed in any of Behat’s supported languages. All Step definitions are loaded (and defined) before Behat starts to execute the features plain text.

When Behat executes the plain text, it will for each step look for a registered Step Definition with a matching Regexp. If it finds one it will execute its callback, passing all groups from the Regexp match as arguments to the callback (after ``$world``, ofcourse).

The adjective/adverb has **no** significance when Behat is registering or looking for Step Definitions.

Successful
~~~~~~~~~~

When Behat finds a matching Step Definition it will execute it. If the block in the step definition doesn’t raise an Exception, the step is marked as passed (green). What you return from a Step Definition has no significance what so ever. Only Exceptions or their absence matters.

Undefined
~~~~~~~~~

When Behat can’t find a matching Step Definition the step gets marked as yellow, and all subsequent steps in the scenario are skipped. If your test suite has undefined steps – Behat will still exit with ``0`` code. But if you'll specify ``--strict`` option, then Behat will exit with ``1`` (fail).

Pending
~~~~~~~

When a Step Definition’s callback throws the ``Behat\Behat\Exception\Pending``, the step is marked as yellow (as with undefined ones), reminding you that you have work to do. If your test suite has undefined steps – Behat will still exit with ``0`` code. But if you'll specify ``--strict`` option, then Behat will exit with ``1`` (fail).

Failed
~~~~~~

When a Step Definition’s callback is executed and raises an Exception, the step is marked as red. What you return from a Step Definition has no significance what so ever. Returning null or false will not cause a step definition to fail. If suite has failed steps - Behat will return ``1`` to console.

Skipped
~~~~~~~

Steps that follow undefined, pending or failed steps are never executed (even if there is a matching Step Definition), and are marked cyan.

Ambiguous
~~~~~~~~~

Consider these step definitions:

.. code-block:: php

    <?php
    // features/steps/amb_steps.php

    $steps->Given('/Three (.*) mice/', function($world, $disability) {
    });

    $steps->Given('/Three blind (.*)/', function($world, $animal) {
    });

And a plain text step:

.. code-block:: gherkin

    Given Three blind mice

Behat can’t make a decision about what Step Definition to execute, and wil raise a ``Behat\Behat\Exception\Ambiguous`` exception telling you to fix the ambiguity.

Redundant
~~~~~~~~~

In Behat you’re not allowed to use a regexp more than once in a Step Definition (even across files, even with different code inside the Proc), so the following would cause a ``Behat\Behat\Exception\Redundant`` error:

.. code-block:: php

    <?php
    // features/steps/redundant_steps.php

    $steps->Given('/Three (.*) mice/', function($world, $disability) {
        // some code
    });

    $steps->Given('/Three (.*) mice/', function($world, $disability) {
        // some other code
    });

Step Definition Localization
----------------------------

Sometimes, you might need to provide multiple languages of your step definitions. For example, when you're writing some assertion library with bundled Behat steps. In this case, Behat supports definition translations. To translate your definitions, create ``features/steps/i18n`` folder and place regex translastions there in `XLIFF <http://en.wikipedia.org/wiki/XLIFF>`_ format:

.. code-block:: xml

    <!-- features/steps/i18n/ru.xliff --->
    <xliff version="1.2" xmlns="urn:oasis:names:tc:xliff:document:1.2">
      <file original="global" source-language="en" target-language="ru" datatype="plaintext">
        <header />
        <body>
          <trans-unit id="i-have-entered">
            <source>/^I have entered (\d+) into calculator$/</source>
            <target>/^Я набрал число (\d+) на калькуляторе$/</target>
          </trans-unit>
          <trans-unit id="i-have-clicked-plus">
            <source>/^I have clicked "+"$/</source>
            <target>/^Я нажал "([^"]*)"$/</target>
          </trans-unit>
          <trans-unit id="i-should-see">
            <source>/^I should see (\d+) on the screen$/</source>
            <target>/^Я должен увидеть на экране (\d+)$/</target>
          </trans-unit>
        </body>
      </file>
    </xliff>

This translations will be used to match your localized features. For example, this is russian translation & it will be used in all features, written in ``# language: ru``. Also, Behat's ``--steps`` option accepts additional ``--lang`` option in which you can specify language to see available steps:

.. code-block:: bash

    behat --steps --lang=ru

will print all available steps in russian (if has translations).

Steps Organization
------------------

How do you name step definition files? What to put in each step definition? What not to put in step definitions? Here are some guidelines that will lead to better scenarios.

Grouping
~~~~~~~~

Technically it doesn’t matter how you name your step definition files and what step definitions you put in what file. You *could* have one giant file called allSteps.php and put all your step definitions there. That would be messy.

We recommend creating a ``*_steps.php`` file for each domain concept. For example, a good rule of thumb is to have one file for each major model/database table. In a Curriculum Vitae application we might have:

* features/steps/employee_steps.php
* features/steps/education_steps.php
* features/steps/experience_steps.php
* features/steps/authentication_steps.php

The three first ones would define all the ``Given``, ``When``, ``Then`` step definitions related to creating, reading, updating and deleting the various models. The last one defines step definitions related to logging in and out.

Step state
~~~~~~~~~~

It’s possible to keep object state in ``$world->variables`` inside your step definitions. Be careful about this as it might make your steps more tightly coupled and harder to reuse. There is no absolute rule here – sometimes it’s ok to use ``$world->variables``.
