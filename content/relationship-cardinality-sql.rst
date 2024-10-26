=============================
Relation : cardinalité et SQL
=============================

:date: 2024-10-26 12:00
:slug: relation-cardinalite-sql
:authors: Florian Strzelecki
:summary: Représenter les cardinalités de relations en SQL
:category: dev
:tags: sql, model

One to one, one to many, many to many... si vous êtes dans le développement
depuis assez longtemps, vous avez sans doute déjà entendu parler de ces termes
là.

.. note::

    Dans les exemples de requêtes SQL j'utilise la syntaxe pour `PostgreSQL`__.
    Il est normalement possible d'adapter la majorité des requêtes pour
    d'autres SGBD, tels que MySQL/MariaDB ou Microsoft SQL Server, en prenant
    en compte leurs différentes limites, contraintes, et spécificités.

.. contents:: Sommaire

.. __: https://www.postgresql.org/


Cardinalité et notation
=======================

Avant de parler des relations, j'ai besoin d'expliquer la façon de noter la
cardinalité des relations que j'utilise. La `cardinalité`__ [#]_, c'est ce qui
décrit le nombre minimum et maximum d'objets reliés entre eux. Par exemple
ceci ::

    [A]- 0..1 --- 2..4 [B]

Veut dire que pour chaque objet A, entre 2 et 4 objets B lui sont reliés.
De l'autre côté, chaque objet B est relié à zéro ou un et un seul objet A.

La notation ``x..y`` me permet de dire "au moins ``x`` et au plus ``y``". En
plus des valeurs numériques, je peux utiliser ``*`` :

* ``0..*`` pour "zéro, un, ou plusieurs", qui se raccourcit en ``*``
* ``5..*`` pour "au moins cinq"

