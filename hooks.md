# S'interfacer avec WordPress : les hooks

*WordPress* est un outil ouvert, et au cours de son existence il s'est construit une importante communauté de développeurs travaillant à son amélioration et l'ajout de fonctionnalités. Ces nouvelles fonctionnalités peuvent être installées sur les sites propulsés par *WordPress* grâce à un système d'**extensions**.

Ces extensions peuvent être de toutes sortes, comme la gestion de cache, l'amélioration du moteur de recherche interne ou la transformation en plateforme de e-commerce. *Polylang* fait partie des extensions très installées, et s'est construite sur la base des fonctionnalités offertes par *WordPress*.

Pour développer notre projet, nous avons choisi de le réaliser sous forme d'*extension* complémentaire à *Polylang*. Si cette dernière est nécessaire pour faire tourner notre programme, les *codes source* des deux *extensions* seront distincts. Cette façon de procéder est courante dans la communauté *WordPress* et qualifiée sous le terme d'**add-on**.

## Une architecture orientée événement

Pour permettre aux extensions de s'interfacer, *WordPress* offre des points d'accroche : les **hooks**. Ces *hooks* sont présents en plusieurs endroits du programme. Lors de l'exécution, chaque fois que le programme passe par un *hook*, il interrompt le cours de son exécution, vérifie si des actions ont été enregistrées sur ce *hook*, les exécute une par une, puis reprend le cours de son exécution, jusqu'à un prochain *hook* et ainsi de suite...

![Toutes les actions sont exécutées séquentiellement.](ipi-memoire-images/hooks-001_action_flowchart.png)

Ce fonctionnement peut être rapproché de l'*architecture orientée événement*, et d'ailleurs, le terme "hook" sera, dans les chapitres suivants, francisé sous le terme d'"événement".

*WordPress* différencie deux types de *hook* : les **actions** et les **filtres**. Ils partagent les mêmes propriétés :

- `$tag` : Le nom du filtre, exemple: `'plugins_loaded'`
- `$function_to_add` : Une fonction ou une méthode qui sera appelée lorsque l'événement est déclenché.
- `$priority` : La priorité si plusieurs *actions* s'accrochent sur le même événement. Plus elle est basse, plus tôt l'*action* sera exécutée.
- `$accepted_args` : Le nombre d'arguments à passer à la fonction exécutée.

Ce qui les différencie est dans leur utilisation par *WordPress* : un *filtre* doit retourner une valeur alors qu'une *action* effectue un traitement sans rien retourner. 

## Initialisation de l'extension

