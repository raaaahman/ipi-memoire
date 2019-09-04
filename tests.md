# Ecrire des tests automatisés pour une extension de WordPress

## Configurer les outils

Dans le milieu du développement d'applications, la mise en place de *suites de tests automatisés* est une pratique courante. Cela consiste à créer un programme (on parle de *code de test*) qui va s'assurer que l'application en développement (le *code de production*) se comporte de la manière attendue, et ce, durant tout le processus de développement. Pour cela on utilise un jeu de **données connues** en entrée censé produire des **valeurs attendues** en sortie.

![WordPress contraint les versions des outils utilisés](ipi-memoire-images/tests-005_tools.png)

*WordPress* n'échappe pas à cette règle, et son *coeur* et soumis à une batterie de test automatisés. Pour ces tests, il utilise le *framework PHPUnit*, qui est un outil populaire. Comme *WordPress* assure la compatibilité avec PHP 5.6 comme version minimum (la dernière étant la 7.3), cela implique d'utiliser la version 5 de PHPUnit (la dernière étant la 8).

*Polylang* utilise ce même environnement de tests automatisés, ce qui permet d'utiliser la suite mise en place par *WordPress* comme base pour ses propres *tests automatisés*.

## Inventaire des parties à tester

Chaque test va se concentrer sur une *méthode* particulière. Ces méthodes en appellent d'autres, qui seront alors testées au passage. Egalement, on désire se laisser la possibilité de modifier des fonctionnements internes au module par la suite, ce qui pourrait amener à réécrire des tests qui seraient trop spécifiques au code actuel. On ne testera donc pas toutes ces méthodes individuellement, mais en **intégration**.

![Les tests se concentrent chacun sur une méthode de notre classe](ipi-memoire-images/tests-010_overview.png)

### Méthodes et propriétés statiques

La fonction `register_option()` sert à instancier la classe qui reste stockée dans la *variable statique* `$instance` (cf. le chapitre *Refactoring d'un module existant - Créer un point d'entrée unique* de ce document). Elle va poser des problèmes pour les tests, car on ne désire pas utiliser cette *instance partagée*, mais en créer une nouvelle dans chaque test, **indépendamment** des test précédents. 

![Les méthodes et variables statiques seront difficiles à tester](ipi-memoire-images/tests-020_static.png)

De par ailleurs, cette méthode n'est que l'application d'un *design pattern* très courant, et ne s'occupe pas d'exécuter des *règles métier*. Pour ces raisons, elle ne sera pas testée.


## Maîtriser l'environnement des tests

### Lister les dépendances

Notre *système sous tests* possède des **dépendances** envers d'autres classes de *WordPress* ou de *Polylang*, or le but est de maîtriser la totalité des *conditions* dans lequel notre test est exécuté.  L'utilisation d'un *diagramme de classes UML* est très pratique pour lister ces dépendances.

![Les dépendances de notre SUT](ipi-memoire-images/tests-030_dependencies.png)

Ces dépendances introduisent deux facteurs qui peuvent perturber un *test automatisé* :

La dépendance à un **état global** : si un test se repose sur des *variables* ou *objets*, ceux-ci doivent être assignées / instanciés à l'intérieur même du test, et être supprimés dès qu'il est terminé. Dans le cas contraire, si un test venait à utiliser une *variable* ou un *objet* manipulé par le test précédent, ses résultats pourrait varier en fonctions des résultats obtenus par ce dernier. 

La non-connaissance du **contexte d'exécution** : lorsqu'un test automatisé échoue, on veut être certain que c'est parce qu'il ne satisfait pas les attentes écrites dans le test même, afin de faciliter la correction des erreurs du *code de production*. Toutes les inconnues sont donc à proscrire, car si l'on ne s'en occupe pas durant la rédaction du test, elles pourraient rendre les raisons de l'échec ou de la réussite du test confuses lors de son exécution.

### Créer des doublures

On sait que notre méthode va *déléguer* la résolution de l'action à une *instance* de la classe `PLL_Bulk_Translate_Option` (cf. le chapitre *Refactoring d'un module existant - Encapsuler du code réutilisable* de ce document). On ne veut pas exécuter cette action, et cette classe n'est pas notre *système sous tests* (SUT pour "System Under Tests"), nous allons donc en créer une **doublure** : une *instance* de substitution qui n'exécutera pas le code en question.

	$bulk_translate_option = $this->getMockBuilder( PLL_Bulk_Translate_Option::class )
		->setConstructorArgs( array(
			'copy',
			'copy posts',
			null,
		))
		->setMethods( [ 'do_bulk_action' ] )
		->getMock();

