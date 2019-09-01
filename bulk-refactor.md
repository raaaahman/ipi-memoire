# Refactoring d'un module existant

Le panneau d'administration de *WordPress* permet aux utilisateurs de réaliser des **actions groupées** sur des contenus enregistrés dans la base de données. Il est par exemple possible de supprimer plusieurs *articles* à la fois, de les *publier* en même temps, de changer leurs *catégories*, etc.

*Polylang* a repris ce mécanisme pour deux de ces fonctionnalités:

- Il permet de traduire simultanément plusieurs *contenus multimédias, articles et contenus personnalisés*. Ces contenus sont alors copiés dans les traductions nouvellement créées.
- Il permet de synchroniser les *articles ou types de contenus personnalisés* entre plusieurs langues. Cela peut servir par exemple à répercuter les changements entre deux langues extrêmement proches. Par exemple, l'Anglais américain et l'Anglais britannique.

![L'action groupée de traduction](../img/bulk-refactor-000_edit-post.png)

Conformément à la philosophie générale de l'extension, la conception de ce module s'est voulue coller au plus près du fonctionnement de *WordPress*, réutilisant en grande partie le code existant. 

Notre nouvelle fonctionnalité consistant à exporter de manière groupée des *contenus* pour la traduction, il est apparu de bon ton de reprendre ce système et de l'étendre plutôt que d'en créer un nouveau.

## Analyse du code hérité

Pour ses développements, *Polylang* adopte le *paradigme* de la *Programmation Orientée Objet*. Le code de l'*extension* est donc subdivisé en différentes *classes* ayant chacune des responsabilités propres.

Pour ce module, une seule classe est responsable de l'ensemble des fonctionnalité. Il s'agit de la classe `PLL_Bulk_Translate`, qui définit les méthodes suivantes :

- `init` : Enregistre les *actions* et les *filtres*
- `add_bulk_action` : Ajoute l'action "Translate" dans la liste des *action groupées*
- `handle_bulk_action` : Vérifie la validité des *paramètres* récupérés, puis invoque la méthode suivante :
- `translate` : Exécute l'*action groupée* pour les *contenus* sélectionnés pour la traduction.
- `display_form` : Affiche le formulaire pour paramétrer l'*action groupée*.
- `admin_notices` : Affiche les notifications pour informer l'utilisateur du résultat de l'*action groupée*

Dans cette classe, c'est la méthode `translate()` qui a la charge d'exécuter les *règles métier*, utilisant des *conditions* pour trouver la règle adaptée à la situation. L'exemple ci-dessous montre le code qui effectue la traduction d'un contenu de type *article* (post).

	if ( ! empty( $data['pll-translate-lang'] ) ) {
		// Posts
		if ( ! empty( $data['post'] ) ) {
			$post_ids = array_map( 'intval', (array) $data['post'] );
			if ( ! empty( $post_ids ) ) {
				foreach ( $post_ids as $post_id ) {
					foreach ( $data['pll-translate-lang'] as $lang ) {
						if ( 'sync' !== $data['translate'] ) {
							$this->sync_post->sync_model->save_group( $post_id, array() );
						}
						if ( ! $this->model->post->get_translation( $post_id, $lang ) ) {
							$this->sync_post->sync_model->copy_post( $post_id, $lang, 'sync' === $data['translate'] );
							$done++;
						} else {
							$missed++;
						}
					}
				}
			}
		}
	}

On observe, au sein de la boucle qui itère sur les identifiants d'articles la condition `if ( 'sync' !== $data['translate'] )` vérifiant si le choix de l'action de traduction groupée par l'utilisateur correspond à la synchronisation d'un article ou à sa duplication.

Egalement, on trouve plus loin un bloc similaire pour la duplication des contenus de type *media*. Leur principale différence étant le tableau dans lequel la fonction va chercher les identifiants des *contenus* à traduire.

	// Medias
	if ( ! empty( $data['media'] ) ) {
		$post_ids = array_map( 'intval', (array) $data['media'] );
		if ( ! empty( $post_ids ) ) {
			foreach ( $post_ids as $post_id ) {
				foreach ( $data['pll-translate-lang'] as $lang ) {
					if ( ! $this->model->post->get_translation( $post_id, $lang ) ) {
						$this->posts->create_media_translation( $post_id, $lang );
						$done++;
					} else {
						$missed++;
					}
				}
			}
		}
	}

Utiliser le code tel quel présente certains inconvénients. D'abord, il est **difficile à réutiliser** : pour ajouter une *action groupée*, il faut modifier l'intérieur de la méthode `translate()`. Cela risque à terme de créer une fonction contenant beaucoup de code dont seule une petite partie est exécutée à chaque appel. Cela peut créer de la confusion pour la maintenance ou les évolutions futures.

Ce code est également **fortement couplé** : pour appeler des méthodes d'autres modules de *Polylang*, il faut ajouter des *références* à des *instances* d'autres classes dans au sein de l'instance de `PLL_Bulk_Translate`. De cette manière, les différentes *classes* se retrouvent intrinsèquement liées et l'on perd l'avantage d'avoir découpé le code en plusieurs classe dans un premier temps. 

On souhaiterait à la place, pouvoir ajouter des *actions groupées* dans d'autres modules de *Polylang*, sans avoir à modifier celui-ci à chaque fois.

## Encapsuler du code réutilisable

### Composition et délégation

La *composition* consiste à inclure au sein d'une *classe*, une ou plusieurs *propriétés* étant elles-mêmes des *instances* d'autres *classes*. Par ce principe, on décide d'externaliser l'exécution de l'*action groupée* dans une nouvelle classe : `PLL_Bulk_Translate_Option`[^1].

![PLL_Bulk_Translate se compose d'une ou plusieurs PLL_Bulk_Translate_Option](../img/bulk-refactor-001_composition.png)

Ainsi, `PLL_Bulk_Translate` contient une *tableau* d'instances de `PLL_Bulk_Translate_Option`, chacune représentant une des *actions groupées* suivantes:

- Duplication d'articles
- Synchronisation d'articles
- Duplication de médias
- **Export de fichiers de traduction** : la fonctionnalité que nous avons ajouté.

La *classe* `PLL_Bulk_Translate` conserve les responsabilités de vérification des *paramètres* passés lors du déclenchement de l'*action groupée*, ainsi que de l'affichage du formulaire. Dans cette *classe composée*, on peut alors utiliser le principe de *délégation* pour donner la responsabilité de la traduction des *contenus* à la classe `PLL_Bulk_Translate_Option`.

![La traduction des contenus est déléguée à la classe PLL_Bulk_Translate_option](../img/bulk-refactor-002_delegation.png.png)

Ainsi lorsque `PLL_Bulk_Translate` reçoit l'instruction d'exécuter une *action groupée*, la méthode `handle_bulk_action()` vérifie les *paramètres* de la requête, mais l'exécution en question de cette action est déléguée à la méthode `do_bulk_action()` d'une *instance* de la classe `PLL_Bulk_Translate_Option`. Cette *instance* a été préalablement assignée à la propriété `PLL_Bulk_Translate::$options`. C'est également la méthode `PLL_Bulk_Translate::handle_bulk_action()` qui se charge de comprendre quelle est l'*instance* de `PLL_Bulk_Translate_Option` correspondant à l'*action groupée* demandée.

D'autre part, lorsque l'on affiche le formulaire servant à paramétrer l'*action groupée*, la méthode `PLL_Bulk_Translate::display_form()` va appeler chacune des `PLL_Bulk_Translate_Option` disponibles afin de pouvoir les afficher et de les rendre sélectionnables dans ce formulaire.

![Chaque option disponible est affichée dans le formulaire](../img/bulk-refactor-005_display-form.png)

### Le pattern "Strategy"

On peut relever des similarités dans le comportement des différentes *action groupée* de traduction:

- On **itère** sur le tableau des identifiants des *contenus* à traduire.
- Pour chaque *contenu*, on **vérifie** la pertinence d'en effectuer une traduction.
- Selon le résultat de cette vérification, on **exécute l'action** sélectionnée.

Evidemment, la logique d'**itération** est un point commun à toutes nos *actions groupées*, il y a peu de raison de réécrire le code s'en occupant à chaque fois que l'on veut rajouter une nouvelle action.

La **vérification** était commune aux actions disponibles avant le début du projet: il s'agissait alors de s'assurer que l'on écrasait pas un *contenu* déjà existant dans la base de données. Notre action d'*export groupé* n'a pas besoin de vérifier cela, car il ne s'agit pas d'écrire dans la base de données mais de générer un nouveau fichier. En revanche, il faut s'assurer que pour chaque *contenu* exporté, il n'y ai qu'une *traduction* de ce contenu qui soit envoyée en tant que source dans le fichier de traductions. Par exemple, on ne voudrait pas avoir à traduire une fois la page d'accueil depuis l'anglais, puis la traduire à nouveau depuis le français.

Pour l'**exécution** en question, il est évident qu'elle sera différente pour chacune des *actions de traduction groupée* disponibles.

La *classe* `PLL_Bulk_Translate_Option` va donc elle même être composée afin de ne pouvoir changer que le code dont on a besoin: c'est le *design pattern* "Startegy". On défini alors deux *interfaces* pour les classes composantes, afin d'expliciter les *méthodes* minimales requises pour que l'on puisse créer différentes *classes* interchangeables.

- `PLL_Bulk_Translate_Filter_Strategy` : Défini la méthode `filter` qui **vérifie** la pertinence d'exécuter l'action sur un *contenu*.
- `PLL_Bulk_Translate_Translate_Strategy` : Défini la méthode `translate` qui **exécute** l'action sélectionnée sur un *contenu* à partir de son identifiant et de la langue vers laquelle on souhaite le dupliquer / synchroniser / exporter / etc.

![La classe PLL_Bulk_Translate_Option se compose de deux stratégies](../img/bulk-refactor-003_strategy.png)


Toutes les *classes* implémentant ces *interfaces* seront donc assignables par notre classe `PLL_Bulk_Translate_Option`. L' **itération** sur les *contenus* à traduire va donc être exécuté par cette classe.  A l'intérieur de cette *boucle*, les étapes de **vérification** et d'**exécution** seront déléguées aux classes composantes.

	public function do_bulk_action( $object_ids, $languages ) {
		$done = 0;
		$missed = 0;
		if ( ! empty( $object_ids ) ) {
			foreach ( $object_ids as $object_id ) {
				foreach ( $languages as $lang ) {
					if ( $this->filter_strategy->filter( $object_id, $lang ) ) {
							$this->translate_strategy->translate( $object_id, $lang );
						$done++;
					} else {
						$missed++;
					}
				}
			}
		}
		return compact( 'done', 'missed' );
	}

## Créer un point d'accroche unique

### Instance partagée et pattern "Singleton"

Pour permettre aux différents modules de *Polylang* de s'interfacer avec, et d'utiliser ce mécanisme de traduction en masse, ils faut que ceux-ci puissent récupérer une *référence* à la classe `PLL_Bulk_Translate`. Plutôt que d'en créer une *instance* systématiquement et de chercher comment la récupérer, j'ai préféré *instancier* cette classe uniquement lorsque des actions de traductions groupées sont disponibles.

![Les modules peuvent passer des instances de PLL_Bulk_Translate_Option à la classe PLL_Bulk_Translate](../img/bulk-refactor-004_dependency-inversion.png)

L'enjeu est de n'avoir qu'une seule *instance* de la classe *PLL_Bulk_Translate* (ou aucune), contenant autant de `PLL_Bulk_Translate_Option` que le contexte requiert. C'est cette même instance qui doit être appelée depuis chaque module de *Polylang*. On se penche donc sur le *design pattern* "Singleton".

	class PLL_Bulk_Translate {
		private $instance;
		
		public static function register_options( $screen, $options ) {
			if ( null === self::$instance ) {
				self::$instance = new self();
			}
		}
	}

Ce *pattern* permet de retourner toujours la même *instance* de la classe qui l'implémente. Pour cela, la *classe* doit stocker l'instance courante dans une **propriété statique**. Cette propriété ne peut être récupérée qu'en passant par une **méthode statique**. Ainsi on peut contrôler à chaque appel de cette méthode statique (ici `register_options()`) si une *instance* de la classe existe, et en créer un nouvelle si ce n'est pas le cas.

Une fois que l'on a récupéré (ou créé) l'unique *instance* de la *classe* `PLL_Bulk_Translate`, on peut lui assigner les *options* pour chaque *écran d'administration*.

	class PLL_Bulk_Translate {
		public static function register_options( $screen, $options ) {
			// ...
			
			if ( is_array( $options ) ) {
				foreach ( $options as $option ) {
					self::$instance->options[ $screen ][ $option->get_name() ] = $option;
				}
			} else {
				self::$instance->options[ $screen ][ $options->get_name() ] = $options;
			}
		}
	}

De cette manière, on s'assure d'enregistrer toutes nos *instances* de `PLL_Bulk_Translate_Option` dans le même *tableau* (qui est la propriété `$options` d'une unique instance de `PLL_Bulk_Translate`). Cela nous assure par la suite, lorsque l'on itère sur ce *tableau* d'obtenir toutes les *actions de traduction groupée* existantes, quel que soit le module de *Polylang* à laquelle elles appartiennent.

[^1]: Le nom `PLL_Bulk_Translate_Option` vient de l'organisation de l'*interface utilisateur* des *actions groupées* de *WordPress*. En effet, l'utilisateur aura d'abord à choisir dans un menu déroulant l'action groupée nommée "Translate", ce qui lui ouvrira un menu avec un choix entre la duplication, la synchronisation ou l'export des contenus; d'où le terme d'"option".