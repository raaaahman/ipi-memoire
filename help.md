# Syntaxe des langages de programmation

Ce document traite d'une application programmée dans deux langages différents: PHP et Javascript. Ces langages partagent des particularités qui pourront sembler courantes aux programmeurs:

- **Interprétés** : Les deux langages nécessitent un logiciel qui fait la traduction du code écrit en *langage machine* au cours même de l'exécution du programme.
- **Faiblement typés** : Lorsque que le programmeur utilise des *structures de données* du langage, il n'a pas besoin de préciser de quel *type de données* il veut s'occuper, le langage analysant directement les *valeurs* manipulées; et peuvent éventuellement en réaliser la conversion.
- **Allocation automatique de la mémoire** : Le programmeur n'a pas à se soucier de la gestion de l'*espace mémoire* dédié au stockage temporaire des données. 
- **Orientés objet** : Les deux langages permettent de programmer selon ce *paradigme*. Contrairement au langage *Java*, d'autres paradigmes sont également envisageables comme la *programmation procédurale* ou la *programmation fonctionnelle*.

Leur syntaxe peut néanmoins varier pour des opérations semblables, et le tableau ci-dessous, loin d'être exhaustif, résume les principales formulations du code que le lecteur ou la lectrice pourra trouver dans ce document:

|   | PHP | Javascript |
|---|-----|------------|
| Fin d'instruction | Point virgule obligatoire |  Point virgule optionnel |
| Commentaires mono-lignes | `// Commentaire` | `// Commentaire` |
| Commentaires multi-lignes | `/* Commentaire  sur plusieurs lignes*/` | `/* Commentaire  sur plusieurs lignes*/` |
| Déclaration d'une variable | `$variable` (si non déclarée) | `let variable` |
| Appel de variable | `$variable` (si déjà déclarée) | `variable` |
| Convention de nommage des variables | `$underscore_case` | `camelCase` |
| Concaténation des chaînes | `'Ma chaîne' . ' de caractères';`| `'Ma chaîne' + ' de caractères'` |
| Insertion de variable | `"Ma chaîne {$variable}"` | `"Ma chaîne ${variable}"` |
| Déclaration d'un tableau | `$mon_tableau = array();` ou `$mon_tableau = [];` | `monTableau = []` |
| Accès à une entrée | `$mon_tableau[0];` ou `$mon_tableau["cle"];`| `monTableau[0]` |
| Ajout d'une entrée | `$mon_tableau[] = 1;` | `monTableau.push(1)` |
| Itération sur un tableau | `foreach($mon_tableau as $entree){ }` ou `foreach($mon_tableau as cle => valeur)` | `monTableau.forEach( function(entree){ })` |
| Déclaration de fonction | `function foo( $bar ) { }` | `function foo( bar) { }` |
| Appel de fonction | `foo( $bar );` | `foo( bar )` |

Concernant la *Programmation Orientée Objet* :

|   | PHP | Javascript |
|---|-----|------------|
| Déclaration de classe | `class Ma_Classe { }` | `class MaClasse { }` |
| Instanciation d'une classe | `$mon_instance = new Ma_classe();` | `monInstance = new MaClasse()` |
| Déclaration de propriété | `public|protected|private $ma_propriete;` | `maPropriete|#maProprietePrivee` |
| Appel d'une propriété | `$mon_instance->ma_propriete;` | `monInstance.maPropriete` |
| Déclaration d'une méthode | `public|protected|private function ma_methode(){ }` | `maMethode(){ }` |
| Appel d'une méthode | `$mon_instance->ma_methode();` | `monInstance.maMethode()` |
| Référence à l'instance | `$this` | `this` |
| Déclaration du constructeur | `public function __construct(){ }` | `constructor(){ }` |
| Déclaration d'une propriété statique | `static public|protected|private $propriete` | N/A |
| Appel d'une propriété statique | `Ma_classe::$propriete;` | N/A |
| Déclaration d'une méthode statique | `static public|protected|private methode(){ }` | `static methode()` |
| Appel d'une méthode statique | `Ma_Classe::methode();` | `MaClasse.methode()` |
| Référence statique à la classe | `self` | N/A |
| Héritage | `Ma_Sous_Classe extends Ma_classe { }` | `MaSousClasse extends MaClasse { }` |
| Référence au parent | `parent` | `super` |
| Surcharge de méthode | `public|protected|private function ma_methode(){ parent::ma_methode(); }` | `maMethode(){ super.maMethode() }`