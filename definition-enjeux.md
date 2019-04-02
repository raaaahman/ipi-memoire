# Définition du cadre et des enjeux du projet

Le projet présenté est un module de l'application *Polylang*. Son but est de gérer l'import, comme l'export, de fichiers de traduction.

Les données seront alors transformées de fichiers aux format *XLIFF* ou *PO*, en entrées de base de donnée *MySQL*.

## Une première définition du besoin

La définition originale du besoin a pris la forme d'une réunion d'une journée, regroupant les membres de l'équipe suivants:

- Frédéric DEMARLE, patron de WP-Syntex et chef de projet
- Marianne & Sylvain, les développeurs de l'application
- Christelle DREVON, testeuse en qualité et technicienne de support

Ce projet est à l'initiative de l'entreprise WP Syntex, après l'expression d'un besoin de la part de divers clients, collectée à partir de l'outil de support pour le produit *Polylang*.

Ce besoin est d'utiliser un format de fichier d'échange, afin d'externaliser les traductions depuis l'outil *WordPress* vers un outil de traduction tiers. (exemple: *Matecat*, *Virtaal*, etc.)

## Enjeux du projet

Lors de la présentation, Frédéric DEMARLE, en tant que chef de projet, a exposé l'intérêt du projet pour la société:

La société a reçu au fil du temps, plusieurs proposition de partenariat de la part d'agences de traductions, souhaitant faire communiquer l'application *Polylang* avec leur **Translation Management System** de référence, souvent un outil que lesdites agences ont elles-mêmes développé. La société a également reçu des demandes de clients souhaitant récupérer leur fichiers de traduction afin de les utiliser dans leur outil de traduction local, de manière à pouvoir travailler sur ces traductions hors-ligne.

Frédéric DEMARLE, en tant que développeur principal de l'application *Polylang*, a ensuite passé en revue les difficultés pouvant se présenter lors du déroulement du projet.

Au niveau fonctionnel :
- Les systèmes de traductions facturant la quantité de texte à traduire, il faut veiller à ce que l'utilisateur de l'application *Polylang* n'aie pas à traduire plusieurs fois des **contenus** déjà traduits, sauf demande explicite de sa part.
- Veiller à ce que la mise en forme originelle soit respectée à l'import du nouveau fichiers

Au niveau technique :
- Les **métadonnées** pouvant être associées à plusieurs contenus, elles ne doivent pas être traduites à nouveau à chaque traduction d'un de ces contenus.
- Lors de l'import, des traductions pourraient entrer en conflit avec celles pré-existantes sur le **site** de l'utilisateur, il faudra trouver un moyen de gérer les ambiguités.
- Les imports comme les exports devront prendre en compte la fonctionnalité de synchronisation, des **contenus** comme des **métadonnées**, existante au sein de l'application *Polylang*.
- Le système de traduction des **chaînes de caractère** isolées diffère de celui des **contenus** de par leur modèle de **persistance** (stockées dans des **fichiers** et non en **base de données**).

## Organisation de l'équipe

L'organisation et la communication au sein de l'équipe ont ensuite été abordées. Le défi ne porte ici pas tant sur la taille de l'équipe (quatre personnes) mais sur son éclatement, les membres étant tous en situation de télé-travail.

Ainsi, une partie de la réunion a porté sur le choix des outils de collaboration. En ressortent notamment:

- *Skype* : Logiciel de messagerie instantanée permettant la communication quotidienne de l'équipe.
- *appear.in* : Application web de visio-conférence pour les réunions d'équipe.
- *Git* : Outil de **gestion des versions** du code source.
- *GitHub* : Outil de partage du code source et de la **documentation** technique.
- *Trello* : Sa flexibilité nous permet de l'utiliser à la fois comme tableau de gestion des tâches et comme centre de partage de documentation.

L'équipe a ensuite débattu sur des choix de gestion de projet inspiré de la méthode *SCRUM* ainsi que de la philosophie **agile**:

- La rédaction des spécifications fonctionnelles sous forme d'**user stories**, permet le découpage et la répartition des tâches à réaliser au sein de l'équipe de développement.
- Organisation de cycles de travail sous forme de **sprints** de deux semaines, permettant d'adapter les prochaines **itérations** selon les résultats observés en fin de sprint. Le rythme de deux semaines permet également de s'adapter au planning de formation en alternance des développeurs. Par ce fait, chacun sait ce qu'il devra faire durant l'absence de son collègue, ainsi que ce que ledit collègue pourra avoir produit durant sa propre absence.
- 

## Choix techniques
