# Feuille de route

## Phase 0 — Cadrage et documentation

Objectif : clarifier le périmètre, la vision et les principes du projet.

Livrables :
- vision du produit
- décision d'intégration REA
- règle de non-réécriture du moteur
- hiérarchie de responsabilités

Statut : terminé au niveau de la conception initiale.

## Phase 1 — Audit REA et validation des interfaces

Objectif : vérifier les interfaces officielles du moteur et les capacités réellement disponibles.

À valider :
- CLI REA
- MCP REA
- providers
- capabilities
- sessions
- evidence
- dépendances et prérequis
- conditions système et de sécurité

Livrable : dossier d'intégration et matrice de compatibilité.

## Phase 2 — Spécification fonctionnelle et technique

Objectif : formaliser les services, modules, workflows et contrats.

Contenu :
- architecture détaillée
- flux d'analyse
- modèle de données
- contrats internes
- sécurité et permissions
- exigences de qualité

## Phase 3 — Agent local et runners

Objectif : sécuriser l'exécution des analyses sur les machines autorisées.

Contenu :
- enrôlement agent
- authentification sortante
- déclaration de capacités
- distribution de jobs
- remontée de preuves
- journaux et diagnostics

## Phase 4 — IA et production de rapports

Objectif : fiabiliser l'assistance IA sans la confondre avec la logique technique.

Contenu :
- OpenRouter ou autre fournisseur
- routeur IA
- fallback
- minimisation de contexte
- synthèse de rapports
- traçabilité des modèles

## Phase 5 — Interface produit et validation

Objectif : livrer une plateforme exploitable de bout en bout.

Contenu :
- frontend
- dashboard et workspaces
- visualisation des preuves
- rapports techniques
- tests fonctionnels et de sécurité

## Phase 6 — Déploiement et exploitation

Objectif : passer en environnement pilote puis production maîtrisée.

Contenu :
- déploiement hybride ou serveur
- sécurité de production
- reprise après panne
- observabilité
- politique de mise à jour REA

## Critères de passage entre phases

Une phase n'est validée que si :
- le livrable associé est documenté ;
- les dépendances et risques sont listés ;
- les critères d'acceptation sont clairs ;
- le passage à la phase suivante est explicite.
