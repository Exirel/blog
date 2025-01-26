======================
Liste en compréhension
======================

:date: 2022-02-24 17:40
:slug: list-comprehension
:authors: Florian Strzelecki
:summary: Les "list-comprehensions" en Python
:category: dev
:tags: python

.. figure:: https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Sugar_2xmacro.jpg/1280px-Sugar_2xmacro.jpg
   :alt: Une photo de grains de sucrose rafinée.

   **Macro photograph of a pile of sugar (saccharose)**, par Lauri Andler
   (`source`__)

   .. __: https://commons.wikimedia.org/wiki/File:Sugar_2xmacro.jpg

.. contents:: Sommaire

Une liste est une suite d'éléments, avec un premier, un dernier, et une taille,
et il est possible d'itérer dessus pour obtenir chacun des éléments, ou bien
d'utiliser l'index d'un élément pour obtenir ledit élément. Une liste peut se
définir et s'utiliser de cette façon :

.. code-block:: pycon

    >>> l = [1, 2, 3, 4]
    >>> l[0]
    1
    >>> l[-1]
    4
    >>> l[1:3]
    [2, 3]
    >>> l[::2]
    [1, 3]
    >>> l[::-1]
    [4, 3, 2, 1]

Et il est possible d'itérer dessus de cette façon :[#]_

.. code-block:: pycon

    >>> for i in l:
    ...     print(i)
    ...
    1
    2
    3
    4

C'est une approche assez directe et qui va droit au but : pour chaque
(``for-in``) élément ``i`` de cette liste ``l``, je l'affiche dans le terminal
(via ``print(i)``). Et si je veux créer une nouvelle liste à partir de la
première ? Ce sera presque la même chose :

.. code-block:: pycon

    >>> m = []
    >>> for i in l:
    ...     if i % 2 == 0:
    ...         m.append(i**2)
    ...
    >>> m
    [4, 9]

Dans la plupart des langages que je connais, l'histoire s'arrêterait là, et
bien entendu je n'en viendrais pas à écrire un article dessus. Alors, parlons
plutôt des `listes en compréhension`__, et de la magie qui va avec.

.. __: https://fr.wikipedia.org/wiki/Liste_en_compr%C3%A9hension

Syntaxe
=======

Parlons de la syntaxe en utilisant une liste en compréhension (ou "list
comprehension" en anglais) pour obtenir le même résultat que l'exemple
précédent :

.. code-block:: pycon

    >>> m = [i**2 for i in l if i % 2 == 0]
    >>> m
    [4, 16]

Sa structure est composée de trois parties :

1. **l'élément** : ce qui est désiré pour chaque élément du résultat (ici
   ``i**2``)
2. **l'itération** : le contenu qui sert de source au résultat (ici
   ``for i in l``)
3. **la condition** : et enfin comment la source est filtrée pour n'autoriser
   que certains éléments (ici ``if i % 2 == 0``)

Ces trois parties sont peut-être plus faciles à voir en formatant le code de
cette façon :

.. code-block:: python

    [
        i**2  # élément
        for i in l  # itération
        if i % 2 == 0  # condition
    ]

À noter qu'une telle syntaxe retourne une liste : l'expression est évaluée
immédiatement et retourne un résultat après avoir exécuter la boucle entière.
Cela veut dire aussi que le résultat de cette expression va prendre du temps
d'exécution, et le résultat (la variable) va prendre de la place en mémoire :

.. code-block:: pycon

    >>> type(m)
    <class 'list'>

Comme le résultat est une liste, toutes les opérations sur les listes
fonctionnent directement dessus (trier, filtrer, itérer, etc.). L'avantage,
outre les opérations habituelles comme ``len(m)`` pour obtenir la taille de la
liste, c'est qu'il est possible de la parcourir plusieurs fois. Bref, c'est une
instance de ``list`` tout ce qu'il y a de plus classique.

Cependant si, et seulement si, il n'est pas nécessaire de parcourir cette liste
plus d'une fois alors il est possible de ne pas avoir à stocker en mémoire plus
que le strict nécessaire. Pour cela, il faut se pencher sur les générateurs, et
les expressions de générateur.

