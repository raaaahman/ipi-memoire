# Définition du cadre et des enjeux du projet

Le projet présenté est un module de l'application *Polylang*. Son but est de gérer l'import, comme l'export, de fichiers de traduction.

Les données seront alors transformées de fichiers aux formats *XLIFF* ou *PO*, en entrées de base de données *MySQL*.

## Une première définition du besoin

La définition originale du besoin a pris la forme d'une réunion d'une journée, regroupant les membres de l'équipe suivants:

- Frédéric DEMARLE, dirigeant de *WP Syntex* et chef de projet
- Marianne & Sylvain, les développeurs de l'application
- Christelle DREVON, testeuse en qualité et technicienne de support

Ce projet est à l'initiative de l'entreprise *WP Syntex*, après l'expression d'un besoin de la part de divers clients, collectée à partir de l'outil de support pour le produit *Polylang*.

Ce besoin est d'utiliser un format de **fichier d'échange**, afin d'externaliser les traductions depuis l'outil *WordPress* vers un outil de traduction tiers. (exemple: *Matecat*, *Virtaal*, etc.)

## Enjeux du projet

Lors de la présentation, Frédéric DEMARLE, en tant que chef de projet, a exposé l'intérêt du projet pour la société:

La société a reçu au fil du temps, plusieurs proposition de partenariat de la part d'agences de traductions, souhaitant faire communiquer l'application *Polylang* avec leur **Translation Management System** de référence, souvent un outil que lesdites agences ont elles-mêmes développé. La société a également reçu des demandes de clients souhaitant récupérer leurs fichiers de traduction afin de les utiliser dans leur outil de traduction local, de manière à pouvoir travailler sur ces traductions hors-ligne.

Frédéric DEMARLE, en tant que développeur principal de l'application *Polylang*, a ensuite passé en revue les difficultés pouvant se présenter lors du déroulement du projet.

Au niveau fonctionnel :

- Les systèmes de traductions facturant la quantité de texte à traduire, il faut veiller à ce que l'utilisateur de l'application *Polylang* n'aie pas à traduire plusieurs fois des **contenus** déjà traduits, sauf demande explicite de sa part.
- Veiller à ce que la mise en forme originelle soit respectée à l'import du nouveau fichiers

Au niveau technique :

- Les **métadonnées** pouvant être associées à plusieurs contenus, elles ne doivent pas être traduites à nouveau à chaque traduction d'un de ces contenus.
- Lors de l'import, des traductions pourraient entrer en conflit avec celles pré-existantes sur le **site** de l'utilisateur, il faudra trouver un moyen de gérer les ambiguïtés.
- Les imports comme les exports devront prendre en compte la fonctionnalité de synchronisation, des **contenus** comme des **métadonnées**, existante au sein de l'application *Polylang*.
- Le système de traduction des **chaînes de caractère** isolées diffère de celui des **contenus** de par leur modèle de **persistance** (stockées dans des *fichiers* et non en *base de données*).

## Organisation de l'équipe

L'organisation et la communication au sein de l'équipe ont ensuite été abordées. Le défi ne porte ici pas tant sur la taille de l'équipe (quatre personnes) mais sur son éclatement, les membres étant tous en situation de télé-travail.

Ainsi, une partie de la réunion a porté sur le choix des outils de collaboration. En ressortent notamment:

- *Skype* : Logiciel de messagerie instantanée permettant la communication quotidienne de l'équipe.
- *appear.in* : Application web de visio-conférence pour les réunions d'équipe.
- *Git* : Outil de **gestion des versions** du code source.
- *GitHub* : Outil de partage du **code source** et de la **documentation** technique.
- *Trello* : Sa flexibilité nous permet de l'utiliser à la fois comme tableau de gestion des tâches et comme centre de partage de documentation.

L'équipe a ensuite débattu sur des choix de gestion de projet inspiré de la méthode *SCRUM* ainsi que de la philosophie **agile**:

- La rédaction des spécifications fonctionnelles sous forme d'**user stories**, permet le découpage et la répartition des tâches à réaliser au sein de l'équipe de développement.
- Organisation de cycles de travail sous forme de **sprints** de deux semaines, permettant d'adapter les prochaines **itérations** selon les résultats observés en fin de sprint. Le rythme de deux semaines permet également de s'adapter au planning de formation en alternance des développeurs. Par ce fait, chacun sait ce qu'il devra faire durant l'absence de son collaborateur, ainsi que ce que ledit collaborateur pourra avoir produit durant sa propre absence.
- Des **daily meetings** permettant de garder le contact au quotidien, de suivre l'avancée de chacun, et de suggérer des solutions entre collaborateurs. Ceci est d'autant plus important que l'équipe n'est pas présente sur un même site.
- Une journée de **pair programming** toutes les deux semaines pour les développeurs, leur permettant de s'accorder sur la manière d'écrire le code ainsi que de connecter les travaux réalisés en autonomie.
- Des **code reviews** régulières, permettant d'expliciter et de modifier le code selon les suggestions de l'équipe.

## Environnement technique

Le projet étant une nouvelle fonctionnalité pour l'extension *Polylang* du *CMS* *Wordpress*, certaines technologies étaient donc imposées:

- **PHP** (compatibilité avec la version 5.6) : Langage de scripts *interprété*, sans état (*stateless*) et *dynamiquement typé*.
- **HTML/CSS** : Pour l'*interface utilisateur*.
- **Javascript/jQuery** : Pour quelques scripts *côté client*, permettant notamment l'appel de *scripts* *côté serveur* en *AJAX*.
- **MySQL 5.7** : *Base de données* comprenant son propre *langage de requête* basé sur le langage *SQL* et l'étendant. La version 5.7 est la dernière version *open source* du logiciel avant son rachat par la société *Oracle*.

D'autres ont été choisies par l'équipe:

- **PHPUnit** : *Framework* de *tests automatisés*.
- **XLIFF** : Format de *fichiers* basé sur la *syntaxe* du format *XML*. C'est un format très couramment utilisé par les *TMS* (Translation Management System).
- **Helpscout** : *Application* web de gestion des tickets de support, permet à l'équipe de suivre et résoudre les problèmes rencontrés par les utilisateurs.

Finalement, les environnement de développement peuvent varier selon les développeurs:

- **Linux** : *Système d'exploitation* *open source*. Il est aujourd'hui l'*OS* très largement majoritaire dans les *serveurs webs*.
- **Apache (HTTP Server)** : *Logiciel* permettant l'interprétation de *requêtes HTTP* ainsi que la génération des *réponses*.
- **PHP Storm** : *IDE* dédié au développement en *PHP*.