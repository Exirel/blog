===========
Autodidacte
===========

:date: 2025-09-26 03:00
:slug: autodidacte
:authors: Florian Strzelecki
:summary: Mon apprentissage de la programmation en autodidacte.
:category: blog
:tags: documentation, software engineering

Le premier langage de programmation auquel j'ai été confronté est le
`QBasic`__, que j'ai découvert avec son IDE, disponible sur un vieux PC
récupéré par mon père à son travail. Windows 3.1, un ancêtre de Word, et cet
IDE furent les outils parfait pour l'adolescent et amateur de jeu de rôle que
j'étais à l'époque. Contrairement au PC familial, installé dans le salon et
qu'il me fallait partager avec ma fratrie, celui-ci était installé dans ma
chambre, et ce qu'il manquait de connexion à Internet était compensé par
l'autonomie et l'indépendance qu'il m'apportait. Je ne compte pas les heures,
souvent tardives, passées à écrire pour du jeu de rôle ou pour les nombreuses
tentatives de romans que j'avais en tête. [#]_

Je ne me suis pas pour autant lancé dans QBasic immédiatement, ce n'est arrivé
qu'après deux événements importants : un cours de `MPI`__, et un programme sur
une calculatrice.

.. __: https://fr.wikipedia.org/wiki/QBasic
.. __: https://fr.wikipedia.org/wiki/Mesures_physiques_et_informatique


.. contents:: Sommaire


Un élève dissipé
================