Expression de générateur
========================

La `PEP 289`__ prend pour exemple la somme d'une liste d'entiers pour expliquer
l'intérêt des générateurs et de ce type d'expression. Tout d'abord avec une
liste il est possible d'utiliser la fonction ``sum``, directement ou bien avec
notre liste en compréhension :

.. code-block:: pycon

    >>> sum([1, 2, 3, 4])
    10
    >>> sum([i**2 for i in l if i % 2 == 0])
    20

Comme indiqué précédemment la liste intermédiaire va être exécutée
immédiatement et prendre de la place en mémoire, et ce, avant d'être transmise
à la fonction ``sum`` qui va devoir parcourir à nouveau cette liste pour
effectuer son opération : c'est deux fois plus de travail que nécessaire. [#]_

Dans cet exemple, la liste est très courte et ne pose donc pas de problèmes.
Ce qu'il faut imaginer, ce sont les conséquences sur une liste de très grande
taille, par exemple une liste de nombres provenant d'un énorme fichier CSV.

Dans ce genre de cas, l'effet sur les performances peut être préjudiciable,
soit parce que le programme est trop lent, soit parce qu'il consomme trop de
mémoire (c'est d'autant plus important sur un système aux ressources limitées).

C'est là qu'entre en jeu une expression qui va produire un **générateur**,
c'est à dire un objet itérateur qui génère un nouvel élément à chaque étape du
parcours, jusqu'à atteindre la fin. La syntaxe est identique à la liste en
compréhension à ceci près qu'au lieux des crochets ``[ ... ]`` ce sont des
parenthèses ``( ... )`` qui sont utilisées :

.. code-block:: pycon

    >>> g = (i**2 for i in l if i % 2 == 0)
    >>> type(g)
    <class 'generator'>
    >>> sum(g)
    20

À noter qu'il est possible de se passer des parenthèses lorsque l'expression
est appelée dans un contexte qui le permet (par exemple, lors de l'appel d'une
fonction) :

.. code-block:: pycon

    >>> sum(i**2 for i in l if i % 2 == 0)
    20

Nous retrouvons ici les mêmes trois éléments :

* l'élément à obtenir,
* la boucle ``for-in``,
* et enfin la condition.

Et avec un format différent pour la lisibilité :

.. code-block:: python

    sum(
        i**2  # élément
        for i in l  # itération
        if i % 2 == 0  # condition
    )

La différence ici est que l'expression n'est pas exécutée au moment de sa
définition, pour cela il faut attendre que le code exécute une boucle sur notre
générateur pour qu'il s'exécute, élément après élément.

.. __: https://www.python.org/dev/peps/pep-0289/

Fonctionnement du générateur
============================

Pour mieux comprendre le fonctionnement de cette expression, voici une fonction
qui se contentera d'afficher un élément puis de le retourner, et que j'utilise
ensuite dans mon générateur :

.. code-block:: pycon

    >>> def debug(i):
    ...     print('Debug: %s' % i)
    ...     return i
    ... 
    >>> g = (debug(i) for i in l)

