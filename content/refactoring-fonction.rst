==========================
Refactoring d'une fonction
==========================

:date: 2024-10-13 11:00
:tags: python
:slug: refactoring-fonction
:authors: Florian Strzelecki
:summary: Refactoring d'une fonction
:category: dev

Le refactoring, certains l'aiment, d'autres le détestent. Pour moi, c'est une
partie intégrante de mon processus de développement. C'est une activité
quotidienne, et au cœur même de ma façon de faire.

Reste à savoir comment. Pour cela, voici un exemple (fictif et inspiré de
faits réels) :

.. code-block:: python

    import configparser, csv, os, typing


    class Settings(typing.Namedtuple):
        source_dir: str
        filename: str


    def merge() -> None:
        # read ini file and get settings
        ini_filename = os.env.get(
            'APP_CONFIG_FILENAME',
            os.path.expanduser('~/.config/app.ini'),
        )
        ini_config = configparser.ConfigParser()
        ini_config.read(ini_filename)

        settings = Settings(
            ini_config['merge']['source_dir'],
            ini_config['merge']['filename'],
        )

        # load data from all CSV files
        data = []
        for csv_filename in os.path.listdir(settings.input_dir):
            if not os.path.splitext(csv_filename)[1] == '.csv':
                continue

            path = os.path.join(settings.input_dir, csv_filename)
            with open(path, newline='') as fd:
                reader = csv.reader(fd)

                for i, line in enumerate(reader):
                    if i > 0:  # skip header
                        data.append(line)

        # merge everything together
        with open(settings.filename, 'w', newline='') as fd:
            writer = csv.writer(fd)
            for line in data:
                writer.writerow(line)

    if __name__ == "__main__":
        merge()

Un script qui n'a qu'un seul but : merger les fichiers ``.csv`` depuis un
dossier en un seul fichier. En l'état, ce script est légèrement configurable
grâce à une variable d'environnement. Il a aussi du mal à traiter des fichiers
CSV trop volumineux, puisqu'il doit lire l'ensemble des fichiers et tout
stocker en mémoire. Enfin, il n'est pas possible de tester unitairement quoi
que ce soit sans mettre des ``mock`` partout - ce que personnellement je
déteste.

C'est donc un candidat parfait pour le refactoring.

.. contents:: Sommaire

Découper en fonction
====================

Le code étant plutôt clair, chaque partie est rendue visible par la présence
d'un commentaire inline :

1. ``# read ini file and get settings``
2. ``# load data from all CSV files``
3. ``# merge everything together``