Un peu à la même période où ce PC est entré dans ma vie, je suis entré au lycée
en seconde, avec l'option MPI : Mesure Physique et Informatique. Si j'avais
assez peu d'intérêt pour la physique, l'électronique, ou l'électricité, j'étais
en revanche fasciné par l'informatique. De plus, l'option n'étant présente
que dans un lycée privé dans ma ville, cela me permettait d'échapper à l'école
publique où je n'avais connu que le harcèlement scolaire, et ce depuis la
primaire. [#]_

Je me souviens bien d'un cours en particulier : l'enseignant nous présenta un
programme, avec quelques concepts clés (une boucle ``WHILE``, et l'instruction
``INPUT``). Puis, il nous demanda de recopier un programme qu'il avait écrit au
tableau... dans lequel j'ai détecté une erreur que je me suis empressé de lui
faire remarquer. Autant vous dire qu'il n'apprécia pas vraiment mon
intervention [#]_ —d'autant plus que cela semblait beaucoup m'amuser. [#]_

Puis vient le travail en binôme : nous devions écrire un court programme
reprenant ce que nous avions appris pour résoudre un problème trivial. La
programmation ayant immédiatement piqué mon intérêt, il ne me prit pas plus de
15 min pour réaliser l'exercice, me laissant 45 bonnes minutes pour laisser
libre cours à mon imagination. S'il faut tirer une leçon de mon expérience
scolaire, c'est qu'il ne faut **jamais** laisser libre cours à mon imagination
dans une salle de classe.

Après avoir aidé mon binôme à comprendre le programme, après en avoir écrit
plusieurs autres pour explorer les possibilités de mon nouveau jouet, après
avoir aidé d'autres binômes à comprendre l'exercice, je me suis retrouvé à
papoter avec un volume sonore de plus en plus élevé, jusqu'à provoquer l'ire de
mon enseignant. Ce dernier me convoqua donc immédiatement au tableau histoire
de me faire les pieds.

Si son espoir était probablement de corriger le comportement d'un cancre par la
technique de la honte [#]_ du passage au tableau devant toute la classe, il
déchanta rapidement après que j'eus écrit, de tête, la solution de l'exercice
au tableau, et ce en moins de 5 min. Je garde encore en souvenir son air un peu
bougon à grogner à demi mot que je peux retourner à ma place mais que la
prochaine fois attention je dois arrêter de gêner mes petits camarades.


La calculatrice programmable
============================

Cette première rencontre avec la programmation va me laisser songeur : l'idée
même de pouvoir transformer une page blanche en un programme qui s'exécute
devant mes yeux est la chose la plus magique jamais produite par l'humanité.
Ça et tout un tas d'autres choses que je découvre à l'adolescence, tel que
Star Wars, le jeu de rôle, la culture gothique, Matrix, et toutes les
déclinaisons possibles du fantastique, de la SF au *med-fan*, [#]_ des livres
aux films en passant évidemment par les jeux vidéo.

C'est d'ailleurs au croisement du fantastique et de l'informatique que va se
poursuivre ma découverte de la programmation : comme je m'inscris en première
S, j'ai accès à une calculatrice scientifique **programmable**, et comme je
m'ennuis profondément en cours, toute distraction est bonne à prendre. C'est là
qu'intervient un camarade de classe [#]_ qui m'installe rapidement un petit
programme : il s'agit d'un jeu de type "RPG", où je contrôle un personnage qui
doit combattre des monstres de plus en plus fort.

J'y passe des heures, à combattre toute une panoplie de monstres, à choisir
entre améliorer une caractéristique ou une autre, à découvrir les sorts et
capacités spéciales de chaque classe... et à ressentir un immense frustration :
le jeu n'est pas comme je voudrais qu'il soit.

Il n'en faut pas plus pour me décider. Le soir lorsque je rentre chez moi je
n'ai qu'une seule idée en tête (après avoir regardé un épisode de `Batman`__,
de `Sliders`__, ou de `Daria`__), c'est de reproduire le programme de ma
calculatrice sur mon ordinateur avec QBasic. Je découvre la complexité de la
programmation impérative, la gestion des états, des erreurs de saisie, et
surtout, j'apprends petit à petit à lire la documentation, incluse avec l'IDE,
en anglais.

.. __: https://fr.wikipedia.org/wiki/Batman_(s%C3%A9rie_t%C3%A9l%C3%A9vis%C3%A9e_d%27animation,_1992)
.. __: https://fr.wikipedia.org/wiki/Sliders_:_Les_Mondes_parall%C3%A8les
.. __: https://fr.wikipedia.org/wiki/Daria_(s%C3%A9rie_t%C3%A9l%C3%A9vis%C3%A9e_d%27animation)


De QBasic à PHP
===============

Si je m'éclate vraiment avec QBasic, j'ai quelques limitations : je comprends
mal l'anglais technique, et certains concepts m'échappent complètement, et je
n'arrive pas à contourner les limitations du langage. Je comprends par exemple
qu'il m'est possible de créer une "routine" ou "sous-routine", dans un fichier
à part, ce qui devrait me permettre de dépasser la limite du nombre maximum
d'instructions dans un fichier. Malheureusement, ne comprenant pas le concept
de "return value" (valeur de retour, i.e. ce qu'une fonction retourne), je me
retrouve bloqué, et incapable de progresser.

Cependant, mon attention limitée pour une passion m'amène à en découvrir une
autre : et si je programmais **un site web** ? Si cela fait maintenant
plusieurs années que je bidouille avec HTML sur le PC familial, [#]_ je n'ai
pas encore passé le pas vers une application web. Guidé là encore par une
connaissance, je me dirige vers PHP : je découvre `LAMP`__, avec EasyPHP sur
Windows, et je me lance à la découverte des tutoriels (en français) sur PHP.

Mon anglais n'est pas encore suffisant pour me permettre d'accéder à toutes les
ressources nécessaires sur le sujet, et certains sujets m'échappent
complètement. Autant vous dire qu'à l'époque où `Lycos`__ et `Yahoo!`__ sont
mes moteurs de recherche principaux, j'ai encore du mal à résoudre des
problèmes qui me semblent aujourd'hui triviaux : comment ça PHP 3 et PHP 4 ce
n'est pas pareil ? C'est quoi un "array" ? ``$_SESSION`` c'est beaucoup trop
magique ! `T_PAAMAYIM_NEKUDOTAYIM`__ ??? Je ne vous parle même pas d'apprendre
JavaScript, que je choisi soigneusement d'ignorer pour le moment.

Quoi qu'il en soit, je transforme mes belles pages HTML/CSS *optimisées pour
IE6* en site dynamiques grâce à PHP, je me sens limité et frustré par ma
compréhension limitée du langage et de ses outils. Arrive alors dans ma vie un
site qui va changer ma vie.

.. __: https://fr.wikipedia.org/wiki/LAMP
.. __: https://fr.wikipedia.org/wiki/Lycos_(portail_web)
.. __: https://fr.wikipedia.org/wiki/Yahoo!
.. __: https://fr.wikipedia.org/wiki/Paamayim_Nekudotayim


Le Site du Zéro
===============

Lorsque je découvre QBasic, un camarade me conseille plutôt d'apprendre le C++,
et il me conseille alors un site web, le `Site du Zéro`__, qui contient
quelques tutoriels en français. Si j'abandonne rapidement mon apprentissage de
ce langage beaucoup trop complexe pour mes connaissances de l'époque, je garde
le site dans mes favoris.

Quelques temps plus tard, je le ressors lorsque je me mets à PHP : non
seulement tout est en français, en prime il contient bien plus que des
tutoriels sur PHP, avec un cours assez complet sur les bases de données et
l'utilisation de MySQL. J'apprends alors, avec des exercices pratiques, à
effectuer mes premières requêtes SQL, à comprendre la structure des tables, et
même à utiliser des jointures. Le cours m'apprend l'un des fondamentaux de la
programmation : ne jamais faire confiance, sous aucun prétexte, aux entrées
utilisateurs. [#]_

Je ne pense pas être capable d'exprimer à quel point j'ai eu de la chance, et à
quel point c'est un privilège que d'avoir connu l'époque du Site du Zéro. Il
s'agit d'un des piliers de mon apprentissage et de ma culture : accessible,
gratuit, communautaire, il a façonné les principes de mon approche de
la programmation, de l'open-source, et de la pédagogie. Ces valeurs
m'accompagnent encore aujourd'hui, et je suis triste de ne pas connaître
d'équivalent aujourd'hui. [#]_

.. __: https://fr.wikipedia.org/wiki/OpenClassrooms


De l'IUT à la documentation officielle
======================================

Lorsqu'arrive le temps de déposer ses vœux [#]_ pour effectuer des études
supérieures, je me rappelle indiquer, sans trop d'entrain, une licence de math
et informatique à la fac de Bordeaux. Il faut dire que depuis la seconde, j'ai
bien déchanté concernant la filière scientifique : je découvre que je déteste
les maths [#]_ et la bio, et que si mon prof de Physique est génial, il me
gronde dès qu'il doit me rendre une copie qui affiche généralement un faible 13
sur 20. [#]_ Tout ce qui me motive, c'est la partie informatique.

Puis, par un coup du hasard, mon groupe d'amis du moment me parle de l'IUT à
La Rochelle : l'une s'inscrit en bio, l'autre en info... et moi, je n'ai pas
envie de me séparer de mes rares amis de l'époque. [#]_ Je décide alors sur un
coup de tête de présenter mon dossier, le jour même. Je découvre alors qu'il
s'agit **du dernier jour** pour le déposer, et que je dois le faire valider par
le CPE avant de pouvoir l'envoyer. Il me reste 15 min et j'ai rarement couru
aussi vite dans un escalier que ce jour-là.

Tout cela m'amène à la rentrée 2004 à l'IUT de La Rochelle, en section
informatique. Je découvre la programmation orientée objet en C++, puis le
développement de serveur web en Java, et enfin deux compétences essentielles :
l'`algèbre relationnelle`__ et la `conception logicielle`__ (par des cours ACSI,
i.e. Analyse et Conception des Systèmes d'Information, à ne pas confondre avec
l'`ASCII`__). Ces deux compétences me permettent de mieux comprendre le
développement de logiciel et l'utilisation des bases de données. J'apprends les
concepts abstraits derrières les outils concrets de mon quotidien.

Ce n'est pas pour autant la fin de mon apprentissage en autodidacte : PHP n'est
pas enseigné en première année, et les maigres cours en deuxième année sur le
langage sont complètement dépassés. C'est à tel point que j'en viens à
corriger les erreurs du prof (...), et à enseigner moi-même à mes camarades les
bases de la programmation Web.

C'est aussi à cette époque que je renforce ma compréhension de l'anglais
technique, et que je peux donc profiter de la documentation officielle de
PHP...

.. __: https://fr.wikipedia.org/wiki/Alg%C3%A8bre_relationnelle
.. __: https://fr.wikipedia.org/wiki/Conception_de_logiciel
.. __: https://fr.wikipedia.org/wiki/American_Standard_Code_for_Information_Interchange


(Auto)formation continue
========================

Une fois l'IUT terminé, et un DUT (aujourd'hui obsolète) en poche, je me suis
immédiatement lancé dans le monde professionnel [#]_, sans trop savoir comment
évoluerait ma carrière. Il y a presque 20 ans de ça, tout ce que je savais
était mon envie de travailler dans le web, et j'étais même assez fier d'être
un "développeur web". Autant vous dire qu'à l'époque, avec un simple DUT (donc
un niveau affiché de "technicien", et pas d'ingénieur), et voulant faire du
Web, j'étais perçu par les autres ingénieurs comme une sorte de
sous-informaticien. Si je suis architecte aujourd'hui, ce n'est pas sans une
certaine dose d'efforts et de sacrifices. [#]_

Pour toutes ces raisons j'ai continué à apprendre par moi-même, notamment en
suivant les standards du `W3C`__ (à l'époque du xHTML, des années avant HTML5,
période où je découvre la `checklist OpQuast`__), en apprenant à créer des
sites compatibles pour IE6 et Firefox, en découvrant au travail le
fonctionnement de JavaScript et de la technologie `AJAX`__. J'ai appris à
maîtriser les feuilles CSS, et j'ai eu la joie (non) de découvrir tous les
problèmes de sensibilité à la casse et de collation des tables de MySQL.

Ma majorité de mon apprentissage pendant mes deux premières années se fait par
l'échec : j'utilise une solution naïve qui ne fonctionne pas, et plus d'une
fois je ne le découvre qu'en production. Le stress est particulièrement élevé à
plusieurs reprises, et j'ai la chance d'être en bonne santé (physique et
mentale) pour le supporter. Je n'ai pas encore de mentor, et il me manque
définitivement des outils pour m'en sortir : je ne connais aucun `VCS`__, si
j'ai entendu parler des tests à l'IUT je ne connais aucune application pratique
ni framework, et comble de l'ignorance je suis bien incapable d'utiliser SSH ou
de configurer un serveur Apache. Ces briques qui me semblent aujourd'hui
essentielles à mon quotidien me sont étrangères, et personne n'est là pour me
les enseigner.

.. __: https://www.w3.org/
.. __: https://checklists.opquast.com/
.. __: https://fr.wikipedia.org/wiki/Ajax_(informatique)
.. __: https://fr.wikipedia.org/wiki/Logiciel_de_gestion_de_versions


Mentor
======

Ce n'est qu'en sortant du monde des `SSII`__ (aujourd'hui les ESN) que je
trouve un jour mon premier (et derniers à ce jour) mentor en informatique. [#]_
Il ne va pas m'apprendre tel ou tel outil, il va faire mieux que ça : il va me
dire où regarder, comment trouver l'information qui m'intéresse, et comment
creuser un problème pour trouver la solution. C'est le mélange de toute mon
expérience, de mon apprentissage, et de son enseignement qui a changé ma vie,
et sans nul doute le chemin de ma carrière.

Je découvre alors qu'il n'y a rien de vraiment magique, et que tout peut
s'expliquer en revenant aux fondamentaux que j'ai appris avant, et en lisant
beaucoup (beaucoup) (vraiment beaucoup) de documentation : un serveur Apache
est un programme comme les autres, avec sa configuration, et dépend des autres
mécanismes de l'OS et du réseau ; SVN est un système avec un serveur et des
copies locales de fichiers ; le navigateur n'est qu'un client HTTP comme les
autres, etc. tout peut s'expliquer en décortiquant les choses, couche après
couche.

Il me fait aussi découvrir des textes et des concepts, comme
`tout est fichier`__, ou le fameux texte `la cathédrale et le bazar`__. Il
partage avec moi un amour de la programmation et du travail bien fait, de la
documentation, des tests, et du partage des connaissances, autant de principes
et de valeurs que je retrouve finalement dans l'open-source et les mouvements
`Agile`__, `Extreme Programming`__, et le `Software Craftmanship`__. Notez
l'absence de technologies dans ces derniers éléments : en tant que mentor, il
m'a partagé sa **culture**, et m'a fait confiance pour acquérir des **savoirs**
par moi-même —ce que j'ai fait et continue de faire depuis.

.. __: https://fr.wikipedia.org/wiki/Entreprise_de_services_du_num%C3%A9rique
.. __: https://en.wikipedia.org/wiki/Everything_is_a_file
.. __: https://fr.wikipedia.org/wiki/La_Cath%C3%A9drale_et_le_Bazar
.. __: https://fr.wikipedia.org/wiki/M%C3%A9thode_agile
.. __: https://fr.wikipedia.org/wiki/Extreme_programming
.. __: https://fr.wikipedia.org/wiki/Software_craftsmanship


Python
======

En 2010, je quitte mon CDI et la capitale pour retourner en province, à la
recherche de la suite. Je ne savais pas vraiment ce que je voulais, ni dans
quelle direction aller. Je voulais... autre chose. Une autre façon de
travailler, de penser, de collaborer.

Depuis lors, j'ai mis en pratique ce que mon mentor m'apprit jusqu'à ce que je
considère être devenu un ingénieur. J'ai changé de langage de prédilection en
passant à Python, tout d'abord en travaillant sur des projets personnels, puis
en démarchant des entreprises en leur vendant la promesse que j'allais m'en
sortir. J'ai d'ailleurs rencontré un succès fulgurant, puisqu'en 2014, deux ans
après mon choix de carrière, je trouve un job moins d'une heure après avoir
annoncé ma démission de mon job précédent : mon investissement dans la
communauté Python et Django y aura été pour quelque chose. Ça, et la chance de
tomber sur la bonne opportunité au bon moment.

C'est d'ailleurs à la même période qu'un collègue me fait lire "How To Win
Friends & Influence People", de Dale Carnegie. C'est un moment important dans
ma carrière, où je commence à me poser des questions sur mon rôle : pas encore
tout à fait *senior* dans ma tête, j'ai assez d'expertise pour faire progresser
les autres. Pourtant, j'ai du mal à partager ce que je sais au travail.

Qu'à cela ne tienne, je découvre et acquiers une certaine expertise sur ce que
l'on appelle `à l'époque l'ELK`__ (`Elasticsearch`__, `Logstash`__, et
`Kibana`__), et sur `Ansible`__. J'en apprends beaucoup trop pour ma propre
santé mentale sur la configuration d'un serveur `PostgreSQL`__. Et puis je
découvre tout ce qu'il faut savoir sur le e-commerce, les OMS, les
`ERP`__, et la logistique, le tout en discutant avec des collègues. Peu à peu,
je deviens un véritable expert en Python, et je donne de plus en plus de
`conférences sur la documentation`__, sujet pour lequel je me passionne.

.. __: https://www.elastic.co/fr/elastic-stack
.. __: https://www.elastic.co/fr/elasticsearch
.. __: https://www.elastic.co/fr/logstash
.. __: https://www.elastic.co/fr/kibana
.. __: https://docs.ansible.com/
.. __: https://www.postgresql.org/
.. __: https://fr.wikipedia.org/wiki/Progiciel_de_gestion_int%C3%A9gr%C3%A9
.. __: https://www.pycon.fr/2016/videos/lire-ecrire-la-doc.html


Flanconade
==========

En parallèle, à peu près à la même époque, un ami [#]_ m'invite à son cours
d'escrime artistique et de spectacle, encadré par David Héry. Quelques années
se passent, et je rejoins mon maître au `REC Escrime`__, en même temps que je
débute ma formation de cadre fédéral escrime artistique et de spectacle. [#]_
J'ouvre une école de sabre laser artistique, où j'enseigne tous les samedis
matins pendant deux ans, à Rennes, avec un ami, lui aussi éducateur bénévole.

    Il lui avait, d'une vigoureuse `flanconade`__ fait sauter son épée.

    — Alexandre Dumas, les "Trois Mousquetaires"

David est mon second mentor, mais pas en informatique : il m'apprend la
pédagogie, et me fait découvrir l'esprit sportif qui sommeillait manifestement
en moi. [#]_ Son enseignement vient directement alimenter mes réflexions sur
mon rôle de senior software engineer, sur le partage des savoirs dans un rôle
actif, d'enseignant et de mentor. C'est un peu comme un alignement des
planètes : je me rends compte, au travers de l'escrime que j'enseigne, que je
peux former des gens. Je peux leur partager plus que ma passion, avec des
savoirs faire et des savoirs être. C'est ce qui me manquait pour passer à
l'étape d'après... enfin, presque.

.. __: https://www.rec-escrime.fr/
.. __: https://www.cnrtl.fr/definition/flanconade


COVID 19
========

Si je suis né l'année de `Tchernobyl`__, que j'ai vu les `avions s'écraser`__
à la TV en 2001, traversé la `crise des subprimes`__ de 2007 sans la
comprendre, pour m'éveiller au féminisme lors du `#Gamergate`__ de 2014, puis
que j'ai observé avec l'effroi la montée du fascisme avec la première élection
de Donal Trump en 2016, c'est en 2020 que je partage le récent traumatisme qu'a
été le COVID 19, avec ses confinements et son hécatombe mondiale. [#]_

Cet événement met fin à certaines de mes activités sociales, et met le
télétravail sur le devant de la scène.

.. __: https://fr.wikipedia.org/wiki/Catastrophe_nucl%C3%A9aire_de_Tchernobyl
.. __: https://fr.wikipedia.org/wiki/Attentats_du_11_septembre_2001
.. __: https://fr.wikipedia.org/wiki/Crise_des_subprimes
.. __: https://www.nytimes.com/interactive/2019/08/15/opinion/what-is-gamergate.html


Architecte
==========

Alors que les annonces dans la presse se veulent rassurantes sur la montée de
l'épidémie qui touche la Chine, j'obtiens mon premier poste qui marque le plus
gros tournant de ma carrière : je passe d'un rôle de développeur senior à celui
d'architecte junior. Stupeur et tremblement [#]_, je prends un énorme risque,
et c'est uniquement grâce à la cooptation d'un ami [#]_ que j'arrive à
décrocher ce poste. Pendant ce temps, je prends
`la présidence de mon club d'escrime`__, à ma grande surprise, et pour les
quatres années qui suivront.

En tant qu'architecte, j'apprends un tout autre niveau de conception et de
gestion de projet. Ce qui me semblait intéressant de loin devient le cœur de
mes préoccupations : guider les développements tout en prévoyant les questions
opérationnelles et... politiques. Sur le pan technologique, j'apprends en même
temps que je dessine des plans : auto formation accélérée sur `Kafka`__, sur
les API gateway, sur la production de document de conception, sur la norme
`ISO 27001`__ et les process `ITIL`__.

.. __: https://www.rec-escrime.fr/2021/10/29/assemblee-generale-29-octobre-2021/
.. __: https://kafka.apache.org/
.. __: https://fr.wikipedia.org/wiki/ISO/CEI_27001
.. __: https://fr.wikipedia.org/wiki/Information_Technology_Infrastructure_Library


Staff
=====

En 2024, je mets fin au télétravail comme modalité principale, et décide de
rejoindre une entreprise locale : plus besoin de me déplacer à Paris
régulièrement, en 15 min je suis à mon bureau, entouré de collègues. Pourtant,
ce n'est qu'un tout petit changement en comparaison d'un autre : de "senior",
ou "d'architecte", je suis surtout dans un nouveau rôle pour moi, celui de
`staff engineer`__.

Ce rôle, c'est l'aboutissement de ma carrière **jusqu'à aujourd'hui**. Si je me
sens capable d'endosser ce rôle, je sais aussi que ce n'est qu'une étape. Mon
intérêt pour le mentorat devient une nécessité, et mon expertise technique un
"simple" point de départ, en quelque sorte. Il est attendu de moi une
contribution qui dépasse mon équipe voire mon département, pour affecter toute
l'entreprise. Tout ce que j'ai appris pour moi, je dois maintenant permettre
aux autres de se l'approprier. Je dois favoriser les bonnes initiatives, et
faire le tri dans les backlogs techniques. Je dois me saisir de sujets flous,
voire nébuleux, pour les rendre clairs et digestes.

J'en suis là pour le moment. [#]_

.. __: https://staffeng.com/


Privilèges
==========

À l'origine de cet article je souhaitais écrire une courte [#]_ introduction à
la documentation, en expliquant pourquoi c'était devenu un sujet si important
pour moi, dans ma carrière et dans mon travail. Cela s'est vite transformé en
exercice autobiographique. J'invite mon lectorat à croire en sa sincérité, bien
que certains éléments aient été obfusqués et que j'ai choisi d'être
volontairement un peu flou sur certaines dates.

Il existe un mythe du `self-made man`__, et si par bien des aspects mon
ascension sociale s'est faite grâce à mes capacités et à mon travail, je
rejoins `Arnold Schwarzenegger`__ sur la question :

    This is why I don't believe in the self-made man. Why I want you to
    understand that is because as soon as you understand that you are here
    because of a lot of help then you also understand that now is time to help
    others.

    —Arnold Schwarzenegger, 2018

J'ai appris beaucoup de choses par moi-même, et je continue d'être, par bien
des aspects, un **autodidacte**. Pourtant, mon parcours est aussi le résultat
de la chance, de privilèges, d'opportunités aux bons moments, et surtout grâce
à l'aide de mes amis, de mes mentors, et de toutes les personnes qui ont
contribué à mes savoirs et à ma culture. Et à vous toutes et vous tous, je vous
remercie.

.. __: https://fr.wikipedia.org/wiki/Self-made_man
.. __: https://www.youtube.com/watch?v=lF7NqeZuO3E


Notes
=====

.. [#] La plupart sont devenus des "projets fantômes", d'autres sont devenus
       les bases d'autres histoires que je développerai peut-être un jour.
.. [#] Il me faudra un jour trouver le courage d'aborder ce sujet plus en
       détail.
.. [#] Les profs et moi, ça n'a jamais vraiment été une histoire d'amour.
       Notamment parce que je faisais *toujours* ça et que c'est probablement
       très désagréable de m'avoir comme élève.
.. [#] Sale gosse.
.. [#] Procédé classique qui me semble pourtant aussi absurde que cruel.
.. [#] Excusez-moi d'utiliser les termes de mon enfance pour décrire un champ
       entier de ma propre culture.
.. [#] Dont j'ai oublié le nom, sache néanmoins que si jamais tu tombes sur ces
       lignes, tu as été, sans le savoir, la personne la plus influente sur mes
       débuts en informatique et en programmation. Je n'ai jamais pu te dire
       merci, toi et ton serveur auto-hébergé, tes lan party où tu m'as invité
       sans trop me connaître, et tes explications sur HTML et CSS. C'est aussi
       ton groupe d'amis qui m'a fait découvrir Matrix et ce qu'était un switch
       réseau !
.. [#] Saviez-vous qu'il était possible, sur Windows 98, de remplacer le fond
       d'écran par une page HTML avec laquelle il était possible d'interagir ?
.. [#] Leçon qui, d'après mon expérience quotidienne, n'est toujours pas bien
       retenue par les développeurs et développeuses d'aujourd'hui.
.. [#] Je sais que la nostalgie joue une part importante dans mes souvenirs, et
       même si je pense que la programmation web en 2025 permet plus de choses
       plus facilement qu'en 2002, je ne peux que regretter la relative
       simplicité de cette époque.
.. [#] Sur un ancêtre, l'équivalent, ou peut-être même directement sur `APB`__.
.. [#] Mes moyennes de spé maths du premier, second, et troisième trimestre de
       terminale sont croissantes : 7, 8, et 9 sur 20 respectivement.
.. [#] D'après lui je suis un feignant qui ne fait aucun effort, et que si
       j'arrêtais de discuter en cours et que je faisais les exercices de la
       semaine au lieu d'attendre 5 min avant le cours pour les faire dans le
       couloir, peut-être que je pourrais progresser. Plus tard j'obtiens 17
       sur 20 au bac de physique, en ayant ignoré un exercice à 3 points sur la
       physique nucléaire. Je n'ai, à ce jour, jamais compris comment cela
       était arrivé. Reste que mon prof de Physique était un prof génial,
       c'était moi le problème.
.. [#] Et avec le recul, je suis à peu près sûr que j'avais déjà un crush sur
       la fille du groupe. Je suis même sorti avec, mais ça n'a pas fonctionné
       et je l'ai perdue de vue depuis.
.. [#] Absence de motivation à remplir un dossier d'inscription dans une fac,
       à choisir une licence ou une prépa pour faire une école d'ingénieur, et
       bien entendu le facteur matériel le plus important à savoir : l'argent.
       Ou plutôt l'absence de.
.. [#] Je ne dis pas que c'est une bonne chose.
.. [#] Je l'ai recontacté pour l'occasion, l'ayant complètement perdu de vue
       après ça, et j'attends qu'il me permette de citer son nom ici.
.. [#] Avec qui j'ai été `aux législatives de 2012`__ !
.. [#] Spécialité "Grand Siècle", c'est à dire rapière & dague.
.. [#] Croyez-moi de mes amis je suis encore celui le plus surpris de ce fait !
.. [#] Plus tard ce sera la guerre en Ukraine. Quant à aujourd'hui où j'écris
       ces lignes, je ne peux pas dire que je comprenne vraiment la folie de ce
       qui se passe à Gaza.
.. [#] Amélie Nothomb, 1999, chez Albin Michel.
.. [#] Oui, encore un autre !
.. [#] `Ask me anything!`__
.. [#] 530 lignes et 25 notes plus tard, moi aussi ça me fait bien rire.

.. __: https://fr.wikipedia.org/wiki/Admission_Post-Bac
.. __: https://www.ouest-france.fr/bretagne/rennes-35000/benoit-evellin-candidat-pour-le-parti-pirate-1298980
.. __: https://askastaffengineer.com/