Maintenant, je peux parcourir manuellement le générateur grâce à la fonction
built-in ``next()`` :[#]_

.. code-block:: pycon

    >>> next(g)
    Debug: 1
    1
    >>> next(g)
    Debug: 2
    2
    >>> next(g)
    Debug: 3
    3
    >>> next(g)
    Debug: 4
    4
    >>> next(g)
    Traceback (most recent call last):
    File "<stdin>", line 1, in <module>
    StopIteration

Deux remarques :

1. Lorsque le générateur arrive au bout de la liste, il émet une exception
   ``StopIteration``, ce qui permet à une boucle ``for-in`` de s'arrêter
   naturellement.
2. La fonction ``debug`` n'est appelée que sur un seul élément à la fois, une
   fois par appel de ``next()``.

Cela permet de comprendre que l'expression n'est pas exécutée tant que le
générateur n'est pas appelé. Les conséquences ?

    **La liste n'existe pas !**

Chaque élément est généré à la demande uniquement, et il n'est pas stocké par
le générateur. C'est pour cela qu'il est très intéressant de fournir un
générateur à la fonction ``sum()``, puisque cette dernière n'a pas besoin que
la liste "existe" : elle parcourt l'itérable en ajoutant chaque élément à son
résultat, ne stockant ainsi que ce dernier en mémoire.

Cela en fait donc un outil très pratique lorsqu'il s'agit de traiter un élément
à la fois sans encombrer la mémoire, en donnant beaucoup plus de contrôle au
code exploitant ses capacités.

Attention à quelques *détails* importants :

* Un générateur n'est **pas** une liste, il ne possède pas toutes les
  propriétés ni les méthodes d'une liste (il ne permet pas de connaître sa
  taille à l'avance).
* Il n'est pas possible d'itérer plusieurs fois sur un générateur, ce n'est pas
  son but.

Une expression de générateur n'est que du sucre syntaxique pour définir
rapidement un générateur. Pour bien comprendre les limites de cette expression,
il faut comprendre ce qu'est un générateur, et ses limites.

Limitations et potentiels
=========================

Une seule fois
--------------

Sans m'attarder trop en détails sur ce que sont les générateurs [#]_ il
m'apparaît important de préciser quelques unes de leurs limitations. Tout
d'abord un exemple :

.. code-block:: pycon

    >>> g = (i for i in range(10))
    >>> list(g)
    [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
    >>> list(g)
    []

Je tranforme ici un générateur en objet ``list`` une première fois, avec succès
puisque j'obtiens l'intégralité des éléments. Lors de la seconde
transformation, le résultat est une liste vide, car le générateur est arrivé au
bout de son traitement. Il a été entièrement **consommé** et ne peut aller plus
loin. Le résultat est donc une liste vide.

À chaque fois qu'une opération demande de parcourir le générateur, elle
provoque son exécution. Cette exécution s'arrête si l'une de ces conditions est
remplie :

* Le générateur arrive au bout de son traitement, il n'y a donc plus rien à
  exécuter (le générateur lèvera une exception ``StopIteration``).
* Le parcours est arrêté manuellement, ce qui cesse mécaniquement d'exécuter le
  générateur.

En quelque sorte, le parcours **consomme** le générateur, et une fois consommé
il n'y a donc plus rien. Cela peut être une source d'incompréhension et de
bugs. Commençons par une liste :[#]_

.. code-block:: pycon

    >>> l = list(range(10))
    >>> l
    [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
    >>> 2 in l
    True
    >>> 2 in l
    True

Lorsqu'un élément est présent dans une liste, en vérifier la présence ne change
rien. C'est l'avantage d'avoir la liste en mémoire, permettant de vérifier
la présence de données à tout instant. La liste existe en entier, et elle est
capable de fournir des informations sur son contenu sans être altérée par cette
opération.

Maintenant, voici ce qui se passe lorsque j'utilise un générateur à la place
d'une liste :

.. code-block:: pycon

    >>> g = (i for i in range(10))
    >>> 2 in g
    True
    >>> 2 in g
    False

Lors du premier ``2 in g``, le générateur est consommé jusqu'à ce que
l'opérateur ``in`` en arrête le parcours en trouvant l'élément recherché. Au
second ``2 in g``, le générateur a déjà consommé les premiers éléments, et
l'opérateur ``in`` continue donc de parcourir le générateur jusqu'à le
consommer entièrement, sans jamais trouver le bon élément : il retourne donc
``False``, et le générateur ne contient plus rien. En essayant de transformer
le générateur en liste, cette dernière est vide :

.. code-block:: pycon

    >>> list(g)
    []

Il faut donc faire très attention lorsque vous manipulez un générateur, tant sa
fonction est précise et son comportement particulier. Lorsque vous utilisez un
itérable dans vos fonctions, assurez vous de bien réfléchir au comportement
dont vous dépendez : s'il faut parcourir plusieurs fois, ou bien connaître la
taille de l'itérable, ou encore s'il faut accéder directement à un élément par
son index, alors ce n'est **pas** un générateur qu'il vous faut. C'est
peut-être l'occasion de considérer `une séquence`__ ou un autre type de données
ayant les bonnes propriétés pour votre usage.

.. __: https://docs.python.org/3/library/stdtypes.html#sequence-types-list-tuple-range

Étape intermédiaire
-------------------

Un avantage que je trouve au générateur est sa capacité à exprimer un
traitement séquentiel tout en permettant un contrôl précis quant à l'éxécution
des opérations de génération.

Si personne ne parcourt un générateur, il ne consomme presque pas de ressources
(mémoire comme CPU). Cela en fait un candidat idéal pour générer des résultats
intermédiaires, qu'il n'est pas nécessaire de parcourir plus d'une fois :

.. code-block:: pycon

    >>> raw_source = [
    ...     "arbre",
    ...     "banane",
    ...     None,
    ...     "chaise",
    ...     "",
    ...     "pizza",
    ... ]
    >>> 
    >>> words = (word for word in raw_source if word)
    >>> filtered_words = (word for word in words if len(word) > 5)
    >>> ", ".join(filtered_words)
    'banane, chaise'

Il y a deux générateurs :

* Le premier n'accepte que les éléments non vide (ni ``None`` ni une chaîne
  vide).
* Le second utilise cette liste pour filtrer sur les mots qui font plus de
  5 caractères. [#]_

La méthode ``str.join`` permet de joindre tous les mots de la liste par une
virgule, donnant le résultat final ``'banane, chaise'``. La liste d'origine
``raw_source`` n'a été parcourue qu'une seule et unique fois, bien qu'au
travers de plusieurs générateurs.

Une question de taille
----------------------

Imaginez un cas peut-être plus concret, avec un long fichier CSV de plusieurs
millions de lignes : stocker l'intégralité du fichier en mémoire n'est
peut-être pas possible, ou bien consomme trop de ressources. Dans ce cas, le
générateur permet d'effectuer une suite de traitements sur chaque ligne, et ce,
**sans jamais stocker l'intégralité du fichier en mémoire** :

.. code-block:: python

    import csv

    # open input & output CSV files
    with (
        open('input.csv', newline='') as in_csv,
        open('output.csv', 'w', newline='') as out_csv,
    ):
        # get a reader and a writer on the CSV files
        reader = csv.reader(in_csv)
        writer = csv.writer(out_csv)
        # parse each line from the reader
        temporaries = (
            parse(line)
            for line in reader
            if line  # exclude empty lines
        )
        # apply a condition on the parsed lines
        temporary = (
            transform(line)
            for line in temporaries
            if apply_condition_on(line)
        )
        # write each lines
        writer.writerows(temporary)

Ce bloc de code mélange plusieurs choses : ouvrir des fichiers CSV pour la
lecture et l'écriture (les objets ``reader`` et ``writer``), appliquer
plusieurs traitements (via des générateurs), puis sauvegarder le résultat
(via la méthode ``writer.writerows()``).

Le ``reader`` est un itérateur sur les lignes du fichier, qui permet de
parcourir ce dernier ligne par ligne. J'utilise alors un premier générateur
pour parcourir ce fichier et appliquer la fonction ``parse()`` sur chaque
ligne qui ne soit pas vide (``if line``). Aucune opération n'est exécutée par
le code à ce moment là, puisqu'il s'agit d'un générateur.

Ensuite, j'applique la fonction ``transform()`` à chaque ligne valide d'après
la fonction ``apply_condition_on()``. Là encore, aucune exécution de code,
puisqu'il s'agit à nouveau d'un générateur.

En fin de course, j'appelle la méthode ``writer.writerows()`` sur mon dernier
générateur, provoquant son exécution : chaque ligne lue va être traitée puis
écrite dans le fichier de sortie, et ce, sans jamais la conserver en mémoire
une fois écrite. De fait, il n'y a jamais plus d'une ligne stockée en mémoire
lors du parcours du fichier d'entrée et de son traitement.

Imaginons alors que ce fichier pèse plusieurs Go, le traitement sera peut-être
long, il ne consommera cependant pas plus que la mémoire requise pour stocker
une seule ligne de ce fichier !

Sucre syntaxique
----------------

Comme tout ceci n'est que du sucre syntaxique sur la boucle ``for-in``, il est
tout à fait possible d'écrire la même chose sans générateur :

.. code-block:: python

    import csv

    # open input & output CSV files
    with (
        open('input.csv', newline='') as in_csv,
        open('output.csv', 'w', newline='') as out_csv,
    ):
        # get a reader and a writer on the CSV files
        reader = csv.reader(in_csv)
        writer = csv.writer(out_csv)
        # parse each line from the reader
        for line in reader:
            if line:  # exclude empty lines
                temporary = parse(line)
                # apply a condition on the parsed lines
                if apply_condition_on(temporary):
                    temporary = transform(temporary)
                    # write one line
                    writer.writerow(transform(temporary))

Ce n'est donc pas une question du nombre de lignes de code, puisque la version
avec des ``for-in`` prend moins de place.

Je trouve la version avec générateur plus simple à lire, car séquentielle, là
où la version avec une boucle et plusieurs conditions imbriquées est un peu
plus difficile à suivre pour moi. C'est à la fois une question d'habitude, et
une question du nombre d'opérations à garder en tête à chaque itération. [#]_
Cependant, je vous laisse seul juge sur cet aspect là.

Là où l'avantage me semble plus concret, c'est qu'il est plus facile de
modifier le premier code en intervenant au milieu des traitements, avec ou sans
découpage du code, là où ce sera plus complexe avec cette seconde structure.
La seconde structure demande de savoir où placer le nouveau code, au bon niveau
d'imbrication, là où la première structure avec les générateurs permet
d'intercaler des modifications plus aisément.

D'ailleurs, pourquoi se contenter d'en parler, lorsque je peux le montrer avec
du code :

.. code-block:: python

    import csv

    def parse_all(lines):
        """Apply ``parse`` to each valid line in ``lines``."""
        return (
            parse(line)
            for line in lines
            if line
        )

    def transform_all(lines):
        """Apply ``transform`` to each valid line in ``lines``."""
        return (
            transform(line)
            for line in parse_lines(reader)
            if apply_condition_on(line)
        )

    # open input & output CSV files
    with (
        open('input.csv', newline='') as in_csv,
        open('output.csv', 'w', newline='') as out_csv,
    ):
        # get a reader and a writer on the CSV files
        reader = csv.reader(in_csv)
        writer = csv.writer(out_csv)
        # parse and transform
        temporary = parse_all(reader)
        temporary = transform_all(temporary)
        # write each lines
        writer.writerows(temporary)

Ici, il est tout à fait possible d'ajouter une nouvelle fonction entre ces
deux lignes :

.. code-block:: python

    temporary = parse_all(reader)
    # ici, par exemple : temporary = new_function(temporary)
    temporary = transform_all(temporary)

Outre la possibilité d'intervenir dans le code, et de le factoriser, un autre
avantage est qu'au lieu d'avoir une seule énorme boucle de traitement de
l'information, il y a maintenant deux fonctions qui peuvent être testée
unitairement, pour en prouver le comportement :

* ``parse_all`` accepte n'importe quel itérable, pas seulement un CSV, et
  retourne un générateur qu'il est possible d'inspecter aussi.
* ``transform_all`` fonctionne de la même façon, en acceptant un itérable et en
  retournant à son tour un générateur.

Cela veut aussi dire qu'il est plus simple d'ajouter ou de retirer des
traitements intermédiaires avant ou après les appels à ``parse_all`` et
``transform_all``. [#]_

Yield
=====

Sans chercher à détailler plus dans cet article (déjà long), il est possible
de créer un générateur à partir d'un mot clé du langage : ``yield``. Ce mot
clé transforme automatiquement la fonction dans laquelle il est appelé en
générateur, c'est à dire qu'appeler la fonction ne va pas retourner son
résultat, mais un générateur sur lequel itérer.

Pour reprendre les deux fonctions de l'exemple précédent, elles peuvent être
modifiées pour utiliser ``yield`` au lieu de retourner directement un
générateur :

.. code-block:: python

    def parse_all(lines):
        """Apply ``parse`` to each valid line in ``lines``."""
        for line in lines:
            if line:
                yield parse(line)

    def transform_all(lines):
        """Apply ``transform`` to each valid line in ``lines``."""
        for line in parse_lines(lines):
            if apply_condition_on(line):
                yield transform(line)

Dans les deux cas le résultat de l'appel **est** un générateur :

.. code-block:: pycon

    >>> type(parse_all(range(10)))
    <class 'generator'>

Ici, la situation reste simpliste, et il n'y a pas vraiment de différence entre
les deux implémentations. J'espère néanmoins que présenter cette possibilité
vous donnera des idées et des pistes de réflexion.

Imbrication
===========

Jusqu'à présent, je n'ai utilisé qu'une seule boucle dans mes expressions,
que ce soit pour générer une liste ou pour créer un générateur.

Comment transformer le code suivant avec des listes en compréhension ou un
générateur ?

.. code-block:: python

    data = []
    for i in range(10):
        for y in range(i + 1):
            if (i + y) % 2 == 0:
                data.append((i, y, i * y))

La logique et la structure sont les mêmes :

1. ``(i, y, i * y)`` est l'élément qui nous intéresse
2. ``i in range(10)`` et ``y in range(i + 1)`` sont les itérations
3. ``(i + y) % 2 == 0`` est la condition pour ajouter un nouvel élément

La syntaxe sera alors la suivante :

.. code-block:: python

    data = [
        # élément
        (i, y, i * y)
        # itération
        for i in range(10)
        for y in range(i + 1)
        # condition
        if (i + y) % 2 == 0
    ]

Notez une chose importante : les itérations sont déclarées dans l'ordre dans
lequel elles vont être exécutées, c'est à dire de haut en bas :

* La première itération donne la variable ``i``.
* La seconde itération donne la variable ``y``, et utilise pour cela la
  variable ``i`` précédemment déclarée.

Sans cet ordre de déclaration, il ne serait pas possible d'utiliser ``i`` dans
``range(i + 1)`` puisque cette variable n'existerait pas encore.

Les autres itérables
====================

En Python, la class ``list`` n'est pas la seule permettant de représenter un
itérable : les `Tuple`__, les `Set`__, et les `Mapping`__ existent aussi.

.. __: https://docs.python.org/3/library/stdtypes.html#tuples
.. __: https://docs.python.org/3/library/stdtypes.html#set-types-set-frozenset
.. __: https://docs.python.org/3/library/stdtypes.html#mapping-types-dict

Tuple
-----

La classe ``tuple`` fonctionne comme une version immuable de la classe
``list``. Pour générer un ``tuple``, le plus simple est de transformer une
expression de générateur en ``tuple`` :

.. code-block:: pycon

    >>> tuple(i**2 for i in range(10))
    (0, 1, 4, 9, 16, 25, 36, 49, 64, 81)

Notez l'absence de parenthèses ou de crochets superflus.

Set
---

La classe ``set`` dispose de propriétés intéressantes, puisque c'est une liste
dont tous les éléments sont uniques. Il n'est pas nécessaire de lui fournir un
itérable où les éléments sont déjà uniques, la classe ``set`` s'en occupera
elle même. Contrairement à la classe ``tuple`` il est possible d'utiliser
directement la syntaxe des listes en compréhension en utilisant des accolades :

.. code-block:: pycon

    >>> {i**2 for i in range(10)}
    {0, 1, 64, 4, 36, 9, 16, 49, 81, 25}

Vous noterez à l'occasion qu'un objet ``set`` n'est pas ordonné.

Mapping
-------

La classe ``dict`` (et ses divers dérivés) permet de déclarer des mappings
de type (clé, élément), et d'accéder à un élément non pas via son index, mais
via sa clé. Tout comme les objets de la classe ``set``, il est possible
d'utiliser une syntaxe similaire aux listes en compréhension :

.. code-block:: pycon

    >>> {i: i**2 for i in range(10)}
    {0: 0, 1: 1, 2: 4, 3: 9, 4: 16, 5: 25, 6: 36, 7: 49, 8: 64, 9: 81}

Vers l'infini et au-delà
========================

Les listes en compréhension et les générateurs sont des outils très puissants,
et je n'ai fait qu'effleurer la surface des possibilités.

Par exemple, qu'est-ce qui se passerait si, à l'origine de votre traitement de
données se trouvait un générateur infini ? Un générateur qui écoute une socket
et ``yield`` chaque message reçu ne se termine théoriquement jamais, pas tant
que la connexion est ouverte. C'est ce genre de réflexions qui amènent à la
programmation asynchrone [#]_ que je vous invite à découvrir.

Dans une autre direction, il existe la programmation fonctionnelle. En
transformant le code d'une imbrication de boucles ``for-in`` et de ``if-else``
par une suite d'opérations sur des données, il devient possible de transformer
son code suivant les paradigmes de la programmation fonctionnelles. Il faut
alors aborder les fonctions pures, se poser la question de l'immutabilité, et
de beaucoup d'autres concepts. [#]_

Après tout si j'aime Python, c'est aussi pour ces morceaux de sucres.

Notes
=====

.. [#] Façon que je trouve des plus élégantes, au passage.
.. [#] Non, pas avec quelques éléments dans une liste, il faut plutôt imaginer
       de très grandes listes, avec plusieurs centaines de milliers, voire des
       millions d'éléments. Encore mieux si générer chaque élément prend de la
       place en mémoire.
.. [#] J'utilise très peu cette fonction en dehors de mes exemples à but
       pédagogique, car je préfère souvent d'autres façons de traiter mes
       itérateurs. Elle est cependant très pratique pour démontrer le
       fonctionnement de ces derniers, et j'invite à en lire la documentation
       (la fonction `next`__ dans les built-in de Python).
.. [#] Peut-être pour un autre article !
.. [#] J'utilise ici la class built-in ``range`` qui permet de générer une
       liste de ``n`` éléments. C'est un outil très pratique que je vous invite
       à découvrir (lisez `sa documentation`__).
.. [#] C'est un cas où réfléchir avec des types peut éventuellement aider :

       * le type de la liste ``raw_source`` est ``List[Union[str, None]]``
       * le premier générateur est une fonction qui a pour signature
         ``List[Union[str, None]] -> List[str]``
       * le second est une fonction qui a pour signature
         ``List[str] -> List[str]``

       Ce n'est pas aussi précis que je le souhaiterais, c'est une limite du
       langage à laquelle je me confronte de plus en plus. Néanmoins, cela
       permet d'exprimer, en partie, la logique des traitements.
.. [#] Ma mémoire me joue souvent des tours. Mon truc, ce sont les systèmes
       complexes, pas le code compliqué.
.. [#] Je pourrais (encore) écrire un autre article sur le fait que,
       fondamentalement, il est possible d'appliquer une suite de traitements à
       partir de la donnée d'origine avec une approche fonctionnelle :

       .. code-block:: python

           lines = csv.reader(in_csv)
           for func in [parse_all, transform_all]:
               lines = func(lines)
           writer.writerows(lines)

       Ce qui peut être ré-écrit avec ``reduce`` :

       .. code-block:: python

           from functools import reduce

           lines = csv.reader(in_csv)
           lines = reduce(
               lambda lines, func: func(lines),
               [parse_all, transform_all],
               lines,
           )
           writer.writerows(lines)

       Je pourrais aller plus loin et tout faire en une seule opération. Bon,
       d'accord, je vais écrire un autre article (peut-être, un jour).
.. [#] Et donc au module built-in ``asyncio`` et aux coroutines (lisez la
       `documentation`__)
.. [#] Que je suis **très loin** de maîtriser suffisamment pour écrire dessus.

.. __: https://docs.python.org/3/library/functions.html#next
.. __: https://docs.python.org/3/library/stdtypes.html#range
.. __: https://docs.python.org/3/library/asyncio.html
