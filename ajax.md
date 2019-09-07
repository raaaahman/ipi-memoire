# Communiquer en AJAX avec WordPress

## Un procédé éprouvé

### Qu'est-ce que l'AJAX ?

*AJAX* est l'acronyme d'*Asynchronous Javascript and XML*. C'est un procédé de communication *client/server* basé sur le protocole *HTTP*. Son appellation nous renseigne sur ses trois grands principes :

- **Asynchrone** : La réponse à une requête AJAX prenant une durée qui ne peut être connue à l'avance, tout le traitement dépendant de cette réponse est donc mis en attente pendant que le programme continue d'exécuter les tâches qui ne dépendent pas de cette réponse.
- **Javascript** : C'est ce langage, désormais embarqué dans tous les navigateurs web, qui va se charger de déclencher la requête vers le serveur. Cela permet aux développeurs de contrôler plus directement les conditions d'envoi de la requête à l'intérieur du programme, au lieu d'attendre que l'utilisateur la déclenche via les fonctions de son navigateur. C'est également dans le code Javascript du programme que va être effectuée le traitement de la réponse, ce qui permet de ne pas avoir à recharger la page HTML.
- **XML** : Les données de la réponse sont échangées sous forme de fichiers, au lieu d'être directement traitées par le navigateur. Si le format XML a été un des premiers à être utilisé pour ces fichiers d'échange, il n'est absolument pas obligatoire et tend à laisser place au JSON.

