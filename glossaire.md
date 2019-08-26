# Glossaire des termes utilisés

## Informatique Générale

- **Adresse IP** : Série de chiffres permettant d'identifier une machine ou un *sous-réseau* au sein d'un *réseau* informatique.
- **Adresse web** : Pour accéder aux pages hébergées au sein du *World Wide Web*, il est usuel de renseigner dans la *requête* l'*URL* de la *ressource* demandée. Cette *URL* comprend un *nom de domaine*, un éventuel *chemin*, ainsi que d'éventuels *paramètres*.
- **Application / logiciel** : Toute forme de *programme* informatique fournissant une ou plusieurs *fonctionnalités* à un *utilisateur*. Ainsi, *Polylang*, comme ces modules, peuvent tous être considérés comme des applications.
- **Base de données** : *Logiciel* gérant la *persistance* ainsi que l'accès à un ensemble de *données*.
- **CMS (Content Management System)** : *Application* permettant la gestion de contenus en ligne sans besoin de programmer. Cela ouvre la possibilité aux personnes non formées à la programmation d'agir en tant qu'administrateurs de *sites internet*.
- **Contenu** : Un ensemble de *données* formant une unité qui leur donne un sens au yeux d'un utilisateur.
- **Distant / à distance** : Se Nécessite un protocole de transfert, comme le *HTTP* dans le cas d'internet.
- **Fichier** : Structure de données stockées sous forme binaires ou textuelles.
- **Hors-ligne** : Sans d'être connecté à une machine *distante*.
- **HTTP (Hyper Text Transfer Protocol)** : Protocole de transfert de données permettant d'accéder à des contenus distants à travers l'internet. La *machine cliente* génère une *requête* qui est dirigée par un *DNS (Domain Name Server)*, puis éventuellement vérifiée par le *serveur* (si l'*adresse* de la requête est valide), qui retourne ensuite une *réponse*.
- **Interface utilisateur (UI)** ou **interface homme/machine (IHM)** : Partie d'un *programme* ou d'un *système informatique* permettant à un *utilisateur* humain de transmettre des *données* et de déclencher des *traitements* par la machine.
- **Local / en local** : Se dit d'une *ressource* existante sur le même système informatique.
- **Localisation** : Processus de créer des versions d'une *application* ou d'un *contenu* dans plusieurs langues différentes.
- **Métadonnées** : Données isolées, secondaires à un *contenu*, et offrant des précisions techniques sur celui-ci, permettant ainsi sa classification auprès d'autres *contenus*. Plusieurs *contenus* peuvent partager les mêmes *métadonnées*.
- **Navigateur (web)** : Application permettant la lecture de *contenus* distants, hébergés sur le *World Wide Web*.
- **Nom de domaine** : *chaînes de texte* permettant la traduction vers une *adresse IP* lors d'une *requête HTTP*.
- **OS** (Operating System / Système d'exploitation) : *Couche logicielle* minimale permettant l'exécution de *programme* informatique sur une machine. 
- **Persistance** : Capacité des données à subsister après l'extinction du programme informatique.
- **Programme** : Série d'*instructions* à réaliser par une machine. Ecrites par un *programmeur* à travers un *langage* permettant à la machine d'interpréter ces *instructions*. 
- **Site (web)** : Une ressource distante, pouvant prendre la forme d'une *application* hébergée sur le *World Wide Web*.
- **Translation Management System (TMS)** : *Application* permettant à un utilisateur de traduire ou de gérer des traductions de *contenus*.


## Développement

- **AJAX** (Asynchronous Javascript And XML) : Procédé permettant d'effectuer une *requête HTTP* sans provoquer le chargement complet de la page web dans le navigateur. La page sera *rafraîchie* lorsque le *navigateur* aura reçu une réponse de la part du *serveur*.
- **Chaînes (de caractères)** : Un type de données informatique composé d'une succession de caractères alphanumérique et de ponctuation. Peuvent être mono-caractères ou contenir des milliers de mots.
- **Code source** : Les *instructions* écrites par un *programmeur* dans un *langage* compréhensible par les humains, avant une potentielle étape de transformation (minification, *compilation*, etc.).
- **Côté client** : Dans une *application* sur le modèle client / serveur, désigne la partie du programme exécutée sur la machine locale de l'utilisateur, en général dans un *navigateur* lorsque l'*application* prend la forme d'un *site web*.
- **Côté serveur** : Partie du programme exécutée sur la machine distante (le *serveur*), généralement en réponse à une *requête HTTP*.
- **Documentation** : Dans l'ingénierie logicielle, support textuel permettant d'expliciter le fonctionnement du *programme informatique* comme les techniques mises en place.
- **Feature / fonctionnalité** : Partie d'une *application* permettant de répondre à un besoin défini. Les features sont en théorie indépendantes les unes des autres.
- **Gestion des versions** : Procédé permettant de fiabiliser la création de programme informatique en organisant la persistance des incréments du programme.
- **IDE** (Integrated Development Environment) : *Logiciel* destiné à l'édition du *code* par les programmeurs.
- **Interface** :  construction au sein d'un *langage de programmation* permettant de définir une *implémentation* déléguée à une *classe*.
- **Instruction** : Opération qu'une machine devra exécuter lors d'un *programme*, agissant généralement sur des *données*, en y accédant, les modifiant et/ou en les enregistrant.
- **Langage** (de programmation) : Règles d'écriture permettant à une machine de traduire et d'exécuter des *instructions* écrites par un *programmeur* à son intention.
- **Langage interprété** : Lors de l'*exécution* d'un programme écrit dans un *langage interprété*, le code écrit par le programmeur est transformé en *instructions* compréhensibles par la machine au travers d'un *interpréteur*. Cet *interpréteur* est un programme qui doit donc être installé sur la machine chargée de l'exécution du programme.
- **Open Source** : Désigne les *logiciels* dont le *code source* est partagé et dont les licences permettent la modification ainsi que la distribution par des tiers.
- **Règle métier** : Dans un *programme*, il s'agit de la partie responsable des traitements effectués sur les *données*. Exemple: le calcul de la *TVA* dans un logiciel de comptabilité.
- **Stateless** : Un langage *sans état/stateless* ne conserve pas de données entre deux *exécutions* d'un même *programme*
- **Syntaxe** : L'ensemble des mot-clés spécifiques à un *langage de programmation*.
- **Typage dynamique** : Capacité d'un langage informatique d'évaluer, lors de l'*exécution*, le type de données stockées dans la *mémoire temporaire*.

## Programmation Orientée Objet (POO)

La *Programmation Orientée Objet* est un *paradigme* de programmation popularisé par le langage *Java*. Son objectif est de rendre le code plus modulable en l'agençant dans des *objets* qui surveillent leur propre *état* et sont chacun responsables de *fonctionnalités* spécifiques.

- **Classe** : Définition de *propriétés* et de *méthodes*, groupée thématiquement, permettant la réutilisation de leur *code* à travers les *instances* de cette classe.
- **Couplage** : Un *couplage fort* désigne des *classes* dont l'inter-relation ne permet pas à l'une de se passer de l'autre. A l'inverse d'un *couplage faible*.
- **Design Pattern** (Patron de Conception) : Désigne une organisation du code selon certaines conventions, permettant d'améliorer la maintenabilité du code.
- **Héritage** : Permet de créer une *classe enfant* (ou *sous-classe*) à partir d'une *classe parent* (ou *super-classe*) existante. La *classe enfant* peut donc utiliser le code contenu dans son *parent*.
- **Instance / Objet** : Pour exécuter le code défini dans une *classe*, il est généralement nécessaire d'*instancier* cette classe. Chaque *instance* d'une même classe, bien que se comportant de façon similaire, n'est pas affectée par les modifications apportées aux autres instances.
- **Référence** : Permet de retrouver une *instance* existante d'une *classe* gardée en *mémoire*.
- **UML** (Unified Modeling Language) : Conventions de schématisation du code afin de communiquer aisément entre développeurs.


## Agilité & framework SCRUM

**Agilité / méthodes agiles** : Principes de gestion de projet mettant en avant un processus de conception itératif, permettant d'adapter la production à un besoin utilisateur changeant, sur lequel l'équipe de développement a des retours fréquent par la livraison d'incréments pouvant être testé par ledit utilisateur.
**SCRUM** : Cadre de travail inspiré de l'*agilité*, définissant un certains nombre de pratiques à mettre en place au sein de l'équipe du projet. La liste suivante en présente une partie non exhaustive.
- **User story** : Décrit succinctement une *feature* à réaliser pour l'*application*, basé sur un besoin de l'utilisateur.
- **Itération** : Organisation du travail en cycles, impliquant une régularité des événements de début et fin de cycle (notamment les réunions), ainsi que la production d'*incréments*
- **Sprint** : *Itération* de développement, généralement comprise entre 1 semaine et 1 mois de travail. livrables à un client.
- **Incrément** : Version non définitive d'une *application* en cours de production.
- **Daily meeting**: Réunion quotidienne de l'équipe de développement permettant de recueillir l'humeur générale et de partager les avancées ou les blocages du quotidien
- **Pair programming** : Deux développeurs travaillent ensemble sur un même ordinateur, partageant leurs réflexions à chaud. Il est recommandé que la personne ayant la main sur le clavier change au cours de la session.


