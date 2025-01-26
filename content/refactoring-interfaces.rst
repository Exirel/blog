=========================
Refactoring et interfaces
=========================

:date: 2024-12-14 22:00
:slug: refactoring-interfaces
:authors: Florian Strzelecki
:summary: Refactoring et interfaces, loose coupling et SOLID
:category: dev
:tags: python, software architecture, refactoring

Lorsqu'il est question d'interface dans le code, la plupart des développeurs
vont probablement penser à quelque chose comme ``IController`` ou
``ParserInterface``, ou autre construction faite pour la programmation orientée
objet, où une classe concrète implémente une interface.

Cependant, dans le cadre du refactoring, j'ai tendance à considérer le concept
d'interface dans un sens plus large : c'est tout ce qui constitue le contrat
d'interface entre l'intérieur d'un bout de code et l'extérieur. Ainsi, lorsque
je `refactore une fonction`__ je prends en compte tant son comportement que
ses entrées et sorties comme faisant partie d'un contrat entre la fonction et
là où je vais en avoir besoin.

Quelque part, les "interfaces" au sens programmation orientée objet ne sont
jamais qu'une façon de représenter ce contrat, et il n'est pas surprenant
de les retrouver à une place de choix dans les principes SOLID.

.. __: {filename}/refactoring-fonction.rst


SOLID ?
=======

