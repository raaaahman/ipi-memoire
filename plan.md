# Rapport d'activité : Développeur Fullstack chez WP Syntex

## Index

1. WP Syntex, une équipe distribuée
2. Polylang, une extension populaire
	1. Assurer le support technique
		* ticketing helpscout
		* préciser la demande
		* vérifier dans le code
	2. Résoudre les bugs
		* ticketing github
		* revue de bugs
		* documenter
			- tests unitaires
			- post mortem ?
3. L'importeur de traductions
	1. Cadre du projet et intentions de départ
	2. Intégration à un projet existant
		- Hooks
		- WP_Filesystem
		- Bulk action
	3. Sécuriser dès la conception
		- Erreur de débutant (supprimer n'importe quel fichier)
		- L'API WordPress (nonces et user permissions)
		- Vulnérabilités XSS
	4. Suite de tests automatisés
		* Mise en place de l'outil
		* Tester son code de manière indépendante
		* Tester d'abord, développer ensuite
	5. La communication au sein du projet
		* Etablir des "contrats"
			- UML
			- Intefaces
		* Versionnage du code
		* Code reviews
4. Refactoring des actions administrateur
	+ Strategy: une action, plusieurs implémentations
	+ Ajax
	+ Injection de dépendances ?
5. Annexe I : L'éco-système WordPress
	+ Un outil ouvert
		* Standards
		* Inspiration
		* Compatibilité
	+ Le WordCamp Europe, rencontre avec la communauté
6.  Annexe II : L'agilité en question