*PHPUnit* dispose de *méthodes utilitaires* pour créer ces *doublures* :

- `getMockBuilder()` Crée une nouvelle *doublure* pour la classe `PLL_Bulk_Translate_Option`
- `setConstructorArgs()` : Passe des paramètres au constructeur de la classe doublée
- `setMethods()` : Définit un ensemble de *méthodes* de la classe originale qui seront remplacées par des *méthodes vides* (elles seront appelées mais n'exécuteront pas de code). 
- `getMock()` : Retourne la doublure ainsi instanciée.

### Placer un observateur

Le code précédent est suffisant pour avoir une doublure qui satisfait les nécessité de notre *SUT*. Mais on veut en plus pouvoir l'utiliser pour *écouter* les appels à la méthode `PLL_Bulk_Translate_Option::do_bulk_action()`.

![Notre double nous permet de vérifier le déroulement de nos tests](ipi-memoire-images/tests-050_observer.png)

Cette doublure va pouvoir nous servir à valider ou invalider le test. Si sa méthode `do_bulk_action()` est exécutée, cela signifie que la méthode `PLL_Bulk_Translate::handle_bulk_action()` a passé toutes les vérifications de conditions et s'est exécutée jusqu'au bout. Sinon, cela signifiera qu'une des conditions internes à `PLL_Bulk_Translate::handle_bulk_action()` a échoué et que l'exécution s'est arrêtée (cela peut être notre intention dans certains *scénarios*).

	$bulk_translate_option->expects( $this->once() )
			->method( 'do_bulk_action' );

- `expects()` : programme *PHPUnit* pour écouter un événement
- `once()` : l'événement est sensé être exécuté une et une seule fois
- `method( 'do_bulk_action' )` : cet événement est l'exécution de la méthode `do_bulk_action` de notre instance `$bulk_translate_option`

## Contourner les obstacles

### Doubler une classe finale

Le mécanisme de *doublures* de *PHPunit* fonctionne sur le principe de l'*héritage*: une doublure est une *classe enfant* de la classe utilisée en production. Il est donc impossible de créer une doublure de la classe *WP_Screen*, qui est définie comme *finale*, ce qui veut dire qu'elle ne peut pas avoir de *sous-classe* dans le langage PHP.

![Utilisation de la réflexion pour contourner la classe finale](ipi-memoire-images/tests-080_final-mock.png)

On utilise alors le mécanisme de *réflexion* de *PHP* pour créer une copie de la classe `WP_Screen`. Présenter ce mécanisme de réflexion n'est pas l'objectif ici, d'autant plus que je ne le connais que superficiellement. Cela reste suffisant pour savoir que cette *réflexion* est cette fois *héritable*, et donc disponible pour une *doublure*.

	class Wp_Screen_Mock {
		private $screen;
		
		public function __construct() {
			$this->screen = new ReflectionClass( WP_Screen::class );
		}
	
		public function __call( $method_name, $args ) {
			$screen = $this->screen->newInstance( $args );
			return $screen->$method_name();
		}
	}

On définit une nouvelle classe qui agit comme un *contenant* de cette réflexion. Une nouvelle *réflexion* de la classe `WP_Screen` est assignée à l'*instanciation* de cette classe. Puis, lors de l'appel d'une méthode, une nouvelle *instance* de la *classe réfléchie* est construite avec les *paramètres* passés à cette méthode. L'exécution de la fonction est *déléguée* à cette nouvelle instance. Si la *classe interne* n'est pas héritable, la classe qui la contient en revanche l'est, et on se servira de celle-ci dans nos tests.

### Substituer une propriété privée

Puisque l'on a défini la propriété `$current_screen` de notre classe comme *privée*, on ne peut pas accéder à celle-ci depuis l'extérieur. Le problème est que l'on veut pouvoir contrôler la *valeur* que prend cette propriété lors des tests, pour éviter le problème d'*inconnues* exposé plus tôt. On peut remédier à cela en utilisant à nouveau la *réflexion*.

	$bulk_reflection = new ReflectionClass( PLL_Bulk_Translate::class );
	$bulk_screen_property = $bulk_reflection->getProperty( 'current_screen' );
	$bulk_screen_property->setAccessible( true );
	$bulk_screen_property->setValue( $bulk_translate, $screen );

1. On crée une *réflexion* de la classe à tester.
2. On récupère la *propriété privée* `$current_screen` depuis cette *réflexion*.
3. On la rend *publique* dans la *réflexion*.
4. On injecte notre *doublure* `$screen` en substitution de cette propriété.

### Substituer l'API de WordPress

Notre code se repose sur l'*API de WordPress* pour vérifier les droits des utilisateurs. Cela pose un nouveau problème pour *contrôler l'environnement* des tests. Mais on peut profiter d'une particularité de *WordPress* pour substituer son API : toutes les *fonctions* du fichier `pluggable.php` ne sont déclarées que si aucune fonction de même nom ne préexiste.

![Vérification des droits utilisateur via l'API de WordPress](ipi-memoire-images/tests-100_wordpress-api.png)

Dans le fichier `bootstrap.php` qui permet d'initialiser notre suite de tests, on utilise le système de *hooks* de *WordPress* pour charger notre fichier contenant les fonctions sur l'*événement* `'mu_pluggins_loaded'`. Cela nous permet d'avoir accès à la quasi-intégralité des fonctions de *WordPress*, mais de remplacer celles du fichier `pluggable.php`, qui sera chargé juste après (et après tous les plugins).

	function _manually_load_plugin() {
		require_once dirname( __FILE__ ) . '/pluggable-functions.php';
	}
	tests_add_filter( 'muplugins_loaded', '_manually_load_plugin' );

Dans notre fichier de substitution `pluggable-functions.php`, on réécrit les fonctions qui nous intéressent. Chaque fonction renvoie à une *méthode* d'une nouvelle classe, `Pluggable_Functions`.

	function wp_get_current_user() {
		return Pluggable_Functions_Container::get_instance()->get_current_user();
	}

Parce que cette classe n'est pas encore instanciée au moment de la *déclaration*, on dirige cet appel vers une *méthode statique* de la classe `Pluggable_Functions_Container`. C'est au moment de l'*appel* de cette fonction, que l'on récupère l'*instance courante* de la classe `Pluggable_Functions`. C'est alors la méthode de l'*instance* qui est exécutée.

![Nos classes s'intercalent entre nos tests et l'API de WordPress](ipi-memoire-images/tests-120_pluggable.png)

Lorsqu'une *instance* de `Pluggable_Functions` est crée, cette *instance* est enregistrée dans la *propriété statique* `$instance` de `Pluggable_Functions_Container`, remplaçant l'instance précédente.
 
	class Pluggable_Functions {
	
		public function __construct() {
			Pluggable_Functions_Container::set_instance( $this );
		}
		
		// ...
	}

La classe `Pluggable_Functions` ne fait que ré-implémenter l'*API* de *WordPress*. Mais parce que c'est une **classe**, on peut en créer une *doublure*, contrairement à des *fonctions globales*. Il suffit alors de créer une nouvelle *doublure* pour que celle-ci deviennent l'*instance courante*, et que nous puissions contrôler son comportement.

	$pluggable_functions = $this->getMockBuilder( Pluggable_Functions::class )
			->setMethods( [ 'get_current_user' ] )
			->getMock();
		$pluggable_functions->method( 'get_current_user' )
			->willReturn( $current_user );

## Paramétrage du test

Une fois notre test complet, on veut pouvoir l'utiliser sur plusieurs *scénarios*. Dans ces scénarios, nous allons faire varier les *paramètres* suivants:

- `'action'`    : Le nom de l'*action groupée*[^1] que l'utilisateur essaie d'exécuter.
- `'post'`      : Les identifiants des *contenus*[^1] sélectionnés pour la traduction.
- `'post_type'` : Le *type de contenu*[^1] que désire traduire l'utilisateur.
- `'pll-translate-lang'` : Le *code des langages*[^1] dans lequel l'utilisateur souhaite traduire les contenus.
- `'translate'` : Le nom du *mode de traduction*[^1] choisi.
- `'user_capabilities'`  : Liste des *capabilities* ( = droits / permissions ) définies pour l'utilisateur actuel[^1].

[^1]: Les utilisateurs, langages, contenus, comme les actions groupées en question n'ont pas besoin d'exister, puisque l'on stoppe l'exécution de notre fonction aussi tôt que nous avons l'assurance qu'elle se comporte comme prévu.

Il suffit ensuite de passer la liste des paramètres au test, qui va les assigner à la *variable superglobale* `$_REQUEST`, dans laquelle notre *SUT* va aller les récupérer. Mais cela pose le problème précédemment mentionné de dépendance à un *état global* : ainsi un test peut affecter les résultat d'un autre. On doit donc nettoyer cette valeur après chaque test.[^2]

[^2]: Ce problème de dépendance à un état global reflète un souci dans la conception de notre *code de production*. En effet, il paraît préférable en *POO* que chaque classe soit instanciée avec la totalité des paramètres dont elle a besoin, pour éviter les problèmes liés au *couplage fort*. Cependant, pour accélérer le développement, et parce que le code *WordPress* ne considère pas vraiment ce problème non plus, j'ai décidé de faire un peu plus d'efforts dans les tests et de laisser le *code de production* tel quel.

Seulement, *PHPUnit* **interrompt l'exécution** du code d'un test qui échoue, ce qui nous empêche d'effectuer ce nettoyage dans le code même du test. On utilise alors la méthode `tearDown()` de *PHPUnit*, qui est exécutée systématiquement après chaque test, quel que soit le résultat de ce test.

	class PLL_Bulk_Translate_Test extends PLL_UnitTestCase {
	
		function tearDown() {
			foreach ( array( $_POST, $_GET, $_REQUEST ) as &$superglobal ) {
				if ( array_key_exists('action', $superglobal ) ) {
					unset( $superglobal['action'] );
				}
				
				// Ainsi de suite pour chaque valeur que l'on aura pu assigner.
			}
			parent::tearDown();
		}
	}

La dernière étape du paramétrage de notre test est de passer à notre fonction de test la variable `$times`. Cette variable combien de fois la méthode `PLL_Bulk_Trasnlate::option()` est supposée être appelée durant l'exécution d'un test (soit 1, soit 0). L'avantage  est d'utiliser le même test pour des scénarios supposés faire échouer notre *SUT*, d'autres sensés aboutir. L'avantage d'utiliser le même code de test pour ces deux types de scénarios est que l'on s'assure de la *pertinence du test*. Cela nous permet d'éviter deux écueils :

Ce ne sont pas les *paramètres* auxquels on s'attend qui font échouer le SUT. En effet, le *code du test* pourrait comporter une erreur mais toutefois retourner le résultat attendu dans le *scénario précis* que l'on a testé. Cette possibilité d'erreur tend à réduire lorsque l'on augmente le nombre de scénarios joués avec un même test.

Le *SUT* aboutit mais certains paramètres sont absents : notre test est incomplet ou repose sur un *état global*. Ce test peut tout de même avoir une utilité dans la *version actuelle du code de production*, mais pourra éventuellement laisser passer des erreurs lors de l'*évolution de l'application*. Or l'un des enjeux des *tests automatisé* est d'éviter la *régression*.

	public function test_handle_bulk_action_with_varying_parameters( $query_args, $times ) {
		
		// Paramétrage de l'environnement
		
		$bulk_translate_option->expects( $this->exactly( $times ) )
			->method( 'do_bulk_action' );
			
		$bulk_translate->handle_bulk_action( 'example.com/edit.php' , $query_args['action'], $query_args['post'] );
	}

## En conclusion

L'écriture de *test automatisés* est une expérience que j'espérais acquérir lors du choix de mon entreprise. En effet, travaillant sur un **produit** sensé perdurer dans le temps, il est nécessaire de s'assurer que les nouvelles évolution ne viennent pas *générer de dysfonctionnement* dans les fonctionnalités existantes.

Bien ma collègue Christelle effectue une batterie de tests manuels sur l'intégralité du produit avant la sortie de chaque nouvelle version, ce *processus automatisé* nous offre deux avantages:

- Ces tests sont disponibles **lors du développement**. Ils sont très rapides à faire tourner ( en dessous d'une minute pour tester l'intégralité de *Polylang*) et on peut par conséquent les vérifier régulièrement. Cela nous permet de nous assurer que chaque nouvel ajout ne provoque pas de *régression*.
- Ces tests nous aident à **identifier l'origine** des problèmes rencontrés. Si la partie du code qu'ils testent est suffisamment restreinte, les recherches de la cause d'une erreur sont facilitées (d'où mon insistance durant ce chapitre sur le contrôle de l'environnement de chaque test).

En théorie, l'écriture d'un test devrait avoir une fonction de **documentation** du code de production. En effet, indiquer clairement les *paramètres* comme les *résultats attendus* pour chaque sous-partie de l'application, pourrait être une forme de *communication non-verbale* entre les développeurs.

En pratique, je trouve que malheureusement la *complexité* de certains de ces tests, avec toutes leurs *doublures* et *paramètres* puissent être confus pour les développeurs n'ayant pas travaillé directement sur le *code de production* en question. Cette confusion pourrait éventuellement émaner d'un problème dans la conception de l'application, dont les sous-parties pourraient être trop imbriquées. C'est un questionnement que je pense devoir nourrir au gré de mes expériences futures. 