C'est le point de départ idéal du refactoring. Je commence donc par extraire
le code dans trois fonctions [#]_, dont voici les signatures :

.. code-block:: python

    def get_settings() -> Settings:
        ...

    def get_data(settings: Settings) -> list[list[str]]:
        ...

    def write_to_csv(
        settings: Settings,
        data: list[list[str]],
    ) -> None:
        ...

La fonction ``get_settings`` permet d'obtenir la configuration, qui est
nécessaire pour le reste du script. La fonction ``get_data`` se concentre sur
la récupération des données, et la fonction ``write_to_csv`` s'occupe du reste.
Cela permet de réécrire la fonction merge de cette façon :

.. code-block:: python

    def merge() -> None:
        settings = get_settings()
        data = get_data(settings)
        write_to_csv(settings, data)

Il s'agit là d'une technique très classique de refactoring : prendre une
fonction un peu dense et la séparer en plusieurs parties. L'intérêt ici n'est
pas d'être :abbr:`DRY (Don't Repeat Yourself)`, il est de séparer un gros
problème en plusieurs petits problèmes. N'attendez pas d'avoir besoin de
réutiliser du code pour séparer une fonction en plusieurs, parfois la
lisibilité et la maintenance justifient d'effectuer une telle transformation.

.. note::

    Le refactoring est un outil pour améliorer la maintenance du code, en
    distribuant la complexité du code en plusieurs morceaux plus accessibles.


Réduire le couplage
===================

L'objet ``settings`` est nécessaire pour ``get_data`` et pour ``write_to_csv``.
Cependant, cela implique un couplage fort entre ces fonctions et la classe
``Settings``. En pratique, ces deux fonctions n'ont pas besoin de l'ensemble
des informations, et leurs interfaces peuvent être modifiées comme suit :

.. code-block:: python

    def get_data(source_dir: str) -> list[list[str]]:
        ...

    def write_to_csv(
        filename: str,
        data: list[list[str]],
    ) -> None:
        ...

La fonction ``get_data`` n'a pas besoin de connaître plus que le répertoire à
parcourir, et la fonction ``write_to_csv`` n'a besoin que du fichier de
destination. Il revient donc à la fonction ``merge`` d'articuler l'intégration
de ces deux fonctions :

.. code-block:: python

    def merge() -> None:
        settings = get_settings()
        data = get_data(settings.source_dir)
        write_to_csv(settings.filename, data)

L'objet ``settings`` n'étant plus un paramètre des autres fonctions, j'obtiens
la liberté de manipuler à ma guise les fonctions qui en dépendent. Cela réduit
son statut de `god object`__ : un objet tellement essentiel à l'application que
l'ensemble du code en dépend.

.. note::

    Le refactoring est un outil pour améliorer l'isolation au sein du code en
    réduisant le couplage fort, en réfléchissant mieux aux interfaces.

.. __: https://en.wikipedia.org/wiki/God_object


Réduire les contraintes
=======================

La fonction ``write_to_csv`` demande une **liste**, en Python un objet de type
``list``. Cet objet est bien plus contraignant que les besoins réels de la
fonction, puisqu'il nécessite de stocker toute l'information en mémoire. Comme
la fonction ``write_to_csv`` n'a pas besoin de toutes les capacités d'une
``list``, il est possible de réduire les contraintes de type avec cette
signature :

.. code-block:: python

    from collections.abc import Iterable


    def write_to_csv(
        filename: str,
        data: Iterable[list[str]],
    ) -> None:
        ...


Un objet de type ``Iterable`` est tout ce qu'il faut à la fonction
``write_to_csv``, qui ne fait qu'itérer une seule et unique fois. Cela
permet d'optimiser la fonction ``get_data`` avec l'usage du mot clé ``yield``
(lire la documentation de Python sur l'`expression yield`__) :

.. code-block:: python

    def get_data(source_dir: str) -> Iterable[list[str]]:
        for csv_filename in os.path.listdir(source_dir):
            if not os.path.splitext(csv_filename)[1] == '.csv':
                continue

            path = os.path.join(source_dir, csv_filename)
            with open(path, newline='') as fd:
                reader = csv.reader(fd)

                for i, line in enumerate(reader):
                    if i > 0:  # skip header
                        # yield the line instead of storing it
                        yield line

La fonction ``get_data`` est désormais une fonction génératrice. Elle n'a plus
besoin de stocker l'intégralité du contenu en mémoire. Elle est aussi plus
flexible d'utilisation : pour appliquer un filtre ou une transformation sur les
lignes retournées, il n'est pas nécessaire de passer par une liste, une
`expression génératrice <{filename}list-comprehension.rst>`_ fera l'affaire
puisqu'elle retourne un ``Iterable`` aussi.

.. note::

    Le refactoring est un outil pour améliorer la flexibilité du code et en
    optimiser les usages.

.. __: https://docs.python.org/3/reference/expressions.html#yield-expressions


Remonter les paramètres
=======================

Jusqu'à présent nous nous sommes intéressés aux deux dernières fonctions, en
laissant de côté la fonction ``get_settings`` que voici en intégralité et avec
tout son contexte :

.. code-block:: python

    import configparser, os, typing


    class Settings(typing.Namedtuple):
        source_dir: str
        filename: str


    def get_settings() -> Settings:
        ini_filename = os.env.get(
            'APP_CONFIG_FILENAME',
            os.path.expanduser('~/.config/app.ini'),
        )
        ini_config = configparser.ConfigParser()
        ini_config.read(ini_filename)

        return Settings(
            ini_config['merge']['source_dir'],
            ini_config['merge']['filename'],
        )

Cette fonction n'est pas très pratique à tester, puisqu'elle se repose sur la
présence d'une variable d'environnement. Cela veut dire que dans tous les tests
de cette fonction **et de toutes les fonctions qui en ont besoin**, il faut
mettre en place un mock de l'environnement.

Pour éviter cela, je peux mettre le nom du fichier en paramètre de la fonction,
et laisser la fonction ``merge`` être responsable du reste :

.. code-block:: python

    def get_settings(filename: str) -> Settings:
        ...

    def merge() -> None:
        ini_filename = os.env.get(
            'APP_CONFIG_FILENAME',
            os.path.expanduser('~/.config/app.ini'),
        )

        settings = get_settings(ini_filename)
        data = get_data(settings.source_dir)
        write_to_csv(settings.filename, data)

Cependant... la fonction merge n'a pas besoin de savoir comment récupérer ce
fichier de configuration, et en poussant la logique plus loin, nous pouvons
considérer qu'il n'est pas de la responsabilité de la fonction de savoir
comment obtenir un fichier de configuration en premier lieu !

.. code-block:: python

    def merge(settings: Settings) -> None:
        data = get_data(settings.source_dir)
        write_to_csv(settings.filename, data)


    if __name__ == '__main__':
        ini_filename = os.env.get(
            'APP_CONFIG_FILENAME',
            os.path.expanduser('~/.config/app.ini'),
        )

        settings = get_settings(ini_filename)
        merge(settings)

Les conséquences de ce choix ne sont pas anodines : j'assigne à la fonction
``merge`` la responsabilité de la logique métier, c'est à dire les actions à
mener d'un point de vue fonctionnel. Tout le reste, c'est à dire l'intégration
avec le système, est remonté le plus haut possible, c'est à dire dans la
section ``__main__`` du script. Et il en va de même du côté des tests :

* tester le comportement de ``merge`` et des autres fonctions ne nécessite plus
  de savoir où se trouve le fichier de configuration, son existence étant
  abstraite au travers de la classe ``Settings`` ;
* pour tester l'ensemble de la fonctionnalité, il est nécessaire d'exécuter la
  commande python du point de vue de l'utilisateur, et sans connaissance du
  code ;

ce qui correspond à ma philosophie de tests [#]_. D'un côté, je vais pouvoir
tester le **comportement du code** en mode boîte blanche. De l'autre, je peux
tester le **fonctionnement de l'application** en mode boîte noire.

.. note::

    Le refactoring est un outil d'amélioration de votre architecture logicielle
    en vous aidant à répartir au mieux les responsabilités de chaque partie.


Conclusion
==========

Avec cet exemple, j'espère partager mon approche du code et l'usage que j'ai du
*refactoring*. J'aime séparer le code par domaine de responsabilité, et j'aime
réduire le couplage entre les différents composants, et qui n'aime pas une
belle optimisation de performance sans compromettre la maintenabilité ?

Je ne fais que toucher la surface [#]_, puisque je n'ai pas abordé les outils
d'aide et d'assistance au refactoring, comme `mypy`__ ou les tests. Ici, je me
suis plutôt concentré sur la flexibilité et la maintenance plutôt que sur
l'optimisation et l'ajout de fonctionnalités. J'espère cependant que cela vous
aidera à réfléchir à vos pratiques de refactoring la prochaine fois que vous
serez en face de votre code.

.. __: https://mypy-lang.org/


Notes
=====

Merci à `Kaci Adjou`__ pour sa relecture, qui m'évita quelques erreurs
d'inattention.

.. [#] L'implémentation de ces trois fonctions est un exercice laissé au
       lecteur. Je crois en vous.
.. [#] Philosophie de tests qui demande un autre article à elle seule.
.. [#] D'ailleurs, vous avez peut-être vu plusieurs lignes qui mériteraient
       d'être modifiées...

.. __: https://github.com/Bruce1347
