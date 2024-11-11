=========================
Refactoring et interfaces
=========================

:date: 2024-11-11 20:00
:slug: refactoring-interfaces
:authors: Florian Strzelecki
:summary: Refactoring et interfaces, loose coupling et SOLID
:category: dev
:tags: python, solid

Si la dernière fois que j'ai parlé de `refactoring`__ il s'agissait uniquement
d'une fonction, cette fois je vais parler d'*interfaces et d'abstractions*, en
évoquant au passage les principes SOLID.

.. __: {filename}/refactoring-fonction.rst

SOLID ?
=======

Étant partiellement autodidacte [#]_, je n'ai pas toujours eu un intérêt
fulgurant pour les grands principes informatiques. Non pas que je les ignore
puisque j'en ai besoin dans mon travail, cependant je trouve que la pratique
et l'enseignement par l'exemple sont plus efficaces qu'une suite d'acronymes
parfois répétés comme un mantra [#]_.

Si je vous parle de tout ça, c'est pour évoquer le cas des cinq principes
`SOLID`__ que j'applique depuis bien plus longtemps que je n'en connais les
noms. En vérité, je ne me rappelle à peu près jamais du nom de chacun des
principes, et il me faut toujours réfléchir quelques instants pour savoir
lequel s'applique dans telle ou telle situation. Par exemple la dernière fois
que j'ai eu une discussion avec des pairs sur SOLID, nous nous sommes demandés
lequel de I (`ISP`__, pour "Interface segregation principle") ou de D (`DIP`__
pour "Dependency inversion principle") était applicable à notre situation [#]_.

Cela étant dit, je ne compte ni expliquer les principes, ni comment les
appliquer directement [#]_. Je souhaite partir du code, en reprenant des
exemples (simplifiés et/ou anonymisés) rencontrés lors de ma carrière, et
montrer le chemin que j'ai suivi pour les corriger et arriver à un résultat
satisfaisant. Ce qui m'intéresse est moins le résultat que le chemin pour y
arriver : respecter les principes SOLID devrait provenir d'une compréhension
des problèmes à éviter et à résoudre plutôt qu'une recette appliquée par cœur.

Sans plus attendre donc, parlons d'un problème assez fréquent : la dépendance
entre deux modules, et comment arriver aux bonnes abstractions et interfaces.

.. __: https://fr.wikipedia.org/wiki/SOLID_(informatique)
.. __: https://fr.wikipedia.org/wiki/Principe_de_s%C3%A9gr%C3%A9gation_des_interfaces
.. __: https://fr.wikipedia.org/wiki/Inversion_des_d%C3%A9pendances


Profil et recherche
===================

Tout part d'une fonction de recherche basée sur le profil d'une utilisatrice,
et de deux modules Python qui se retrouvent liés l'un à l'autre par
conséquence.

Le premier module, du fichier ``search.py``, décrit des classes et des
fonctions permettant d'effectuer une recherche de documents. La recherche est
effectuée par une ``Query``, dont l'exécution retourne un ``ResultSet``. La
fonction ``search_for_user`` est un raccourci pratique pour exécuter une
recherche à partir d'un profil utilisateur :

.. code-block:: python

    # file: search.py

    from typing import TYPE_CHECKING

    if TYPE_CHECKING:
        import user


    class ResultSet:
        ...


    class Query:
        def execute(self) -> ResultSet:
            ...


    def search_for_user(
        profile: user.Profile,
    ) -> ResultSet:
        query = profile.as_search_query()
        return query.execute()


Le second module, du fichier ``user.py``, décrit la classe de profil
utilisateur, lié à un ORM, et capable de retourner une requête de recherche à
partir de son attribut ``search_terms`` :

.. code-block:: python

    # file: user.py

    from typing import NewType

    import orm

    import search


    ProfileID = NewType('NewType', str)


    class Profile(orm.Model):
        pk: ProfileID
        search_terms: list[str]

        def as_search_query(self) -> search.Query:
            return search.Query(
                terms=self.search_terms
            )


Enfin, il faut imaginer un troisième fichier, le module ``controller``, qui
décrit le contrôle de l'application. Il fait appel à l'ORM et à un moteur de
template, et sert de glue entre les différents modules ``user`` et ``search`` :

.. code-block:: python

    # file: controller.py

    import orm
    import template

    import user
    import search


    def endpoint(pk: user.ProfileID) -> Response:
        profile = orm.fetch(user.Profile, pk=pk)
        resultset = search.search_for_user(profile)
        return template.as_response('search.html', data={
            'request': request,
            'resultset': resultset,
        })


Analyse des problèmes
=====================

Présenté avec cet extrait de code, au-delà de la relecture de *style* (qui n'a
que peu d'intérêt ici), il est important d'analyser la structure de ce dernier
et d'en extraire les différents problèmes : interdépendance et couplage fort.

Interdépendance
---------------

En étudiant les dépendances de chaque module, il est possible de découvrir
rapidement les interdépendances :

* Comme attendu, ``controller`` dépend de tout le monde : il sert de glue, et
  dépend donc des modules internes (``user`` et ``search``) comme des outils
  externes (``orm`` et ``template``). Aucun module ne dépend de lui.
* Le module ``user`` dépend de l'``orm`` du projet, ainsi que du module
  ``search``, pour lequel il implémente une méthode ``as_search_query``.
* Enfin, le module ``search`` dépend du module ``user``. Il évite le problème
  d'import circulaire en exploitant la capacité de Python sur les annotations
  de type.

Si les dépendances de ``controller`` ne posent pas trop de questions (pour le
moment), l'inter-dépendance entre ``user`` et ``search`` est plus gênante :
chacun a besoin de l'autre pour fonctionner.

Couplage fort
-------------

En plus de l'interdépendance entre ``search`` et ``user``, le couplage est
d'autant plus fort dans la signature de deux fonctions : la méthode
``user.Profile.as_search_query`` d'une part, et la fonction
``search.search_for_user`` d'autre part.

* La méthode ``as_search_query`` doit retourner un objet de type
  ``search.Query``.
* La fonction ``search_for_user`` a besoin d'un objet ``user.Profile``.

Dans les deux cas, c'est un objet concret d'un autre module qui est utilisé,
chacun embarquant avec lui beaucoup plus de propriétés que nécessaire au
comportement de l'autre. Par exemple, la fonctionnalité de recherche n'a pas
besoin de l'identifiant du profil, et le profil n'a pas réellement besoin de
connaître l'objet qui permet d'exécuter des requêtes.

Poser des questions
-------------------

Étudier le code au travers de ses défauts nécessite de connaître à l'avance les
défauts possibles (ici interdépendances et couplage fort). C'est la même chose
pour le *coding style* : c'est au travers d'une liste de règles à appliquer que
nous savons ce qu'il faut corriger. Cette liste de défauts possibles vient avec
la pratique et l'expérience [#]_, et c'est ce qui a donné des principes comme
SOLID, les architectures hexagonales, en oignons, ou la version recombinée
`clean architecture`__ [#]_.

Cependant, il est possible d'aborder les problèmes d'une autre façon, en posant
ce genre de questions :

* Que se passe-t-il si l'un des deux modules est supprimé car il n'est plus
  nécessaire ? Ou bien qu'il faille le réécrire à cause d'une dépendance
  extérieure obsolète ?
* Que se passe-t-il si je veux extraire le module ``search`` dans une
  bibliothèque à part ?
* Comment réutiliser ``search.search_for_user`` avec les mêmes données
  provenant d'autre chose qu'un profil utilisateur ?

Ces questions sont toutes issues de la même préoccupation : la maintenance et
l'évolution du code. Développer de nouvelle feature n'est généralement qu'une
partie du travail de développement, il est fréquemment nécessaire de modifier,
altérer, réutiliser, ou supprimer une fonctionnalité existante pour l'adapter à
un changement de contexte et/ou de besoin.

Je trouve, à titre purement personnel, que poser des questions en rapport avec
mon expérience de développement m'est plus utile pour trouver les bonnes
solutions que de chercher à comprendre comment appliquer des principes
théoriques.

.. __: https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html


Pourquoi ?
==========

La première étape du refactoring est de revenir aux besoins de chaque élément,
et de comprendre l'articulation de ces besoins. Bref, de poser la question :
**pourquoi ?**

Prenons par exemple la fonction ``search.search_for_user`` :

.. code-block:: python

    def search_for_user(
        profile: user.Profile,
    ) -> ResultSet:
        query = profile.as_search_query()
        return query.execute()

Cette fonction exécute une recherche, et retourne un résultat. Sa dépendance
avec le profil utilisateur tient à la méthode ``Profile.as_search_query()`` :

.. code-block:: python

    def as_search_query(self) -> search.Query:
        return search.Query(
            terms=self.search_terms
        )

Alors, pourquoi a-t-elle besoin du ``profile`` si tout ce dont elle a besoin
c'est une ``query`` ? D'ailleurs, cette fonction n'a besoin de rien de
spécifique provenant de ``user.Profile``, mis à part un objet ``query``, et
cela pourrait être déplacé directement dans le contrôleur :

.. code-block:: python

    def endpoint(pk: user.ProfileID) -> Response:
        profile = orm.fetch(user.Profile, pk=pk)
        # execute the query
        query = profile.as_search_query()
        resultset = query.execute()
        return template.as_response('search.html', data={
            'request': request,
            'resultset': resultset,
        })

Cependant, peut-être que plusieurs modules du code ont besoin d'effectuer une
recherche sur la base d'un profil utilisateur ? Cela pourrait expliquer la
présence de cette fonction ``search_for_user``. Si la question n'est plus de
savoir pourquoi cette fonction existe, cela pose de nouvelles questions pour
retirer la dépendance :

* Où faut-il déclarer la fonction ?
* Peut-on remplacer la signature de la fonction ?

Et ça, c'est uniquement pour la fonction ``search_for_user``, il faut
recommencer le procédé pour chaque fonction, chaque méthode, à l'origine d'une
dépendance. Cela revient, finalement, à travailler la conception de ses
interfaces, et à trouver de quoi chaque composant est responsable.


Concevoir l'interface de recherche
==================================

Manifestement, l'interface du module ``search`` pose question en générant une
suite de dépendances. Au travers des besoins, à savoir réutiliser du code et
effectuer des recherches liées aux besoins métiers (et à leurs données),
l'interface de search se repose sur un couplage fort :

* Chaque fonction réutilisable se basant sur un objet métier génère une
  dépendance forte avec cet objet.
* Chaque objet métier permettant de retourner un objet ``search.Query`` se met
  une dépendance forte envers ``search``.

Peut-être que certains choix ont été fait pour suivre de bons principes de
qualité de code, comme le principe DRY (pour *Don't repeat yourself*),
cependant ce n'est pas suffisant pour obtenir une archicture maintenable.

Revenons plutôt sur l'interface de recherche, de quoi avons-nous besoin ?

* Pour chaque recherche, nous avons besoins de critères de recherche.
* Pour chaque objet métier, la possibilité d'extraire des critères de
  recherche.

Pour prendre un exemple concret, voici ce qui pourrait être fait :

.. code-block:: python

    # search.py

    import typing

    if typing.TYPE_CHECKING:
        import user


    class UserProfileQuery(Query):
        @classmethod
        def from_user_profile(
            cls, profile: user.Profile,
        ) -> Self:
            return cls(terms=user.search_terms)


    def search_for_user(
        search: ProfileSearch,
    ) -> ResultSet:
        return search.execute()

Au final, le module ``user`` ne dépend plus du module ``search``, qui défini à
la fois l'interface de recherche (``UserProfileQuery``) et la façon d'obtenir
la requête de recherche (``from_user_profile``). Grâce à l'encapsulation de la
logique dans l'objet ``UserProfileQuery`` il est même possible de se passer
entièrement de la fonction ``search_for_user`` dans le contrôleur :

.. code-block:: python

    # controller.py

    def endpoint(pk: user.ProfileID) -> Response:
        profile = orm.fetch(user.Profile, pk=pk)
        query = search.UserProfileQuery.from_user_profile(profile)
        resultset = query.execute()
        return template.as_response('search.html', data={
            'request': request,
            'resultset': resultset,
        })


Compromis
=========

Cette solution ne propose pas d'éliminer entièrement les dépendances entre tous
les modules, puisqu'à la fin, le module ``search`` dépend toujours du module
``user``. Placer la conversion du profil utilisateur en requête dans la classe
``search.UserProfileQuery`` n'est pas un choix annodin : il donne la
responsabilité de la conversion au module ``search``. C'est au travers de cette
responsabilité que je réduis le couplage fort entre les deux modules.

En supprimant la dépendance dans un sens, il devient beaucoup plus simple et
facile de supprimer entièrement la fonctionnalité de recherche par rapport à un
profil utilisateur : cela revient à supprimer la classe
``search.UserProfileQuery`` ainsi que la fonction ``controller.endpoint``.

De la même façon, il est possible de supprimer entièrement la classe
``user.Profile`` en remplaçant ``UserProfileQuery.from_user_profile`` sans pour
autant avoir besoin de changer beaucoup de code : ``UserProfileQuery`` reste
un objet de type ``search.Query`` qui peut toujours être utilisé comme tel.

Tout cela, c'est grâce à des compromis et des choix [#]_, issus de l'étude des
besoins et en se posant quelques questions en lien avec la maintenance de
l'application.

Vérification par les principes
==============================

En début d'article je parlais de SOLID, et il me semble intéressant de finir
avec ces principes, en vérifiant si la solution proposée respecte les cinq
principes :

1. **Single responsibility principle** : que ce soit la classe ``Profile`` ou
   la classe ``UserProfileQuery``, chacune n'a qu'une seule et unique raison
   d'être. La fonction ``endpoint`` ne se préoccupe pas non plus des détails
   d'implémentation et ne fait qu'articuler le flow d'information de la requête
   vers la réponse.
2. **Open/closed principle** : ce principe est difficile à bien illustrer ici.
   Disons qu'il n'est pas enfreint dans cet exemple, puisque nous supposerons
   que la classe de base ``Query`` est ouvert à l'extension via
   ``UserProfileQuery``, tout en étant fermée à la modification directe.
3. **Liskov substitution principle** : là aussi, ce principe est respecté
   par la classe ``UserProfileQuery`` qui remplace l'objet ``Query`` sans
   changer le comportement global du code (le contrôleur appelle toujours la
   même méthode ``execute`` pour obtenir un ``ResultSet``). Ce n'est pas un
   principe évident à respecter, et il est plein de nuances [#]_.
4. **Interface segregation principle** : ce principe est le plus fortement
   illustré dans cet exemple, puisqu'au début nous avions une classe
   ``Profile`` contenant à la fois des données métiers et des données servant
   à la recherche, exposant ainsi beaucoup trop d'information au module
   ``search``. Avec ``UserProfileQuery``, seul le strict minimum est à
   disposition de la recherche et du contrôleur.
5. **Dependency inversion principle** : joker. J'ai fait un compromis ici, en
   n'utilisant pas de `classe abstraite`__ ou de `typing.Protocole`__, et en
   ne cherchant pas à séparer entièrement les différents modules. Cependant,
   ni ``user`` ni ``search`` ne dépendent de ``controller``, et ``user`` ne
   depend que de l'ORM, respectant *The Dependency Rule* de Clean Architecture.

.. __: https://docs.python.org/3/library/abc.html#abc.ABC
.. __: https://docs.python.org/3/library/typing.html#typing.Protocol


Notes
=====

.. [#] Partiellement, puisque j'ai tout de même obtenu un DUT Informatique
       à l'IUT de La Rochelle en 2006.
.. [#] La meilleure façon de m'énerver est de répondre par un acronyme ou une
       citation d'un principe lors d'une code review pour justifier tout et son
       contraire.
.. [#] En l'occurence, aucun et les des deux à la fois : nous parlions
       d'injection de dépendance, qui est une technique qui peut participer à
       respecter plusieurs principes SOLID sans en être un directement.
.. [#] Wikipedia et quelques recherches Google feront l'affaire pour ça.
.. [#] Dont je dispose aujourd'hui en assez bonnes quantités, il est vrai.
.. [#] Je ne suis pas vraiment un admirateur d'Uncle Bob, cependant il y a de
       bonnes choses à picorer ici et là. Par exemple lorsqu'il dit :

           Conforming to these simple rules is not hard, and will save you a
           lot of headaches going forward.

       Je ne suis pas vraiment d'accord qu'il s'agit de règles simples, ou
       qu'il soit facile de les respecter. Par contre, ces règles peuvent
       définitivement vous aider à réduire la quantité de problèmes pénibles à
       résoudre.
.. [#] Et choisir, c'est renoncer.
.. [#] Qui ne seront définitivement pas abordées ici.