Étant partiellement autodidacte [#]_, je n'ai pas toujours eu un intérêt
fulgurant pour les grands principes informatiques. Non pas que je les ignore
puisque j'en ai besoin dans mon travail, cependant je trouve que la pratique
et l'enseignement par l'exemple sont plus efficaces qu'une suite d'acronymes
parfois répétés comme un mantra [#]_.

Si je vous dis cela, c'est parce que j'applique (en partie) les principes
`SOLID`__ depuis plus longtemps que je n'en connais les noms. J'ai d'ailleurs
tendance à oublier ces derniers, tant et si bien qu'il n'est pas rare que je
doive réfléchir s'il faut appliquer I (`ISP`__, pour "Interface segregation
principle") ou D (`DIP`__ pour "Dependency inversion principle") à la
situation [#]_ que je cherche à résoudre ou expliquer.

Mettons donc ces principes de côté pour la suite, puisque je ne compte
expliquer ni ces principes, ni comment les appliquer [#]_. Je vais plutôt
partir d'un bout de code, et essayer de voir comment réfléchir en pensant aux
interfaces. En particulier : la dépendance entre deux modules, et comment
arriver aux bonnes abstractions et interfaces.

.. __: https://fr.wikipedia.org/wiki/SOLID_(informatique)
.. __: https://fr.wikipedia.org/wiki/Principe_de_s%C3%A9gr%C3%A9gation_des_interfaces
.. __: https://fr.wikipedia.org/wiki/Inversion_des_d%C3%A9pendances


Préférences et recherche
========================

Tout se passe dans deux fichiers :

* ``search.py`` contient le code du module de recherche, notamment une fonction
  qui permet d'effectuer une recherche en respectant les préférences d'un
  utilisateur
* ``user.py`` contient le code du module des utilisateurs, avec une classe de
  profil utilisateur qui stocke des informations de profil, incluant les
  préférences de recherche

Voici un extrait du module ``search`` qui montre *l'interface* de la fonction
``search_for_user`` sans plus rentrer dans les détails :

.. code-block:: python

    # file: search.py

    def search_for_user(
        profile: user.Profile,
        term: str,
    ) -> ResultSet:
        ...

Et un extrait du module ``user`` où je n'indique que la méthode qui
m'intéresse pour la démonstration :

.. code-block:: python

    # file: user.py

    class Profile:
        def get_search_settings(self) -> search.SearchSettings:
            ...

Il faut imaginer que la méthode ``get_search_settings`` exploite plusieurs
attributs de la classe pour créer un objet ``search.SearchSettings``.


Analyse des problèmes
=====================

Ce qui compte maintenant à la lecture de ces extraits, c'est l'analyse de la
structure générale de ces deux modules et de là comprendre comment ils
s'articulent pour répondre à la fonctionnalité de recherche. Cela permet
d'isoler deux problèmes qui sont l'interdépendance et le couplage fort.

L'interdépendances apparaît très vite :

* le module ``search`` dépend du module ``user`` au travers de
  ``user.Profile``, qui lui permet d'obtenir un profil de recherche
* le module ``user`` dépend en retour du module ``search`` pour créer un objet
  qui sera utile à la fonctionnalité de recherche

Cette interdépendance entraîne à son tour le problème du couplage fort, où il
n'est plus possible de retirer ou modifier l'un des modules sans avoir besoin
d'altérer le second.

Au premier abord, il est tout à fait raisonnable de ne pas s'alarmer de la
situation. Après tout, la recherche est faite pour l'utilisateur, et il est
peu probable que la fonctionnalité soit retirée. Les tests ne sont sans doute
pas si complexes à écrire ou à maintenir, et tout semble plutôt logique dans le
fonctionnement.

Le problème de l'interdépendance et du couplage fort, c'est qu'il ouvre la
porte à d'autres problèmes : comme chaque bout de code a accès à beaucoup plus
qu'il n'a besoin, il permet à un développeur peu attentif de renforcer un peu
plus le couplage fort jusqu'où jour où les deux modules seront tellement liés
que la moindre modification coûtera plus cher.

C'est le coût du "code smell", du "legacy code", ou encore de ce qu'on
appelle [#]_ la "dette technique". C'est parce qu'il y a un coût caché, presque
invisible, à ce genre de pratiques, que des principes ont fini par émerger. De
la même façon que le *coding style* est une liste de règles à appliquer issues
de l'expériences de tout ce qu'il ne faut *pas* faire, c'est l'expérience de
problèmes de structures qui a donné des listes de principes comme SOLID, les
architectures hexagonales, en oignons, ou la version recombinée
`clean architecture`__ [#]_.


Poser des questions
===================

Qu'arrive-t-il si, comme moi, vous avez du mal à retenir tous ces principes et
ces règles par cœur ? Contrairement au coding style que j'évoquais, il
n'existe pas vraiment de *linter* capable de vous dire "ah, ceci n'est pas la
bonne architecture !" [#]_ et devoir ouvrir un livre n'est pas toujours
pratique lorsqu'on a une deadline à respecter.

Mon approche est de me poser une série de questions. Des "et si ?" qui vont me
guider sur le chemin du bon contrat d'interface :

* Et si je dois supprimer un module parce qu'il n'est plus nécessaire ?
* Et si je dois réécrire un module à cause d'une autre dépendance devenue
  obsolète ?
* Et si je dois extraire un bout de la fonctionnalité dans une autre
  application ?
* Et si je dois réutiliser la même fonctionnalité à partir d'une source de
  données différentes ?

Ces questions, je me les pose toujours avec la même préoccupation : la
maintenance et l'évolution du code. Développer de nouvelle feature n'est
généralement qu'une partie du travail, il est fréquemment nécessaire de
modifier, altérer, réutiliser, ou supprimer une fonctionnalité existante pour
l'adapter à un changement de contexte et/ou de besoin.

Je trouve, à titre purement personnel, que poser des questions en rapport avec
mon expérience de développement m'est plus utile pour trouver les bonnes
solutions que de chercher à comprendre comment appliquer des principes
théoriques.

.. __: https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html


Pourquoi ?
==========

En parlant de question, celle qui revient toujours dans le top 3 des premières
questions que je pose lors d'une relecture de code : **pourquoi** ?

Regardons justement la fonction ``search.search_for_user`` :

.. code-block:: python

    def search_for_user(
        profile: user.Profile,
        term: str,
    ) -> ResultSet:
        ...

Pourquoi cette fonction a-t-elle besoin du profil utilisateur ? Elle n'a pas
besoin d'en connaître tous les détails, et pourrait se contenter de recevoir
directement les préférences de recherche, le reste ne la concerne pas :

.. code-block:: python

    def search_for_user(
        settings: SearchSettings,
        term: str,
    ) -> ResultSet:
        ...

Cette différence force effectivement tous les endroits du code qui appellent
cette fonction à **savoir** comment obtenir des préférences de recherches à
partir d'un profil utilisateur. Cela pourrait justifier de conserver une
la dépendance qu'a le module ``user`` pour le module ``search``.

Le problème, c'est que le module ``user`` représente des données métiers qui
sont au centre de l'application : nécessaire à l'authentification, à la gestion
des préférences de l'utilisateur pour plusieurs fonctionnalités, etc. c'est un
module central. Toute dépendance envers une autre partie de l'application rend
la maintenance de cette dernière de plus en plus complexe.

... et s'il était possible d'inverser cette dépendance ?


Inverser la dépendance
======================

Le problème que nous avons avec le code est ici, dans le module ``user`` :

.. code-block:: python

    class Profile:
        def get_search_settings(self) -> search.SearchSettings:
            ...

Si c'est bien le module ``search`` qui définit sa propre interface de
recherche, c'est le module ``user`` qui l'implémente, et donc qui en dépend. Il
est probable qu'une personne bien intentionnée a voulu respecter le principe
`DRY`__ (Don't Repeat Yourself) en factorisant la création d'un
objet ``search.SearchSettings`` à partir d'un profil utilisateur directement
sur la classe ``user.Profile``.

.. __: https://fr.wikipedia.org/wiki/Ne_vous_r%C3%A9p%C3%A9tez_pas

Le problème de cette approche, c'est que s'il y a plusieurs objets métiers
comme ``user.Profile``, alors chacun va devoir dépendre du module ``search``,
créant de plus en plus de problèmes pour la maintenance. Il faut donc opter
pour une autre stratégie.

Cette stratégie, c'est de faire porter toutes les dépendances à la
fonctionnalité de recherche : c'est elle qui a besoin de représenter des
requêtes, des préférences, des résultats, etc. C'est donc à elle de s'adapter
aux objets métiers, et pas l'inverse.

Ceci passe par une modification du module ``search`` :

.. code-block:: python

    class SearchSettings:
        @classmethod
        def from_user_profile(
            cls,
            profile: user.Profile,
        ) -> Self:
            ...

Oui, cela veut dire que le module ``search`` dépend toujours du module
``user``, et en retour ``user.Profile`` doit exposer un certain nombres de
données pour permettre au module ``search`` de créer l'objet qui lui convient.
Cependant, il existe désormais une couche intermédiaire entre la recherche et
les objets métiers. Cela permet de réduire le couplage entre les deux modules.


Compromis et principes
======================

Cette solution ne propose pas d'éliminer entièrement les dépendances entre tous
les modules, puisqu'à la fin, le module ``search`` dépend toujours du module
``user``. Placer la conversion du profil utilisateur dans le module ``search``
crée un précédent, et c'est tout sauf un choix anodin. Lorsqu'il faudra ajouter
un cas d'usage avec un autre objet métier, cela créera probablement une
nouvelle dépendance.

Cependant, c'est là que réside le compromis : il est maintenant possible de
retirer et modifier la fonctionnalité de recherche sans toucher au reste...
même si toucher au reste peut amener à altérer la fonctionnalité de recherche.
C'est une question de choix [#]_ que de prioriser certaines dépendances plutôt
que d'autres. Il est tout à fait possible d'aller plus loin - c'est un choix
qui doit se faire au cas par cas, et qu'il ne faut pas hésiter à remettre en
question 6 mois plus tard.

Vous noterez que je n'ai reparlé ni d'interface ni des principes SOLID jusqu'à
présent. Je vous invite à reprendre le code de ``search`` :

* pour appeler ``search_for_user``, il faut un objet ``SearchSettings``
* pour créer un objet ``SearchSettings``, une option est d'utiliser
  ``from_user_profile``
* pour appeler ``from_user_profile``, il faut un objet ``user.Profile``

Et c'est ça, l'interface du module de recherche : la fonction de recherche a
besoin d'un contexte de recherche, et la façon d'obtenir ce contexte dépend
entièrement des règles du module ``search``. Peu importe que ce contexte
provienne d'un profil utilisateur, d'une commande, ou d'un événement extérieur,
cette interface est la seule *vérité* qui compte.

Quant aux principes SOLID, je vous laisse l'exercice de trouver lesquels
correspondent à mes choix. Au quotidien, j'utilise plutôt des questions que des
principes, qui sont pour moi soit trop absolus soit trop théoriques pour être
réellement *pratique*.


Notes
=====

.. [#] Partiellement, puisque j'ai tout de même obtenu un DUT Informatique
       à l'IUT de La Rochelle en 2006.
.. [#] La meilleure façon de m'énerver est de répondre par un acronyme ou une
       citation d'un principe lors d'une code review pour justifier tout et son
       contraire.
.. [#] En l'occurrence, aucun et les deux à la fois : nous parlions d'injection
       de dépendance, qui est une technique qui peut participer à respecter
       plusieurs principes SOLID sans en être un directement.
.. [#] Wikipedia et quelques recherches Google feront l'affaire pour ça.
.. [#] À tort, dirais-je.
.. [#] Je ne suis pas vraiment un admirateur d'Uncle Bob, cependant il y a de
       bonnes choses à picorer ici et là. Par exemple lorsqu'il dit :

           Conforming to these simple rules is not hard, and will save you a
           lot of headaches going forward.

       Je ne suis pas vraiment d'accord qu'il s'agit de règles simples, ou
       qu'il soit facile de les respecter. Par contre, ces règles peuvent
       définitivement vous aider à réduire la quantité de problèmes pénibles à
       résoudre.
.. [#] À l'heure où j'écris ces lignes, ni ChatGPT ni Copilot ne sont capables
       de répondre correctement à des problématiques d'architecture logicielle
       qui dépassent un contexte très restreint. Peut-être que cela changera
       dans le futur, mais ce que nous avons pour le moment n'est vraiment pas
       au niveau de mes attentes.
.. [#] Et choisir, c'est renoncer.
