============================
Liste versus type spécifique
============================

:date: 2025-01-26 15:00
:slug: refactoring-list-vs-specific-type
:authors: Florian Strzelecki
:summary: Où comment je me simplifie la vie avec un type spécifique.
:category: dev
:tags: python, software architecture, refactoring

..

    Pourquoi faire simple lorsqu'il est possible de se compliquer la vie ?

C'est la question qui m'est venue à l'esprit en lisant ce bout de code (abrégé
pour se concentrer sur l'essentiel) :

.. code-block:: python

    def _get_data_from_db() -> list[dict[str, int]]:
        """Récupère le champ ``key`` d'une source de données."""
        # ...

    def do_something() -> None:
        # ...
        data = _get_data_from_db()

        value = 0
        if data:
            value = data[0]['key']

        # ...

Ce que font exactement ces fonctions n'a aucune importance, ce qui compte c'est
le type de retour de la fonction ``_get_data_from_db``, à savoir une liste de
dictionnaire d'une part, et son usage d'autre part. Ce dernier est indiqué par
sa `docstring`__, il est d'obtenir un champ à partir d'une source de données
(par exemple un fichier CSV ou une table de base de données SQL).

.. __: https://en.wikipedia.org/wiki/Docstring

Alors pourquoi retourne-t-elle une liste de dictionnaire ? En lisant le code
de ``do_something``, je me suis demandé si :

* que se passe-t-il s'il y a plus d'un (1) élément dans la liste ?
* pourquoi le champ ``key`` ?
* est-ce qu'il existe d'autre usage de cette fonction ?

Ce sont autant de questions que je me pose, et qui font perdre du temps à la
relecture du code. Ce sont des questions qui mettent le doute sur l'usage de
la fonction, et peut-être que tout cela cache un bug ?

Après vérification, j'ai pu déterminer que cette fonction ne servait réellement
qu'à cela : obtenir une seule et unique valeur, si elle existe. La fonction est
implémentée de telle façon qu'elle ne peut retourner qu'un seul élément au
mieux, éliminant les cas où il y en a plus d'un.

Il est temps de se simplifier la vie, en transformant cette fonction avec
cette signature :

.. code-block:: python

    def _get_data_from_db(default: int = 0) -> int:
        ...

Il n'est même pas nécessaire de changer la docstring, la fonction fait
exactement la même chose qu'avant en retournant un scalaire plutôt qu'une
liste de dictionnaire. Tous les appels à cette fonction se retrouvent donc
simplifiés de cette façon :

.. code-block:: python

    value = _get_data_from_db()

Il n'y a plus ni de ``if data`` nécessaire, ni besoin de ``data[0]['key']``, et
donc moins de risque de se tromper ou d'introduire un bug. Le code est beaucoup
plus *expressif* et son intention est claire à la lecture, même en n'ayant que
la signature de la fonction.

C'est aussi cette expressivité que j'apprécie dans le choix d'un type de
données plutôt qu'un autre, dans sa capacité à réduire les doutes en ne
représentant que les états possibles.

Simplifiez-vous la vie !
