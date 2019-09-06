# S'interfacer avec WordPress : les hooks

*WordPress* est un outil ouvert, et au cours de son existence il s'est construit une importante communauté de développeurs travaillant à son amélioration et l'ajout de fonctionnalités. Ces nouvelles fonctionnalités peuvent être installées sur les sites propulsés par *WordPress* grâce à un système d'**extensions**.

Ces extensions peuvent être de toutes sortes, comme la gestion de cache, l'amélioration du moteur de recherche interne ou la transformation en plateforme de e-commerce. *Polylang* fait partie des extensions très installées, et s'est construite sur la base des fonctionnalités offertes par *WordPress*.

Pour développer notre projet, nous avons choisi de le réaliser sous forme d'*extension* complémentaire à *Polylang*. Si cette dernière est nécessaire pour faire tourner notre programme, les *codes source* des deux extensions sont distincts. Cette façon de procéder est courante dans la communauté *WordPress* et qualifiée sous le terme d'**add-on**.

## Une architecture orientée événement

Pour permettre aux extensions de s'interfacer, *WordPress* offre des points d'accroche : les **hooks**. Ces *hooks* sont présents en plusieurs endroits du programme. Chaque fois que le programme passe par un *hook*, il interrompt le cours de son exécution, vérifie si des actions ont été enregistrées sur ce *hook*, les exécute une par une, puis reprend le cours de son exécution, jusqu'à un prochain *hook* et ainsi de suite...

![Toutes les actions sont exécutées séquentiellement.](ipi-memoire-images/hooks-001_action_flowchart.png)

Ce fonctionnement peut être rapproché de l'*architecture orientée événement*, et d'ailleurs, le terme "hook" sera, dans les chapitres suivants, francisé sous le terme d'"événement".

*WordPress* différencie deux types de *hook* : les **actions** et les **filtres**. Ils partagent les mêmes propriétés :

- `$tag` : Le nom du filtre, exemple: `'plugins_loaded'`
- `$function_to_add` : Une *fonction* ou une *méthode* qui sera appelée lorsque l'événement est déclenché.
- `$priority` : La priorité si plusieurs *actions* s'accrochent sur le même événement. Plus elle est basse, plus tôt l'*action* sera exécutée.
- `$accepted_args` : Le nombre d'arguments à passer à la fonction exécutée.

Ce qui les différencie est dans leur utilisation par *WordPress* : un *filtre* doit retourner une valeur alors qu'une *action* effectue un traitement sans rien retourner. 

## Initialisation de l'extension

A chaque initialisation, *WordPress* cherche dans son sous-dossier `wp-content/plugins` tous les fichiers PHP contenant les *commentaires* conventionnels de description d'extension (nom de l'extension, auteur, dépôt en ligne, etc.), pour charger ces fichiers en tant qu'extensions et en exécuter le code. Une fois fait, il déclenche l'*action* suivante :