![Flux d'une requête AJAX.](ipi-memoire-images/ajax-0010_xmlhhtprequest.png)

### Pour quel besoin ?

Le procédé de l'AJAX n'est pas récent. Les premières versions de *WordPress* ont toutefois été développées avant que son utilisation ne soit répandue. Si au fil de son évolution, *WordPress* a intégré certaines action en AJAX, il reste une grande partie de ses fonctionnalités basées sur des *requêtes HTTP* du navigateur. Notre projet étend une de ces fonctionnalités, mais il nous serait utile de pouvoir bénéficier des possibilités de l'AJAX.

Avec le *protocole HTTP*, un téléchargement de fichier est lancé par le navigateur lorsque les *en-têtes de la réponse* définissent un contenu de type `attachment`. Or, pour rafraîchir la page HTML, et ainsi afficher des notifications ou remettre à zéro le formulaire, il faut récupérer une un réponse dont l'en-tête définit un contenu de type `text/html` (cf. [RFC 7231 - Section 3.1.1.5](https://tools.ietf.org/html/rfc7231#section-3.1.1.5)).

![Les réponses HTTP de type "attachment" sont téléchargées par le navigateur.](ipi-memoire-images/ajax-070_attachment.png)

Ce comportement est intégré dans les *navigateurs web*, et donc hors du contrôle du développeur. On peut utiliser l'AJAX pour contourner ces limitations : en écoutant la réponse du serveur dans le code *Javascript* exécuté par le navigateur, on peut alors décider des parties de la page HTML à modifier ainsi que de faire télécharger un fichier à l'utilisateur, selon le contenu de cette réponse.

![Ont veut traiter les données de la réponses, et en télécharger une partie.](ipi-memoire-images/ajax-080_ajax-download.png)

## Envoyer la requête

### Attacher l'événement

Parce que l'on ne désire pas remplacer tout le système d'*actions groupées de WordPress*, et que les actions de traduction groupée déjà présentes dans *Polylang* fonctionnent très bien avec le système actuel, on désire que des *requêtes HTTP* coexistent avec les *requêtes AJAX* que l'on va ajouter. Pour envoyer ces requêtes AJAX il est de toutes façons nécessaire de surveiller les actions de l'utilisateur du *côté client*. 

	$( '#pll-translate' ).on( 'click', '[type=submit]', function( event ) {
		// ...
	} );

On utilise ici la *DOM API de Javascript*, à travers la bibliothèque *jQuery*. Cela permet à notre application de modifier dynamiquement la page HTML et de réagir aux actions de l'utilisateur. Si l'on décompose ce code, `$( '#pll-translate' )` récupère un *élément* de la page HTML, ici celui ayant l'identifiant `#pll-translate`, et nous permet d'utiliser les fonctionnalités de *jQuery* en le référençant dans un *objet jQuery*.

On cherche à **écouter les événements du DOM** concernant cet élément récupéré. Cette fonctionnalité native de Javascript est simplifiée par *jQuery* à travers la méthode `on()`, qui prend ici trois paramètres:
 
- `'click'` : Est le nom de notre *événement*, qui correspond à un clic de l'utilisateur sur l'élément précédemment récupéré.
- `'[type=submit]'` : Filtre cet événement, on ne s'intéresse ici qu'aux clics sur les *éléments enfants* de l'élément `#pll-translate` ayant l'*attribut* `type="submit"`.
- `function( event ) { ... }` : Cette *fonction anonyme* est appelée lorsque l'*événement* écouté est déclenché, et passe le filtre sélectionné. Elle reçoit en paramètre un *objet représentant l'événement* en question.

### Formater la requête

Lors de l'*appel* de cette fonction, on cherche à récupérer les *valeurs* entrées par l'utilisateur dans le *formulaire* mis à sa disposition. Comme l'on a précédemment récupéré l'*objet* représentant l'événement, on peut utiliser sa propriété `target` pour récupérer l'élément précis sur lequel l'utilisateur a cliqué. Comme cet élément fait partie du formulaire, on utilise la méthode `closest` de jQuery, qui va nous permettre de récupérer l'élément de type `'form'` (un formulaire HTML) le plus proche.
 
	let form = $( event.target ).closest( 'form' );
	
	let formData = form.serialize();
	
	$.post( ajaxurl, formData, function( response ) {
		// Code exécuté si la réponse indique un succès
	});

On utilise ensuite la méthode `serialize()` de *jQuery*, qui permet de transformer les *données du formulaire* en une chaîne de caractères sérialisée selon le format d'une *requête HTTP*. On peut ensuite utiliser ces données pour les envoyer à notre *serveur*. Encore une fois, on utilise une méthode fournie par *jQuery*, `post()`, en lui passant trois paramètres :

- `ajaxurl` : Cette *variable* contient l'*URL* vers laquelle on souhaite diriger la requête AJAX. Sa *valeur* est assignée par *WordPress* afin de rediriger vers un *script PHP* spécifique aux traitement des requêtes AJAX dans le *CMS*. 
- `formData` : Les données précédemment sérialisées. A noter que *jQuery* peut également se débrouiller avec un *objet Javascript*.
- `function( response ) { ... }` : Cette *fonction anonyme* est appelée dès que le navigateur reçoit la *réponse* du serveur. Elle reçoit alors comme paramètre le *corps* de la réponse reçue.

### Vérifier les paramètres

Il nous reste alors à ajouter une étape de vérification pour déterminer si l'on désire une requête en AJAX ou non. Pour cela, on cherche dans le formulaire l'*action groupée* sélectionnée par l'utilisateur grâce à la méthode `find()`, à laquelle on passe comme paramètre le type correspondant à un champ de formulaire, que l'on a nommé "translate" dans notre formulaire, et qui possède l'attribut `checked` (car il est coché).

		let option = form.find( 'input[name=translate]:checked' ).val();
		
		if ( 'export' === option) {
			event.preventDefault();
			
			$.post( ajaxurl, formData, function( response ) {
				// Code exécuté si la réponse indique un succès
			});
			
		}

On en récupère ensuite la valeur grâce à la méthode `val()` de *jQuery*, et l'on vérifie si cette valeur est la chaîne de caractère `'export'`, auquel cas on désire lancer une requête AJAX. Avant de lancer cette requête, on arrête l'exécution par défaut de la gestion des formulaires par le navigateur, en utilisant la méthode `preventDefault()`, provenant de la *DOM API de Javascript*. Dans le cas contraire, le navigateur aurait envoyé une *requête HTTP* classique vers le serveur, comme c'est normalement le cas lorsque l'on clique sur un un bouton d'envoi de formulaire.

## Traitement côté serveur

### Point d'entrée

![Uilitaires proposés par WordPress pour gérer une requête en AJAX.](ipi-memoire-images/ajax-0011_request-tools.png)

[^1]: Il s'agit bien de l'*URL* que *WordPress* a copié pour aider les développeurs dans la variable Javascript `ajaxurl` (uniquement pour les pages du  panneau d'administration).

Lorsqu'une requête *AJAX* est effectuée dans *WordPress*, elle est traitée par le script `/wp-admin/admin-ajax.php`[^1]. On ne fera pas exception à la règle. Ce script ne déclenche pas les mêmes *événements* que lorsque l'on envoie une *requête HTTP* classique, qui est elle traitée par le script `/wp-admin/admin.php`.

On peut par contre attacher des *actions* sur l' *événement dynamique* `'wp_ajax-{$action}'`. En effet, *WordPress* va vérifier le contenu de la *requête* depuis la *variable superglobale* `$_REQUEST` de PHP un paramètre nommé `'action'`, et composer le nom de l'*événement* à partir de celui-ci.

[developer.wordpress.org/reference/hooks/wp_ajax_action/](https://developer.wordpress.org/reference/hooks/wp_ajax_action/)

	$action = ( isset( $_REQUEST['action'] ) ) ? $_REQUEST['action'] : '';
	
	if ( is_user_logged_in() ) {
		// If no action is registered, return a Bad Request response.
		if ( ! has_action( "wp_ajax_{$action}" ) ) {
			wp_die( '0', 400 );
		}
	
		do_action( "wp_ajax_{$action}" );
	}

Comme on a donné le nom de l'*action groupée* précédemment à *WordPress* (cf. le chapitre *S'interfacer avec WordPress : les hooks - Filtrer la liste des actions groupées* de ce document), ce nom sera présent dans le formulaire. Il va donc être passé au script `/wp-admin/admin-ajax.php` dans le *corps de la requête AJAX*. Ce qui va permettre à *WordPress* de déclencher un événement nommé `'wp_ajax_pll_translate'` sur lequel on va attacher les *actions* nécessaires au traitement de cette requête.

![Le nom de l'action est passé à travers les requêtes.](ipi-memoire-images/ajax-020_action-name.png)

### Vérification du nonce

Le "nonce" (pour "number used once"[^2]), sert à protéger des **attaques CSRF* (Cross Site Request Forgery). Il s'agit de générer un nombre sur le *serveur* et de l'envoyer au client lors d'une première réponse. Lors d'une seconde requête, on compare ce nombre que le serveur a gardé en mémoire, au nombre renvoyé dans cette requête. On s'assure ainsi qu'un utilisateur a effectivement demandé à effectuer l'action que nous sommes en train de traiter.

![Le nombre est passé du serveur au client, qui le renvoie ensuite au serveur.](ipi-memoire-images/ajax-040_nonce.png)

[^2]: En réalité les "nonces" de *WordPress* sont utilisés plusieurs fois, ils ont une durée de vie par défaut d'une journée. Ils ne sont pas "gardés en mémoire" mais ils sont régénérés à chaque fois à partir de l'identifiant de l'utilisateur, du nom de l'action qu'il essaie d'exécuter, et de la date.

*WordPress* dispose d'une *API* nous permettant d'utiliser ces *nonces* avec un minimum d'effort. Seulement, ce n'est pas automatique, et il est nécessaire de bien penser à *sécuriser* ces communications entre le client et le serveur. On ajoute donc un *nonce* au formulaire, en utilisant la fonction `wp_nonce_field()` fournie par *WordPress*.

[developer.wordpress.org/reference/functions/wp_nonce_field/](https://developer.wordpress.org/reference/functions/wp_nonce_field/) :

	wp_nonce_field( int|string $action = -1, string $name = '_wpnonce', bool $referer = true, bool $echo = true )

Cette fonction prend les *paramètres* suivants :

- `$action` : nom de l'action pour laquelle le *nonce* a été créé. Dans notre cas, `'pll_translate'`.
- `$name` : nom du *nonce*, on choisit ici `'_pll_translate_nonce'`.
- `$referer` : Si `true`, *WordPress* vérifiera également que la requête provient de l'une de ses pages. On garde ce comportement par défaut.
- `$echo` : Affiche le champ du *nonce* si `true`. Comme on place le nonce dans notre formulaire, on conserve cette valeur par défaut.

Il nous suffit ensuite de vérifier ce nonce avec la fonction `check_ajax_referer()` fournie par *WordPress*. Cette fonction va chercher dans les données du formulaire, récupérées dans la variable superglobale `$_POST`, si les valeurs suivantes correspondent :

- `'pll_translate'` doit être le nom de l'*action groupée*
-  `'_pll_translate_nonce'` doit être le nom du *nonce*
-  la page qui a effectuée la requête doit être une des pages de *WordPress*

Si ces valeurs ne correspondent pas, le *script* est immédiatement arrêté et une erreur est retournée à l'utilisateur. Sinon, l'exécution du code se poursuit sur les lignes suivantes.

	public function handle_ajax() {
		check_ajax_referer( 'pll_translate', '_pll_translate_nonce' );
		
		// Le code qui suit ne sera exécuté que si la fonction précédente ne termine pas le processus.
	}

### Réutiliser les règles métier

Les *règles métier* restent les mêmes que l'on déclenche notre script en *AJAX* ou par une *requête HTTP* classique. On décide donc de réutiliser le code qui les applique. Par conséquent, dès que l'on a fini de vérifier les paramètres spécifiques à cette requête AJAX (nonce, nom de l'action, page d'origine de la requête), on appelle la méthode qui s'occupe de traiter l'*action groupée* dans le cas d'une requête HTTP classique.

![Traitement des spécifités d'AJAX dans une fonction dédiée.](ipi-memoire-images/ajax-030_reusability.png)

## Retourner une réponse

### Formater les données

Pour formater la réponse, on utilise la classe `WP_Ajax_Response` fournie par l'*API de WordPress*. Elle contient les méthodes suivantes:

- `add( $args )` : Ajoute un élément à la réponse, peut recevoir les paramètres suivants:
	- `'action'` : le nom de l'action qui a généré cette réponse, par défaut le nom récupéré dans les données du formulaire
	- `'data'` : les données qui seront envoyées dans la réponse
	- `'supplemental'` : d'autres données, qui pourront être traitée séparément des précédentes
- `send()` : Transmet les données enregistrées dans l'objet au tampon de sortie

Une fois nos *règles métier* exécutées, on recueille leur résultat dans la propriété `$results` de la classe `PLL_Bulk_Translate`. Il nous suffit alors de les transférer à une nouvelle instance de la classe `WP_Ajax_Response`, par le biais de sa méthode `add()`.

	public function handle_ajax() {
			
		// ...
		
		$response = new WP_Ajax_Response();
		$response_args = [];
	
		if ( ! empty( $this->results['message'] ) ) {
			$response_args['data'] = $this->results['message'];
		}
	
		$response->add( $response_args );
	
		$response->send();
	}

Une fois que l'on a transféré les *données* désirées dans cet objet, on utilise sa méthode `send()` qui va s'occuper de communiquer la réponse au client. Si le code est écrit pour nous dans cette méthode, on peut toutefois rapidement s'intéresser au code qu'elle exécute:

- Définit les **en-têtes** correspondant à une réponse au format XML.
- Formate les *données* contenues dans l'objet sous forme de **balises XML**.
- Envoie les chaînes de caractères ainsi formatées dans le *tampon de sortie*, qui les intègre alors au **corps de la réponse**

### Affichage asynchrone

Lors de l'envoi d'une *requête AJAX*, le navigateur ne s'attend pas à recevoir une réponse dans l'immédiat, afin de ne pas être bloqué durant cette attente. On dit que l'exécution de cette action est **asynchrone**. C'est le *moteur javascript* du navigateur qui va gérer cet asynchronisme, en enregistrant les tâches dans la *pile des événements*. Ce mécanisme de priorisation des tâches n'a pas besoin d'être connu du développeur et fonctionne de manière automatique.[^3]

[^3]: La conférence de Philip Roberts, *What the heck is the event loop anyway?* explique de manière aussi simple et claire que possible le sujet. Elle a été enregistrée en vidéo et on peut la trouver à l'adresse suivante : [youtube.com/watch?v=8aGhZQkoFbQ&list=WL&index=45&t=0s](https://www.youtube.com/watch?v=8aGhZQkoFbQ&list=WL&index=45&t=0s)

![Le navigateur ne bloque pas les interactions de l'utilisateur le temps que la requête soit traitée.](ipi-memoire-images/ajax-050_asynchronous.png)

La réponse va ensuite être passée par le navigateur au *code Javascript* en cours d'exécution. Lorsque l'on a précédemment envoyé la requête via *jQuery*, on a déclaré une *fonction anonyme* qui définit comment traiter cette réponse. C'est le moment où cette fonction est appelée, récupérant en paramètre le **corps de la réponse** formatée en objet Javascript.

En parallèle à la classe PHP `WP_Ajax_Response`, *WordPress* met également à disposition l'objet Javascript `wpAjax` qui sert à interpréter, *côté client*, les réponses qui ont été formatées, *côté serveur*, par la précédente. 

![Les utilitaires mis à disposition par WordPress pour gérer les réponses en AJAX.](ipi-memoire-images/ajax-061_response-tools.png)

La principale responsabilité de cet *objet* va être de vérifier que la réponse est valide, et de la formater en *objet Javascript*. On peut ensuite lire toutes les données stockées dans la propriété `responses` de l'objet `wpAjax`, en itérant sur ce tableau via la méthode `forEach()`.

	$.post( ajaxurl, formData, function( response ) {
				
		response = wpAjax.parseAjaxResponse( response, 'ajax-response' );
		response.responses.forEach( function( responseObject ) {
		
			// Code effectuant le téléchargement du fichier
			
		} );
		
		$( '[type=checkbox]:checked' ).removeAttr( 'checked' );
		$( '#pll-translate .cancel' ).trigger( 'click' );
	});

Une fois que ce traitement a été complété, pour chacune des *données* reçues en réponse, on procède à la mise à jour de la page HTML. On utilise encore une fois les méthodes fournies par *jQuery*:

- `removeAttr( 'checked' )` permet de réinitialiser les listes des champs ayant été cochés par l'utilisateur, c'est-à-dire, les *contenus* sélectionnés pour la traduction groupée ainsi que les *langues* vers lesquells l'utilisateur a choisi de les traduire.
- `trigger( 'click' )` permet de déclencher les fonctions ayant été enregistrées sur l'*événement* `'click'` du bouton de fermeture de notre formulaire, et ainsi, de ne pas en réécrire le code.

![Le formulaire de la traduction groupée peut être refermé en cliquant sur le bouton "cancel".](ipi-memoire-images/ajax-065_close-form.png)

## Télécharger des données récupérées en AJAX

On est passé d'un fonctionnement de *requêtes HTTP* déclenchées et gérées entièrement par le navigateur à un système de *requêtes AJAX* qui nous permet de contrôler l'envoi et la récupération des données par du code Javascript. Par contre, le fichier que nous souhaitons récupérer via cette requête n'est toujours pas téléchargé. Comme il a été envoyé dans le *corps de la réponse*, on peut cependant le manipuler par du code Javascript.

L'astuce consiste à créer dynamiquement, dans le *DOM*, un nouvel élément de type *lien hypertexte* (la balise `a`). Ce lien ne va pas diriger vers une *URL externe*, mais à la place va utiliser le mot-clé `data` pour indiquer au *navigateur* que l'on s'intéresse à un jeu de *données*, qu'on lui passe sous forme sérialisée via la fonction `encodeURIComponent()`. Il s'agit des *données de la réponse*, contenues dans le tableau `response.responses`, précédemment généré par la méthode `wpAjax.parseResponse()` (cf. chapitre précédent).

	response.responses.forEach( function( responseObject ) {
	
		let download = document.createElement( 'a' );
		
		download.setAttribute(
			'href',
			'data:application/xml;charset=utf-8,' +
			encodeURIComponent( responseObject.data )
		);
		
		download.setAttribute( 'download', 'pll_export.xliff' );
	}

L'attribut `download` indique au navigateur de télécharger ces données lors d'un clic sur ce lien. Clic que l'on peut déclencher via la *DOM API de Javascript*. Une fois fait, on supprime cet élément devenu inutile. Comme on a défini le style de notre élément en `display: none`, il n'a pas pu apparaître aux yeux de l'utilisateur.

	download.style.display = 'none';
	document.body.appendChild( download );
	download.click();
	document.body.removeChild( download );

## En conclusion

Bien que faisant partie de la même application, les codes PHP et Javascripts présentés dans ce chapitre sont des éléments distincts, nécessitant d'établir une **connexion** afin d'échanger des données. L'étude de ce procédé de l'AJAX m'a permis d'acquérir une meilleure compréhension du *protocole de communication HTTP* qui supporte le *World Wide Web*.

Ces problématiques de programmation à destination d'un **réseau** a soulevé des interrogations concernant la *sécurité des données échangées*. Si *WordPress* dispose d'outils permettant de traiter les écueils les plus courants, leur utilisation m'a toutefois permis d'appréhender les notions mises en oeuvre.

La sécurité des *extensions WordPress* reste une question très discutée dans la communauté, et j'ai eu l'opportunité de participer à un échange avec Julio Potier, auteur de l'extension dédiée à la sécurité *SecuPress*, durant le salon *WordCamp Paris 2019*. J'ai résumé cette conférence à travers un billet sur mon blog à l'adresse suivante [devindetails.com/fr/wordpress-security-an-ouvert-talk-with-julio-potier/](https://devindetails.com/fr/wordpress-security-an-ouvert-talk-with-julio-potier/) (ce billet est également joint en annexe de ce rapport). 