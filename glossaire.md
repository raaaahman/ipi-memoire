# Glossaire

## Informatique Générale

- **Application / logiciel** : Toute forme de *programme* informatique fournissant une ou plusieurs *fonctionnalités* à un *utilisateur*. Ainsi, *Polylang*, comme ces modules, peuvent tous être considérés comme des applications.
- **Base de données** : *Logiciel* gérant la *persistance* ainsi que l'accès à un ensemble de *données*.
- **Contenu** : Un ensemble de *données* formant une unité qui leur donne un sens au yeux d'un utilisateur.
- **Distant / à distance** : Désigne un *ressource* ou un système physique auquel l'on accède à travers un réseau.
- **Extension** (de fichier) : Dernière partie du nom d'un *fichier*, précédé d'un point, donnant une indication sur son format.
- **Fichier** : Structure de données stockées sous forme binaires ou textuelles.
- **Format** ( de fichier) : Convention d'encodage d'un fichier permettant à différents programmes d'en interpréter le contenu.
- **Interface utilisateur (UI)** ou **interface homme/machine (IHM)** : Partie d'un *programme* ou d'un *système informatique* permettant à un *utilisateur* humain de transmettre des *données* et de déclencher des *traitements* par la machine.
- **JSON** (JavaScript Object Notation) : Langage de description de document *structuré* imitant la syntaxe des *objets Javascript*. Très utiliser dans les *applications web* pour la communication *client / serveur*.
- **Local / en local** : Se dit d'une *ressource* existante dans le système informatique que l'on manipule.
- **Localisation** : Processus de créer des versions d'une *application* ou d'un *contenu* dans plusieurs langues différentes.
- **Métadonnées** : Données isolées, secondaires à un *contenu*, et offrant des précisions techniques sur celui-ci, permettant ainsi sa classification auprès d'autres *contenus*. Plusieurs *contenus* peuvent partager les mêmes *métadonnées*.
- **OS** (Operating System / Système d'exploitation) : *Couche logicielle* minimale permettant l'exécution de *programme* informatique sur une machine. 
- **Persistance** : Capacité des données à subsister après l'extinction du programme informatique.
- **Programme** : Série d'*instructions* à réaliser par une machine. Ecrites par un *programmeur* à travers un *langage* permettant à la machine d'interpréter ces *instructions*.
- **Réseau** : Connexion de plusieurs machines permettant leur interaction et les échanges de *données* entre elles.
- **Téléchargement** : Action de récupérer des *fichiers distants* à travers un *réseau*. 
- **Translation Management System (TMS)** : *Application* permettant à un utilisateur de traduire ou de gérer des traductions de *contenus*.
- **XML** (eXtensible Markup Language) : Langage de description de document *structuré* à travers une syntaxe *balisée*. Il est très utilisé comme format de *fichiers d'échange* entre différentes applications.

## Internet

Ce terme désigne un *réseau* mondial permettant la communication entre différentes systèmes informatiques. Il regroupe plusieurs *protocoles de communication* dont les utilisations diffèrent.

- **Adresse IP** : Série de chiffres permettant d'identifier une machine ou un *sous-réseau* au sein d'un *réseau* informatique.
- **Adresse web** : Pour accéder aux pages hébergées au sein du *World Wide Web*, il est usuel de renseigner dans la *requête* l'*URL* de la *ressource* demandée. Cette *URL* comprend un *nom de domaine*, un éventuel *chemin*, ainsi que d'éventuels *paramètres*.
- **CMS (Content Management System)** : *Application* permettant la gestion de contenus en ligne sans besoin de programmer. Cela ouvre la possibilité aux personnes non formées à la programmation d'agir en tant qu'administrateurs de *sites internet*.
- **Hors-ligne** : Sans d'être connecté à une machine *distante*.
- **HTTP (Hyper Text Transfer Protocol)** : Protocole de transfert de données permettant d'accéder à des contenus distants à travers l'internet. On distingue : 
	+ Les **requêtes** : générée par le *client*, puis dirigée par un *DNS (Domain Name Server)* vers le bon *serveur*
	+ Les **réponses** : lorsque le *serveur* reçoit une requête, il peut la traiter par l'un de ses *programmes*, puis retourne une *réponse* au client selon le résultat de ce traitement.
	+ Les **en-têtes** : dans les *réponses* comme dans les *requêtes*, les *en-têtes* ont un rôle descriptif du ces requêtes / réponses. Elles peuvent contenir des informations sur :
		* l'origine de la requête / réponse.
		* le type de données échangées.
	+ Le **corps** : d'une requête ou d'une réponse contient les *données* à échanger.
- **Navigateur (web)** : Application permettant la lecture de *contenus* distants, hébergés sur le *World Wide Web*.
- **Nom de domaine** : *chaînes de texte* permettant la traduction vers une *adresse IP* lors d'une *requête HTTP*.
- **Site (web)** : Une ressource distante, pouvant prendre la forme d'une *application* hébergée sur le *World Wide Web*.
- **(World Wide) Web** : Désigne le réseau mondial de partage d'information crée à partir des pages *web* reliées entre elles par le système de *liens hypertextes*. Inventé par *Tim Berners Lee* dans les années 1990 sur le modèle de l'*ARPANET*.

## Développement

- **AJAX** (Asynchronous Javascript And XML) : Procédé permettant d'effectuer une *requête HTTP* sans provoquer le chargement complet de la page web dans le navigateur. La page sera *rafraîchie* lorsque le *navigateur* aura reçu une réponse de la part du *serveur*.
- **Appel / Appeler** : Permet de récupérer la *valeur* contenue dans une *variable*, ou résultante de l'exécution d'une *fonction*.
- **Assignation / Assigner** : Action de lier une *valeur* à une *variable déclarée*, qui permettra d'accéder à cette *valeur* par la suite.
- **Bloc** (de code) : Regroupement de lignes de code destinées à être exécutées conjointement, mais potentiellement indépendamment du reste du programme.
- **Chaînes (de caractères)** : Un type de données informatique composé d'une succession de caractères alphanumérique et de ponctuation. Peuvent être mono-caractères ou contenir des milliers de mots.
- **Code source** : Les *instructions* écrites par un *programmeur* dans un *langage* compréhensible par les humains, avant une potentielle étape de transformation (minification, *compilation*, etc.).
- **Condition** : Evalue une *expression* et exécute un *bloc de code* selon le résultat de cette évaluation.
- **Côté client** : Dans une *application* sur le modèle client / serveur, désigne la partie du programme exécutée sur la machine locale de l'utilisateur, en général dans un *navigateur web* lorsque l'*application* prend la forme d'un *site web*.
- **Côté serveur** : Partie du programme exécutée sur la machine distante (le *serveur*), généralement en réponse à une *requête HTTP*.
- **Déclaration / Déclarer** : Dans le cas d'une *variable*, il s'agit de lui donner un nom, afin que l'on puisse l'*appeler* par la suite. Dans le cas d'une *fonction*, l'on doit également écrire le *bloc de code* qui sera exécuté lors de son *appel*.
- **Documentation** : Dans l'ingénierie logicielle, support textuel permettant d'expliciter le fonctionnement du *programme informatique* comme les techniques mises en place.
- **Expression** : Portion de code renvoyant une *valeur*, il peut s'agir d'une opération mathématique, d'une comparaison entre deux *variables*, du résultat de l'*appel d'une fonction*, etc.
- **Fonctionnalité** (feature) : Partie d'une *application* permettant de répondre à un besoin défini. Les fonctionnalités sont en théorie indépendantes les unes des autres.
- **Fonction** : *Bloc* de code destiné à être utilisé plusieurs fois à travers le programme, et pouvant retourner une *valeur*. Une fonction peut être :
	+ **nommée** : Comme une *variable*, une fonction doit être *déclarée* avec un *nom*, ainsi que le code qu'elle *exécutera* lors de futurs *appels*.
	+ **anonyme** : Cette *fonction* n'est pas *déclarée* avec un nom permettant de l'identifier dans l'espace mémoire. Elle est donc forcément liée à une autre partie du programme qui se charge de l'*appeler*.
- **Gestion des versions** : Procédé permettant de fiabiliser la création de programme informatique en organisant la persistance des incréments du programme.
- **IDE** (Integrated Development Environment) : *Logiciel* destiné à l'édition du *code* par les programmeurs.
- **Implémenté(e) / Implémentation** : Un *fonctionnalité* a dépassé le stade de la conception et a été réalisée au sein d'un *programme*.
- **Interface** :  construction au sein d'un *langage de programmation* permettant de définir une *implémentation* déléguée à une *classe*.
- **Instruction** : Opération qu'une machine devra exécuter lors d'un *programme*, agissant généralement sur des *données*, en y accédant, les modifiant et/ou en les enregistrant.
- **Langage** (de programmation) : Règles d'écriture permettant à une machine de traduire et d'exécuter des *instructions* écrites par un *programmeur* à son intention.
- **Langage interprété** : Lors de l'*exécution* d'un programme écrit dans un *langage interprété*, le code écrit par le programmeur est transformé en *instructions* compréhensibles par la machine au travers d'un *interpréteur*. Cet *interpréteur* est un programme qui doit donc être installé sur la machine chargée de l'exécution du programme.
- **Open Source** : Désigne les *logiciels* dont le *code source* est partagé et dont les licences permettent la modification ainsi que la distribution par des tiers.
- **Paradigme** (de programmation) : Manière de rédiger des programmes selon des idées et concepts directifs. Différents paradigmes de programmation peuvent produire des *applications* similaires mais dont le code diffère grandement.
- **Paramètres / arguments** : Une ou plusieurs *valeurs* passées à une fonction, afin qu'elle en effectue le traitement.
- **Portée** : Désigne le contexte dans lequel une *variable* ou toute autre *structure de données* est accessible lors de l'exécution d'un programme. On désigne deux types de **portées**:
	+ La portée **globale** : Accessible à toutes les parties du programme.
	+ Les portées **locales** : La *structure de données* n'existe que dans une sous-partie en question. Il peut s'agit d'un module, d'une *fonction*, d'un *bloc conditionnel*,etc.
- **Refactoring** : Action de modifier le *code d'un programme* dans un but organisationnel, sans changer les *fonctionnalités de l'application* en question.
- **Règle métier** : Dans un *programme*, il s'agit de la partie responsable des traitements effectués sur les *données*. Exemple: le calcul de la *TVA* dans un logiciel de comptabilité.
- **Régression** : Au cours du développement, désigne le fait d'introduire une nouvelle *erreur* dans une partie existante d'une *application*.
- **Stateless** : Un langage *sans état (stateless)* ne conserve pas de données entre deux *exécutions* d'un même programme.
- **Structure de données** : Permet de conserver une ou plusieurs *valeurs* (données) en mémoire lors de l'exécution d'un programme.
- **Syntaxe** : L'ensemble des mot-clés spécifiques à un *langage de programmation*.
- **Tableau** : *Type* de *valeur* permettant de stocker et d'accéder à un ensemble d'autres *valeurs* à partir d'une *clé* (ou *index*) les identifiant au sein de ce tableau.
- **Type** : Défini la nature d'une *donnée*, comme par exemple un *entier* ou une *chaîne de caractères*.
- **Typage dynamique** : Capacité d'un langage informatique d'évaluer, lors de l'*exécution*, le *type* de données stockées dans la *mémoire temporaire*.
- **Valeur** : *Donnée* utilisée, voire générée, lors de l'exécution d'un programme.
- **Variable** : *Structure de données* stockant une unique *valeur* qui peut être récupérée, modifiée, réassignée lors de l'exécution d'un programme.

## Bases de Données

- **Entrée**(ou Ligne/Enregistrement) : Un ensemble de *données* intrinsèquement liées.
- **SGBDR** (Système de Gestion de Bases de Données Relationnelles) : *Logiciel* permettant le stockage ordonné et la lecture de *données* pouvant exprimer des corrélations.
- **SQl** : Langage pour effectuer des *requêtes* envers des *SGBDR* afin d'y lire/écrire des *données*. Ce langage a été étendu dans plusieurs *sur-ensembles*, notamment *MySQL* et *MariaDB*.
- **Table**: Dans les *Systèmes de Gestion de Base de Données Relationnelles*, les tables sont les répertoires contenant une liste d'*entrées* (ou lignes), contenant des données structurées de manière similaire.

## Pages Web et Navigateurs

- **DOM** (Document Object Model) : Représentation abstraite de la *page web* dans le *navigateur web*, qui peut être modifiée à la volée, notamment par du code javascript via la *DOM API*.
- **Elément** (HTML) : Le composant de base d'un *document HTML*. Les éléments sont des contenants pour les *données* qui seront affichées à l'utilisateur. Les éléments peuvent contenir d'autres éléments, on parle alors d'*éléments parents* et d'*éléments enfants*.
	+ **Attributs** : Définissent plus précisément le comportement d'un *élément* au sein d'un *document HTML*. Par exemple, l'attribut `href` permet de définir une *URL* de destination pour un lien hypertexte.
- **Evénement** : Se produit lors d'une certaine action du *navigateur web*, par exemple le chargement d'une page HTML, ou une entrée de son *interface utilisateur* (comme un clic de la souris).
- **Formulaire** (HTML) : Cet ensemble d'*éléments* a pour fonction de permettre à l'utilisateur d'entrer ou de sélectionner des *valeurs* à destination d'être envoyées vers un serveur (ou utilisée dans un *script côté client*).
- **HTML** (Hyper Text Markup Language) : permet la rédaction de documents *structurés* à travers une syntaxe *balisée*. Il est construit à destination du *web* en implémentant le système de *liens hypertextes*, et est *interprété* par tous les *navigateurs web* afin de présenter ces pages web aux utilisateurs.

## Programmation Orientée Objet (POO)

La *Programmation Orientée Objet* est un *paradigme* de programmation popularisé par le langage *Java*. Son objectif est de rendre le code plus modulable en l'agençant dans des *objets* qui surveillent leur propre *état* et sont chacun responsables de *fonctionnalités* spécifiques.

- **Classe** : Définition de *propriétés* et de *méthodes*, groupée thématiquement, permettant la réutilisation de leur *code* à travers les *instances* de cette classe.
- **Composition** : Consiste à inclure au sein d'une *classe*, une ou plusieurs *propriétés* étant elles-mêmes des *instances* d'autres *classes*.
- **Couplé(e) / Couplage** : Un *couplage fort* désigne des *classes* dont l'inter-relation ne permet pas à l'une de se passer de l'autre. A l'inverse d'un *couplage faible*.
- **Design Pattern** (Patron de Conception) : Désigne une organisation du code selon certaines conventions, permettant d'améliorer la maintenabilité du code.
- **Héritage** : Mécanisme permettant de créer une *classe enfant* (ou *sous-classe*) à partir d'une *classe parent* (ou *super-classe*) existante. La *classe enfant* peut donc utiliser le code contenu dans son *parent*.
- **Instance / Objet** : Pour exécuter le code défini dans une *classe*, il est généralement nécessaire d'*instancier* cette classe. Chaque *instance* d'une même classe, bien que se comportant de façon similaire, n'est pas affectée par les modifications apportées aux autres instances.
- **Méthode** : Similaire à une *fonction* définie à l'intérieur d'une *classe*. Le code d'une méthode peut être exécuté de manière *statique* au niveau de la *classe*, ou de manière *non statique* depuis n'importe quelle *instance* de cette classe.
- **Propriété / Attribut** : Permet de stocker et des *valeurs* à l'intérieur d'une *classe* ou de ses *instances*, à la manière d'une *variable*.
- **Référence** : Permet de retrouver une *instance* existante d'une *classe* gardée en *mémoire*.
- **Statique / non statique** : Les *propriétés* et les *méthodes* d'une *classe* peuvent agir soit au niveau de la classe et en affecter l'*état*, elles sont alors dites statiques. Ou elles doivent être exécutées au niveau des *instances* de cette classe, ne pouvant intervenir que sur l'instance à partir de laquelle elles sont invoquées, elles sont alors dites statiques.
- **UML** (Unified Modeling Language) : Conventions de schématisation du code afin de communiquer aisément entre développeurs.

## WordPress

- **Articles** : Les *contenus* principaux de *WordPress*, composé, entre autres, d'un titre et d'un corps. Ils se différencient des *pages* seulement dans leur utilisation car les deux sont enregistrés dans une *table* commune de la *base de données*.
- **Catégories** : *Taxonomie* *hiérarchisée* s'appliquant par défaut aux *articles* et aux *pages*.
- **Coeur** (Core ou moteur) : Par opposition aux *extension*, désigne la base du *CMS* qui peut être dérivée dans les divers *sites webs* construits avec.
- **(Type de) Contenu Personnalisé** : Il est possible pour le développeur (ou l'utilisateur muni d'une extension), de définir de nouveaux types de contenus, représentant à sa convenance, des événements, des lieux, des projets, ou tout autre concept ayant du sens au sein de son *application*. Ces contenus se comporteront dans *WordPress* à la manière des *articles* ou *pages*, ou d'une manière nouvelle selon les paramètres qui leur seront assignés.
- **Extension** (plugin): Le coeur du *CMS* peut être enrichi de fonctionnalités grâce au *téléchargement* et à l'activation d'extensions. Celles-ci peuvent être gratuites et mises à disposition sur le réseau public des extensions, ou payantes et vendues selon les modalités que décide leur créateur.
- **Publier / Publié** : Les nouveaux *contenus* enregistrés dans la base de données de *WordPress* ne sont pas automatiquement accessible en ligne aux visiteur. Pour cela, l'utilisateur doit changer le *statut* de ce contenu en le publiant.
- **Statut** : Chaque *contenu* a un statut qui défini sa visibilité de par les utilisateurs, les principaux sont:
	+ **publish** (Publié) : Le contenu est disponible en ligne pour tous les visiteurs
	+ **draft** (Brouillon) : Le contenu est en cours d'édition. Seuls les utilisateurs disposant des capacités d'édition de ce type de contenu peuvent y accéder
	+ **pending** (En attente) : Le contenu a été écrit et un utilisateur ayant la capacité de publication de ce type de contenu doit le publier.
	+ **future** : Une date de publication a été définie pour ce contenu, qui sera alors automatiquement mis en ligne. 
- **Taxonomies** : Chaque *contenu* peut avoir une liste de taxonomies qui servent à le classifier avec des contenus présentant des similarités. Ces *taxonomies* peuvent être :
	+ **hiérarchisées** : Chaque taxonomie peut être rattachée à une taxonomie *parente*.
- **Terme** : Dans une *taxonomie* donnée (exemple: les *catégories*), plusieurs *termes* vont définir le sujet des *contenu* auxquels ils sont appliqués (exemple: Un *article* avec la *catégorie* : "retour d'expérience").

## Agilité & framework SCRUM

**Agilité / méthodes agiles** : Principes de gestion de projet mettant en avant un processus de conception itératif, permettant d'adapter la production à un besoin utilisateur changeant, sur lequel l'équipe de développement a des retours fréquent par la livraison d'incréments pouvant être testé par ledit utilisateur.
**SCRUM** : Cadre de travail inspiré de l'*agilité*, définissant un certains nombre de pratiques à mettre en place au sein de l'équipe du projet. La liste suivante en présente une partie non exhaustive.
- **User story** : Décrit succinctement une *feature* à réaliser pour l'*application*, basé sur un besoin de l'utilisateur.
- **Itération** : Organisation du travail en cycles, impliquant une régularité des événements de début et fin de cycle (notamment les réunions), ainsi que la production d'*incréments*
- **Sprint** : *Itération* de développement, généralement comprise entre 1 semaine et 1 mois de travail. livrables à un client.
- **Incrément** : Version non définitive d'une *application* en cours de production.
- **Daily meeting**: Réunion quotidienne de l'équipe de développement permettant de recueillir l'humeur générale et de partager les avancées ou les blocages du quotidien
- **Pair programming** : Deux développeurs travaillent ensemble sur un même ordinateur, partageant leurs réflexions à chaud. Il est recommandé que la personne ayant la main sur le clavier change au cours de la session.