[developer.wordpress.org/reference/hooks/plugins_loaded/](https://developer.wordpress.org/reference/hooks/plugins_loaded/)

	do_action( 'plugins_loaded' );

Cette *action* est déclenchée après que l'**intégralité** des fichiers des *extensions* aient été chargés en mémoire. Cela permet aux auteurs d'extensions de reporter l'exécution de leur code, afin d'attendre d'être sûr que l'intégralité de leur extension soit chargée, et ainsi de ne pas provoquer d'erreur fatale. 

Dans notre cas, un fichier nommé `xliff-exporter.php` contient la description de notre extension et nous sert donc de **point d'entrée**. Comme l'on ne peut pas être certain que le fichier contenant notre classe `PLL_Bulk_Translate` ait été chargé avant l'exécution du code contenu dans le fichier `xliff-exporter.php`, on a choisi d'attendre que l'événement *plugins_loaded* soit déclenché pour continuer l'exécution de notre code.

	add_action( 'plugins_loaded', 'add_bulk_export' );
	
	function add_bulk_export() {
		PLL_Bulk_Translate::register_options(
			'edit',
			array( new PLL_Export_Bulk_Action() )
		);
	}

## Agir sur le panneau d'administration

Accéder au panneau d'administration de *WordPress* déclenche l'action `'current_screen'`. Cette action passe une valeur en *paramètre* aux fonctions ou méthodes qui seront appelées.
  
[developer.wordpress.org/reference/hooks/current_screen/](https://developer.wordpress.org/reference/hooks/current_screen/)

	do_action( 'current_screen', WP_Screen $current_screen )

Ce paramètre est une instance de `WP_Screen`, contenant des informations sur l'*écran* (la page) actuel. Celles qui nous intéresse sont les suivantes:

- `$id` : Le nom de de l'écran en question.
- `$base` : Dans certains cas, le nom est composé (exemple: `'edit-post'`). Cette propriété est la racine de ce nom (dans l'exemple précédent: `'base'`).
- `$post_type` : Le nom d'un *type de contenu* associé à l'écran, s'il y en a un. Par exemple `'post'`.

On enregistre alors une *action* sur cet *événement*, qui sera exécutée seulement lorsque l'on pourra récupérer ces informations[^1] :

	class PLL_Bulk_Translate {
		
		public static function register_options( $screen, $options ) {
		
			// ... 
			
			add_action( 'current_screen', array( self::$instance, 'init' ), 10, 1 );
		}
		
	}

[^1]: La syntaxe utilisée ici est un peu particulière. En effet, on ne désire pas déclencher une fonction existante dans l'*espace global*, mais la méthode `init()` d'une *instance* de `PLL_Bulk_Translate`. Cette instance est *référencée* dans la *propriété* `$instance`, qui est *statique*. On appelle donc cette propriété depuis la classe elle-même, à laquelle on accède par le mot-clé `self`, qui contient une *référence* de classe courante (cf. le chapitre *Refactoring d'un module existant - Créer un point d'accroche unique* de ce mémoire).

On peut alors vérifier si notre extension est applicable à l'*écran* actuel, en comparant sa propriété `base` avec le *tableau* contenant les possibles *actions groupées* que l'on a enregistré dans la propriété `$options` de la classe `PLL_Bulk_Translate`[^2] : 

	class PLL_Bulk_Translate {
		public function init( $current_screen ) {
			if ( ! array_key_exists( $current_screen->base, $this->options ) ) {
				return false;
			}
			
			// Ce code est exécuté si nous avons des actions groupées applicables à cet écran
		}
	}

[^2]: On utilise dans ce cas une erreur silencieuse, pour ne pas casser le fonctionnement des pages d'administration n'utilisant pas notre extension.

## Filtrer la liste des actions groupées

Dans le panneau d'administration de *WordPress*, différents *écrans* contiennent une *table* listant un certain *type de contenu* ou les *termes* d'un *taxonomie*.

![La table des articles dans le panneau d'adminstration de WordPress](ipi-memoire-images/hooks-030_list-table.png)

Ces tables sont gérées par des classes *héritant* de la classe [WP_List_Table](https://github.com/WordPress/WordPress/blob/77a31e6875ebc31396b70f6bd2682738ab6290b6/wp-admin/includes/class-wp-list-table.php#L454). Elles ont en commun le code permettant de générer l'affichage HTML de la table. Dans ces *méthodes* gérant l'affichage, on s'intéresse à celles créant la liste déroulante des *actions groupées* sur les éléments d'une table.

[developer.wordpress.org/reference/hooks/bulk_actions-this-screen-id/](https://developer.wordpress.org/reference/hooks/bulk_actions-this-screen-id/)

	apply_filters( "bulk_actions-{$this->screen->id}", string[] $actions )

A travers le fonctionnement de PHP, *WordPress* a créé des événements dont le nom est **dynamiquement assigné**. C'est le cas ci-dessus, où la propriété `$id` de l'objet `WP_Screen` stocké dans notre objet `WP_List_Table` va être utilisée pour écrire le nom du filtre, et ce, au moment de l'**exécution** du code. Ainsi, ce filtre a un nom différent selon la page du panneau d'administration que l'utilisateur est en train d'afficher.

Par conséquent, au moment de l'exécution de la méthode `bulk_actions`, tous les *filtres* enregistrés correspondants au nom généré vont, tour à tour, recevoir un tableau de valeurs qu'ils vont pouvoir modifier, puis renvoyer. Chaque *filtre* recevant le tableau retourné par le filtre précédent. Ce tableau sera ensuite réassigné à la propriété `$_actions` de la classe `WP_List_Table`.

Comme l'on a également récupéré cet objet `WP_Screen` lors de l'*événement* `'current_screen'`, qui précède cet *événement dynamique*, on peut alors utiliser l'*identifiant* de l'écran pour y attacher un *filtre*.

	public function init( $current_screen ) {
		// ...
		
		add_filter( "bulk_actions-{$current_screen->id}", array( $this, 'add_bulk_action' ) );
		
		// ...
	}

Ainsi, si l'écran d'administration correspond à l'un de ceux auxquels nous voulons attacher une *action groupée*, on l'ajoute dans le tableau récupéré en paramètre, puis on le retourne. La *clé* que l'on utilise pour ajouter une entrée au à ce tableau est importante, car c'est elle qui va servir lors de l'envoi de la *requête* pour savoir quelle action groupée l'utilisateur a sélectionné.

	public function add_bulk_action( $actions ) {
		$actions['pll_translate'] = __( 'Translate', 'polylang-pro' );
		return $actions;
	}

On laisse alors *WordPress* s'occuper d'afficher à l'utilisateur cette liste d'actions groupées. C'est toujours la méthode `bulk_actions` de la classe [WP_List_Table](https://github.com/WordPress/WordPress/blob/77a31e6875ebc31396b70f6bd2682738ab6290b6/wp-admin/includes/class-wp-list-table.php#L454) (ou ses classes dérivées) qui contient le code qui génère cet affichage. On voit que ce tableau que l'on a modifié est utilisé au sein d'une boucle pour afficher une à une les actions enregistrées.

		class Wp_List_Table {
			protected function bulk_actions( $which = '' ) {
			
			// ...
			
			foreach ( $this->_actions as $name => $title ) {
				$class = 'edit' === $name ? ' class="hide-if-no-js"' : '';
				echo "\t" . '<option value="' . $name . '"' . $class . '>' . $title . "</option>\n";
			}
			
			// ...
		}

## Ouvrir le code aux autres extensions

Il est possible d'utiliser cette architecture événementielle pour définir nos propres *hooks*, et rendre ainsi nos fonctionnalités extensibles par d'autres développeurs. Il s'agit simplement d'exécuter dans notre propre code les fonctions `apply_filters()` ou `do_action()`, en leur passant des noms d'*événements* personnalisés.[^3] On s'intéresse alors à la création d'un *filtre personnalisé* :

[^3]: Il est tout à fait possible d'utiliser ses fonctions en leur passant des noms d'événements déjà existants dans *WordPress*, ce qui aura pour effet d'exécuter toutes les actions ou les filtres qui y ont été attachés, potentiellement plusieurs fois durant la même exécution de *WordPress*. 

Puisque *WordPress* laisse la possibilité aux développeurs d'extensions d'enregistrer leurs propres *types de contenus* (cf. les [Custom Post Types](https://developer.wordpress.org/themes/basics/post-types/#custom-post-types) dans la documentation officielle de WordPress), on permet aux autres développeurs de décider si leurs *types de contenus* personnalisés pourront ou non être dupliqués / synchronisés / exportés à travers un *filtre personnalisé* :

	public function init( $current_screen ) {
		 // ...
		if ( 'edit' === $current_screen->base ) {
			
			$post_types = apply_filters( 'pll_bulk_translate_post_types', $this->model->get_translated_post_types() );
			
			if ( ! in_array( $current_screen->post_type, $post_types ) || ( array_key_exists( 'post_status', $_GET ) && 'trash' === $_GET['post_status'] ) ) {
				return false;
			}
		}
		// ...
	}

Il faut passer à la fonction `apply_filters()` au minimum deux *paramètres*: le nom du nouvel événement et une valeur qu'il passera en paramètre à chaque fonction appelée par les *filtres*. Ici notre valeur sera le tableau retourné par la fonction `PLL_Model::get_translated_post_types()`, qui permet justement de récupérer la liste des *types de contenu* que l'utilisateur aura paramétrés pour être traductibles dans les options de *Polylang*.

![L'écran des options de Polylang permet de sélectionner les types de contenus personnalisés disponibles à la traduction](ipi-memoire-images/hooks-040_polylang-settings.png)

Cette liste va servir de base, car il est évident que l'on ne désire pas qu'un contenu ne pouvant pas être traduit de manière individuelle puisse être traduit en masse. Mais si le développeur d'une extension tierce décide que le *type de contenu* qu'il a ajouté peut être traductible uniquement de manière individuelle, il peut alors attacher un *filtre* à l'événement `'pll_bulk_translate_post_types'`, récupérer le tableau des types de contenus disponibles pour les traductions groupées, et en retirer celui qui l'intéresse.

## Visualiser la chaîne des événements

Les événements déclenchés par *WordPress* sont renseignés dans le *codex*. Ils sont séparés en une [liste d'actions](https://codex.wordpress.org/Plugin_API/Action_Reference) et une [liste de filtres](https://codex.wordpress.org/Plugin_API/Filter_Reference). Ces listes sont triées par *thème* (pages publiques, panneau d'administration, requête AJAX) et les événements sont classées dans l'*ordre* dans lesquels ils sont déclenchés.

Si cela est pratique pour connaître le fonctionnement de *WordPress*, cela peut devenir difficile à suivre lors du développement d'une extension, pour les raisons suivantes :

- Il est possible d'ajouter des *événements personnalisés*
- On n'utilise pas forcément tous les événements à la fois
- Certains événements seront disponibles pour plusieurs types de requêtes, d'autre non.
- Certains événements ne se déclencheront pas toujours au même moment. C'est rare mais c'est le cas pour les événements concernant les *redirections*, qui peuvent intervenir à plusieurs moment de l'exécution du code à d'une *erreur* dans les entrées du programme / données manipulées ...

Afin d'avoir une vision plus précise du fonctionnement de notre extension, j'ai choisi de représenter les *événements* d'une manière adaptée des **diagramme de séquence UML**[^4]. Voici le schéma complet pour le fonctionnement que j'ai décrit durant ce chapitre.

![Séquence d'ajout d'une action groupée dans le panneau d'adminsitration WordPress](ipi-memoire-images/hooks-002_uml_sequence.png)

Les rectangles tout en haut représentent les *objets* ou les *scripts*. Sous chacun d'entre eux, une ligne en pointillés (appelée *ligne de vie*) représente la durée pour laquelle ils restent dans la mémoire du programme. Les rectangles qui s'y accrochent sont les *fonctions* ou *méthodes* exécutées. Les flèches pleines représentent les *appels* à ces fonctions ou méthodes quant aux flèches en pointillés, elles en représentent les *valeurs de retour*.

Le système d'*événements* est ici simplifié: il aurait fallu, pour chacun d'entre eux, représenter l'appel à la fonction `do_action()` (ou `apply_filters()`) du script `wp-includes/plugin.php` qui appelle lui même la méthode `do_action()` d'un objet `WP_Hook` qui va alors appeler la fonction ou méthode de notre programme... A la place, je me contente de représenter un appel direct du script dans lequel l'*événement* est déclenché vers la fonction (ou méthode) en question. Je rajoute simplement le nom de cet *événement* sur la flèche.

Si la création de schémas de ce genre s'avère demander plus de travail, j'estime qu'elle m'a permis de récupérer le temps investi lors de la correction de mes erreurs. En effet, pouvoir analyser d'un coup d'oeil l'**ordre** de déclenchement des événements, de chargement des scripts et d'*initialisation* des objets m'a permis d'identifier des points de blocage dans mon programme, dont voici un exemple dans une de ses *itérations*.

![Une redirection trop hâtive empêche l'analyse de la requête et la journalisation des erreurs](ipi-memoire-images/hooks-006_redirect-trouble.png)

Dans le diagramme ci-dessus[^5], une *redirection* (circuit orange) pouvait empêcher la méthode `PLL_Bulk_Translate::parse_request()` d'être appelée (circuit bleu). Cette méthode a pour responsabilité d'analyser les potentielles erreurs de la requête, et de les transmettre à la *Settings API* de *WordPress* qui doit les communiquer à l'utilisateur. Lorsque cette redirection est déclenchée (il y a une condition à cela, et ce n'est donc pas toujours le cas), l'utilisateur ne pouvait donc pas être informé de la cause de cette redirection.

[^4]: Utiliser les conventions de l'UML telle quelles n'est pas possible, car l'UML suppose que le code est **strictement** orienté objet, ce qui n'est pas le cas de *WordPress*, ni de *Polylang* par effet de bord. 

[^5]: Ce diagramme est simplifié pour exposer le problème. Afin de d'identifier les causes, il a fallu explorer une plus grande partie du code. Ce schéma complet garde sa valeur pour réfléchir aux futures évolutions du programme, ou résoudre d'autres erreurs!

## En conclusion

L'étude de l'*API des hooks de WordPress* m'a permis de comprendre une manière dont une solution logicielle spécifique peut être encapsulée dans un CMS généraliste comme *WordPress*. C'est un mécanisme qui a du sens pour un outil très utilisé qui bénéficie pleinement d'un *écosystème* riches d'extensions et de développeurs spécialisés. Ce mécanisme permet à cette *communauté* de produire des exécutables profitant pleinement des *fonctionnalités déjà implémentées*.

La formalisation de ce système selon la *norme UML* m'a permis de mettre en place une méthode d'analyse et de communication des fonctionnement actuels et des résultats attendus de l'application en développement. J'estime ainsi avoir mis à profit le temps, de toutes façons nécessaire, à la lecture du code existant, afin de produire un document qui a de la *valeur* pour mon équipe.

Le mode de conception dit *architecture orientée événements* m'intéresse comme sujet de recherches futures. En comprendre les tenants et les aboutissants pourrait m'être bénéfique dans l'intégration de solutions futures au sein du système mis en place par *WordPress*. 