Par exemple, dans le script [wp-settings.php](https://github.com/WordPress/WordPress/blob/7f7480cb2a4f9befefeec767b6d99df19e901233/wp-settings.php#L391) on trouve l'*action* suivante :

	do_action( 'plugins_loaded' );

Cette *action* est déclenchée après que *WordPress* aie chargé tous les fichiers des *extensions* installées. Cela permet aux auteurs d'extensions de reporter l'exécution de leur code, afin d'attendre d'être sûr que l'intégralité de leur extension est chargée, et ainsi de ne pas provoquer d'erreur fatale. 

On trouve un exemple de filtre dans la classe [WP_List_Table](https://github.com/WordPress/WordPress/blob/77a31e6875ebc31396b70f6bd2682738ab6290b6/wp-admin/includes/class-wp-list-table.php#L454) :

	protected function bulk_actions( $which = '' ) {
		// ...
		$this->_actions = apply_filters( "bulk_actions-{$this->screen->id}", $this->_actions );  			
		// ...
	}

Dans ce code, au moment de l'exécution de la méthode `bulk_actions`, tous les *filtres* enregistrés vont tour à tour, recevoir un tableau de valeur qu'ils vont pouvoir modifier, puis renvoyer. Ce tableau sera ensuite réassigné à la propriété `_actions` de la *classe* `WP_List_Table`.

L'événement `'plugins_loaded'` est déclenché lorsque tous les fichiers *extensions* ont été chargés par *WordPress*.

	add_action( 'plugins_loaded', 'add_bulk_export' );
	/**
	 * Adds 'export' bulk option in Translate bulk action {@see PLL_Bulk_Translate::register_options()}
	 *
	 * @return mixed
	 */
	function add_bulk_export() {
		PLL_Bulk_Translate::register_options(
			'edit',
			array( new PLL_Export_Bulk_Action() )
		);
	}

---

## L'interface d'administration

Accéder au panneau d'administration *WordPress* déclenche l'événement `'current_screen'`. Celui-ci permet de récupérer en paramètre un objet *Wp_Screen* contenant les informations sur la page actuelle: 
  
[developer.wordpress.org/reference/hooks/current_screen/](https://developer.wordpress.org/reference/hooks/current_screen/) :

	do_action( 'current_screen', WP_Screen $current_screen )

---

On enregistre alors une *action* qui sera exécutée seulement lorsque l'on pourra connaître l'écran qui est alors chargé:

	class PLL_Bulk_Translate {
		
		public static function register_options( $screen, $options ) {
			
			// ... 
			
			add_action( 'current_screen', array( self::$instance, 'init' ), 10, 1 );
			
			// ...		
	}

---

Ce qui nous permet de vérifier si notre extension est applicable à cet écran : 

	class PLL_Bulk_Translate {
		public function init( $current_screen ) {
			if ( ! array_key_exists( $current_screen->base, $this->options ) ) {
				return false;
			}
			
			// ...
		}
	}

**Note** : On utilise une erreur silencieuse, car on ne veut pas casser le fonctionnement des pages d'administration n'utilisant pas notre extension.

---

## Les actions sur les listes

A travers le fonctionnement de *PHP*, *WordPress* a créé des *événements* dont le nom est **dynamiquement assigné**. Par exemple, [/wp-admin/includes/class-wp-list-table.php](https://github.com/WordPress/WordPress/blob/77a31e6875ebc31396b70f6bd2682738ab6290b6/wp-admin/includes/class-wp-list-table.php#L454) :

	class WP_List_Table {
		protected function bulk_actions( $which = '' ) {
			// ...
			$this->_actions = apply_filters( "bulk_actions-{$this->screen->id}", $this->_actions );  
			// ...
		}
	}

---

Parce que l'on récupère l'*identifiant* de l'écran par notre *action* attachée à l'*événement* `'current_screen'`, il nous est possible d'attacher un *filtre* à cet *événement dynamique*.

	public function init( $current_screen ) {
		// ...
		
		add_filter( "bulk_actions-{$current_screen->id}", array( $this, 'add_bulk_action' ) );
		
		// ...
	}

--- 

Ainsi, si l'écran d'administration correspond à l'un de ceux auxquels nous voulons attacher une *action groupée*, on l'ajoute dans la liste récupérée en paramètre.

	public function add_bulk_action( $actions ) {
		$actions['pll_translate'] = __( 'Translate', 'polylang-pro' );
		return $actions;
	}

---

C'est alors *WordPress* qui s'occupe de l'afficher à l'utilisateur de la même manière qu'il affiche les autres options. [/wp-admin/includes/wp-list-table.php](https://github.com/WordPress/WordPress/blob/77a31e6875ebc31396b70f6bd2682738ab6290b6/wp-admin/includes/class-wp-list-table.php#L454) :

		class Wp_List_Table {
			protected function bulk_actions( $which = '' ) {
			
			// ...
			
			foreach ( $this->_actions as $name => $title ) {
				$class = 'edit' === $name ? ' class="hide-if-no-js"' : '';
				echo "\t" . '<option value="' . $name . '"' . $class . '>' . $title . "</option>\n";
			}
			
			// ...
		}

--- 

## Ouvrir le code aux autres extensions

Il est possible d'utiliser cette architecture événementielle pour définir nos propres *hooks*, et rendre ainsi nos fonctionnalités extensibles par d'autres développeurs :

[developer.wordpress.org/reference/functions/apply_filters/](https://developer.wordpress.org/reference/functions/apply_filters/) : 

	apply_filters( string $tag, mixed $value );

**Note** : Il faut passer au minimum deux paramètres, le nom du nouvel événement et une valeur qu'il passera en paramètre à chaque fonction appelée par les *filtres*. Tout paramètre supplémentaire sera passé en paramètre à cette fonction également.

---

Puisque *WordPress* laisse la possibilité aux développeurs d'extensions d'enregistrer leurs propres *types de contenus* (voir [Custom Post Types](https://developer.wordpress.org/themes/basics/post-types/#custom-post-types)), on permet aux autres développeurs de décider si leurs *types de contenus* personnalisés pourront ou non être traduit / exportés à travers un *filtre personnalisé* :

	public function init( $current_screen ) {
		 // ...
		if ( 'edit' === $current_screen->base ) {
			
			$post_types = apply_filters( 'pll_bulk_translate_post_types', $this->model->get_translated_post_types() );
			
			if ( ! in_array( $current_screen->post_type, $post_types ) || ( array_key_exists( 'post_status', $_GET ) && 'trash' === $_GET['post_status'] ) ) {
				return false;
			}
		}

---

## Visualiser la chaîne des événements

Les événements déclenchés par *WordPress* sont renseignés dans le codex. Ils sont séparés en une liste d'*actions* : [WordPress Plugin API - Action Reference](https://codex.wordpress.org/Plugin_API/Action_Reference)

Et une liste de *filtres* : [WordPress Plugin API - Filter Reference](https://codex.wordpress.org/Plugin_API/Filter_Reference)

Ces listes sont triées par thème et les *événements* sont classées par ordre de déclenchement.

---

Comme il est possible de rajouter des *événements personnalisés*, et que on ne les utilise pas tous à la fois, il est utile d'avoir une vision plus précise du fonctionnement de notre plugin. Pour ce faire, j'ai choisi de les représenter d'une manière inspirée des *diagramme de séquence UML*:

![Séquence d'ajout d'une action groupée dans le panneau d'adminsitration WordPress](ipi-memoire-images/hooks-002_uml_sequence.png)