Ceci étant derrière nous [#]_ passons au sujet de fond : comment transposer des
relations et leurs cardinalités dans une base de données relationnelles ? Pour
la suite de l'article, je considère que tous les objets disposent d'une clé
primaire (notée "``PK``") et que chaque clé étrangère (notée "``FK(<table>)``")
est basée sur la ``PK`` de la table ``<table>``. Lorsque je parle du champ
``B.FK(A)``, je veux parler du champ de la table B qui porte une contrainte de
clé étrangère vers la table A.

.. __: https://fr.wikipedia.org/wiki/Cardinalit%C3%A9_(programmation)


One to One
==========

Chaque objet A est relié à un et un seul objet B, qui est lié en retour au
même objet A. Je note cette relation ainsi ::

    [A]- 1 --- 1 -[B]

En pratique dans une base relationnelle, votre premier instinct est sans
doute... de ne faire qu'une seule table ! Et dans la plupart des cas, c'est
probablement le plus simple et le plus efficace.

Cependant, il existe des cas où il n'est pas possible d'altérer l'une des
tables. Par exemple, lorsque la table A appartient à une application, et un
plugin souhaite ajouter de l'information à propos de A et ne peut le faire
qu'au travers d'une autre table. En pratique, les contraintes relationnelles
seront de cette forme ::

    [A]- 1 --- 0..1 -[B]

Où B portera la responsabilité de la clé étrangère en ajoutant une contrainte
d'unicité sur ``B.FK(A)`` :

.. code-block:: sql

    CREATE TABLE A
    (
        pk INTEGER PRIMARY KEY,
    );

    CREATE TABLE B
    (
        pk INTEGER PRIMARY KEY,
        fk INTEGER NOT NULL UNIQUE REFERENCES A(pk)
    );

Il restera au plugin de s'assurer de l'existence de B pour chaque ligne de
A. [#]_ Fort heureusement, il est toujours possible d'utiliser des
``LEFT JOIN`` pour obtenir tous les objets A avec les données de la table B
(qui peuvent donc être NULL) :

.. code-block:: sql

    SELECT A.pk, B.pk
    FROM A
    LEFT JOIN B ON B.fk = A.pk;

À noter que s'il est possible d'avoir des lignes de A sans ligne de B, il n'est
pas possible d'avoir une ligne de B sans ligne de A, puisque ``B.FK(A)`` ne
peut pas être null (contrainte ``NOT NULL``). [#]_

.. note::

    Si les deux tables peuvent être modifiées, alors n'importe laquelle des
    deux tables peut porter la relation.


One to Many
===========

Chaque objet de A est relié à zéro, un, ou plusieurs objet B, qui est lié en
retour à cet objet A uniquement, que je note ::

    [A]- 1 --- * -[B]

Dans cette configuration, la table A ne peut pas porter la relation, et la
solution immédiate est de faire porter la clé étrangère ``FK(A)`` à la table B,
ce qui en fait une variation du one to one (notez l'absence d'une contrainte
``UNIQUE`` sur ``FK(A)``) :

.. code-block:: sql

    CREATE TABLE A
    (
        pk INTEGER PRIMARY KEY
    );

    CREATE TABLE B
    (
        pk INTEGER PRIMARY KEY,
        fk INTEGER NOT NULL REFERENCES A(pk)
    );

Il s'agit d'une relation assez classique, plutôt fréquente, et qui pose
d'ordinaire peu de difficultés puisqu'il est toujours possible, en récupérant
un objet B, d'obtenir l'objet A associé avec un ``INNER JOIN`` :

.. code-block:: sql

    SELECT B.pk, A.pk
    FROM B
    INNER JOIN A ON A.pk = B.fk;

Cela permet notamment de représenter des relations de propriétés ou de
composition, comme le cas d'une commande composée de lignes : une ligne ne peut
pas exister seule, elle est nécessairement reliée à une commande. C'est aussi
une façon de représenter une relation "parent-enfant" dans une classification,
où chaque objet n'appartient qu'à une seule et unique classe parente.

Fonction d'agrégation
---------------------

Qui dit plusieurs, dit agrégation : l'une des joies des données relationnelles
c'est aussi de pouvoir effectuer des regroupements sur les relations.

Par exemple pour obtenir le nombre de B pour chaque ligne de A, je me tourne
vers l'instruction `GROUP BY`__ et la fonction d'agrégation ``COUNT`` :

.. code-block:: sql

    SELECT A.pk, COUNT(B.pk) AS count_b
    FROM A
    LEFT JOIN B ON B.fk = A.pk
    GROUP BY A.pk;

Ici, l'usage de ``LEFT JOIN`` permet de récupérer les lignes de A n'ayant
aucune ligne de B associée, c'est à dire lorsque ``COUNT(B.pk)`` retourne 0. Si
vous voulez ignorez ces lignes, vous pouvez utiliser un ``INNER JOIN`` à la
place.

Cependant, si vous voulez uniquement les lignes de A possedant au moins 10
lignes de B associées, c'est l'instruction `HAVING`__ qu'il faudra utiliser :

.. code-block:: sql

    SELECT A.pk, COUNT(B.pk) AS count_b
    FROM A
    LEFT JOIN B ON B.fk = A.pk
    GROUP BY A.pk
    HAVING count_b >= 10;

Pour utiliser un exemple plus concret, cela permet de ne récupérer que les
produits (table ``product``) ayant au moins **50** avis (table ``review``) dont
la note (``review.rating``) est au moins de **3** :

.. code-block:: sql

    SELECT product.pk, COUNT(review.pk) AS count_reviews
    FROM product
    INNER JOIN review ON review.fk = product.pk
    WHERE review.rating >= 3
    GROUP BY product.pk
    HAVING count_reviews >= 50;

Comprendre la cardinalité de cette relation, et comprendre comment la
représenter en SQL, c'est tout cela qui permet de savoir comment répondre à des
besoins métiers de ce genre.

.. __: https://www.postgresql.org/docs/17/queries-table-expressions.html#QUERIES-GROUP
.. __: https://www.postgresql.org/docs/17/queries-table-expressions.html#QUERIES-GROUP

Parent en option
----------------

Dans le cas où la ``FK(A)`` est optionnelle, noté de cette façon ::

    [A]- 0..1 --- * -[B]

Cela exprime un parent optionnel, ou une composition optionnelle. C'est le cas
par exemple d'un magasin qui peut appartenir à un groupe ou être indépendant.
Cela se transpose aisément lors du ``CREATE TABLE`` en retirant la contrainte
``NOT NULL`` sur la ``FK(A)`` :

.. code-block:: sql

    CREATE TABLE A
    (
        pk INTEGER PRIMARY KEY
    );

    CREATE TABLE B
    (
        pk INTEGER PRIMARY KEY,
        fk INTEGER REFERENCES A(pk)
    );

Là encore, pour récupérer toutes les lignes de B avec en option leur parent,
c'est vers le ``LEFT JOIN`` qu'il faut se tourner en traitant les cas où les
valeurs de A sont nulles :

.. code-block:: sql

    SELECT B.pk, A.pk
    FROM B
    LEFT JOIN A ON A.pk = B.fk;

Il est à noter que les requêtes intéressantes dépendent beaucoup de la nature
de la relation entre les deux tables. La cardinalité, la modélisation, et
l'implémentation ne sont finalement que des outils et des représentations pour
arriver à vos fins.


Many to Many
============

Nous arrivons à la dernière des trois relations, qui s'exprime ainsi ::

    [A]- * --- * -[B]

Dans ce cas de figure, A et B peuvent être reliés entre eux plusieurs fois dans
les deux sens. Il n'est plus possible, côté SQL, de faire porter la FK par
l'une ou l'autre des tables.

Transitivité relationnelle
--------------------------

Si vous avez déjà rencontré ce cas de figure, vous en avez probablement déjà la
solution. Cependant, arrêtons nous un instant sur la modélisation. S'il n'est
pas possible d'implémenter directement la relation entre A et B, il est
possible d'exploiter une propriété des cardinalités : la transitivité.

Prenez ces deux relations ::

    [A]- 1 --- * -[R]
    [B]- 1 --- * -[R]

Et où ``R`` est composé d'un couple unique ``FK(A)`` et ``FK(B)``. Dans ce cas,
ces relations décrivent un système où A et B sont reliés à plusieurs d'entre
eux par **transitivité**, c'est à dire qu'au travers de R, A est relié à
plusieurs B, et B est relié à plusieurs A au travers de R.

Cette modélisation respecte donc les contraintes d'origines ::

    [A]- 1 --- * -[R]- * --- 1 -[B]

Cela nous permet de retrouver deux relations One To Many, tout en applicant
une contrainte de clé primaire sur la table R :

.. code-block:: sql

    CREATE TABLE A
    (
        pk INTEGER PRIMARY KEY
    );

    CREATE TABLE B
    (
        pk INTEGER PRIMARY KEY
    );

    CREATE TABLE R
    (
        fk_a INTEGER NOT NULL REFERENCES A(pk),
        fk_b INTEGER NOT NULL REFERENCES B(pk),
        PRIMARY KEY (fk_a, fk_b)
    );

.. note::

    La clé primaire de la table de relation ``R`` est composée des deux clés
    étrangères ``FK(A)`` et ``FK(B)``, puisque ``R`` ne peut pas relier deux
    fois les mêmes objets.

Cela permet d'obtenir tous les objets B liés à un objet A :

.. code-block:: sql

    SELECT B.pk
    FROM B
    INNER JOIN R ON R.fk_b = B.pk
    WHERE R.fk_a = ?

Ou dans l'autre sens, tous les objets A liés à un objet B :

.. code-block:: sql

    SELECT A.pk
    FROM A
    INNER JOIN R ON R.fk_a = A.pk
    WHERE R.fk_b = ?

Et le reste est entre vos mains, que ce soit les agrégations, les filtres qui
dépendent de A et/ou de B, etc. Cela peut ne pas vous sembler évident au début
et parfois il ne faut pas hésiter à s'entraîner sur un petit jeu de données
pour comprendre toutes les possibilités que s'offrent à vous. [#]_

Propriété de la relation
------------------------

Avant de se séparer, un dernier petit détail : jusqu'à présent je n'ai traité
les relations que comme de "simples" liens, comme si je tirais une ficelle
entre des objets pour les relier. Avec une relation many to many, nous avons
une table en plus, et rien ne nous empêche d'ajouter des propriétés à cette
table !

Du côté de la modélisation, c'est l'un des intérêts de la réification des
relations en classe, qui consiste à traiter la relation comme une classe comme
les autres, et donc à lui affecter des attributs et des comportements.

Par exemple, la relation (table ``art_cat``) entre un article de
blog (table ``article``) et des catégories (table ``category``) peut se voir
adjoindre un attribut "est la catégorie principale" (``art_cat.is_main``) :

.. code-block:: sql

    CREATE TABLE article
    (
        pk INTEGER PRIMARY KEY,
    );

    CREATE TABLE category
    (
        pk INTEGER PRIMARY KEY,
    );

    CREATE TABLE art_cat
    (
        fk_a INTEGER NOT NULL REFERENCES article(pk),
        fk_c INTEGER NOT NULL REFERENCES category(pk),
        is_main BOOL NOT NULL DEFAULT TRUE,
        PRIMARY KEY (fk_a, fk_b)
    );

    CREATE UNIQUE INDEX art_cat_is_main_unique
        ON art_cat (fk_a, is_main)
        WHERE is_main;

Ici, ce que nous voulons c'est que pour n'importe quelle association de
catégories à un article, il n'existe pour chaque article qu'une seule et unique
catégorie principale (et donc potentiellement plusieurs catégories
secondaires). La solution que j'ai choisie est un `index partiel`__, avec une
contrainte conditionnelle (``WHERE is_main``) d'unicité
(``UNIQUE INDEX``) [#]_.

Cela permet de s'assurer que cette requête ne retournera qu'au plus un seul
résultat par article, avec sa catégorie principale (si elle existe, sinon
``category.pk`` sera ``NULL``) :

.. code-block:: sql

    SELECT article.pk, category.pk
    FROM article
    LEFT JOIN art_cat
        ON art_cat.fk_a = a.pk
        AND art_cat.is_main IS TRUE;

.. __: https://www.postgresql.org/docs/current/indexes-partial.html#INDEXES-PARTIAL-EX3


Performances
============

Dans cet article j'ai voulu explorer l'implémentation en SQL des notions de
relations et de cardinalités. Il y a certainement beaucoup plus à dire sur les
solutions que j'ai proposées, notammant au regard de la **performance**.

Un index, qu'il soit une contrainte d'unicité, une clé primaire, ou une clé
étrangère, ce n'est pas un choix sans conséquences. Un ``LEFT JOIN``, un
``INNER JOIN``, et les fonctions d'agrégations peuvent avoir des conséquences
importantes sur les temps de réponses de vos requêtes. Chaque situation demande
d'être étudiée et analysée avec les outils à votre disposition pour votre SGBD,
et fait en toute conscience.

Je m'en voudrais de vous présenter autant d'exemple sans vous avertir :
optimiser votre base de données et les relations de votre modèle est un
exercice que je vous invite à faire avec rigueur et minutie.


Notes
=====

.. [#] La version anglaise est plus détaillée : `Cardinality_(data_modeling)`__
.. [#] Ceci n'était pas un cours sur la notation ou les cardinalités, je ne
       fait que toucher la surface du sujet comme introduction au reste de
       l'article.
.. [#] Exercice que je laisse au plaisir du lecteur. Comme d'habitude.
.. [#] Ce qu'il est possible de faire en rendant ``B.FK(A)`` optionnelle,
       ce qui nous éloigne de la relation d'origine ::

           [A]- 0..1 --- 0..1 -[B]

.. [#] Oui, ça aussi, c'est un exercice que je laisse à votre responsabilité.
.. [#] Il est possible d'utiliser une autre technique, compatible avec MySQL :
       vous pouvez rendre nullable le champ ``is_main`` tout en appliquant une
       contrainte d'unicité. MySQL ignorant les valeurs ``NULL`` vous obtenez
       la même intégrité des données en associant ``TRUE`` aux catégories
       principales, et ``NULL`` pour toutes les autres.

       Cela donne cette structure :

       .. code-block:: sql

           CREATE TABLE art_cat (
               fk_a INT UNSIGNED NOT NULL,
               fk_b INT UNSIGNED NOT NULL,
               is_main BOOL NULL,
               CONSTRAINT UNIQUE (fk_a, is_main)
           );

       **Attention** : vous ne pouvez pas insérer ``FALSE`` plus d'une fois
       par article, il faut donc utiliser ``NULL`` pour désigner une catégorie
       secondaire.

.. __: https://en.wikipedia.org/wiki/Cardinality_(data_modeling)
