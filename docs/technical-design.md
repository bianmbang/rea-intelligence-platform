# Cahier d'architecture et de conception

> Statut : conception uniquement. Aucun code applicatif n'est ajouté à cette étape.

## 1. Objet du projet

L'objectif de la plateforme est d'exploiter le moteur officiel REA (`morluto/rea`) comme moteur technique central, sans le réécrire ni le faire diverger du dépôt officiel.

La plateforme apporte l'interface produit, la gestion des projets, la gestion des cibles, l'orchestration des analyses, les agents locaux ou distants, le traçage des preuves, le routage des modèles IA, les rapports et la gouvernance.

L'architecture doit assurer une distinction claire entre :

- REA : moteur technique spécialisé d'analyse ;
- la plateforme : orchestration, sécurité, travail collaboratif, preuves et reporting ;
- les modèles IA : assistance à l'interprétation, au plan d'investigation et à la synthèse ;
- l'agent local : exécution des analyses sur les fichiers et outils du poste ou d'un runner dédié.

## 2. Décision retenue

Option A retenue : intégration complète de REA.

Les principes sont les suivants :

1. REA reste le moteur officiel utilisé pour les opérations techniques.
2. La plateforme ne maintient pas de fork du moteur.
3. Les interfaces de REA (CLI, MCP, providers, capabilities, sessions, evidence) servent de source de vérité.
4. Les outils natifs et fichiers locaux ne sont pas déplacés vers le cloud dans des environnements incapables de les exécuter.
5. Les preuves ne sont ni altérées ni surinterprétées.

## 3. Architecture cible

### 3.1 Vue d'ensemble

```text
Utilisateur
  |
  v
Frontend Next.js
  |
  v
Backend central (Node.js / TypeScript)
  |-- API Gateway
  |-- Project Manager
  |-- Job Orchestrator
  |-- REA Connector
  |-- AI Model Router
  |-- Evidence Manager
  |-- Resource Registry
  |-- Report Generator
  |-- Audit Service
  |
  v
PostgreSQL / Redis / object storage
  |
  v
Agent local ou runner
  |
  v
REA MCP + REA CLI + outils natifs
```

### 3.2 Rôles des composants

#### Frontend — Next.js

L'interface est une application de travail, pas un simple chat.

Panneaux attendus :

- Dashboard
- Projects
- Targets
- Analysis Workspace
- Agents
- Models
- Resources
- Reports
- Settings

Le workspace d'analyse doit contenir :

- gauche : cible, fichiers, périmètre ;
- centre : étapes, tâches, résultats ;
- droite : preuves, hypothèses, outils, modèle IA ;
- bas : journal, diagnostics et erreurs.

#### Backend central — Node.js / TypeScript

Le backend centralise la logique de sécurité, les tâches, les autorisations, les traces, les preuves et la génération de rapports.

Services logiques :

- API Gateway
- Project Manager
- Job Orchestrator
- REA Connector
- AI Model Router
- Evidence Manager
- Resource Registry
- Report Generator
- Audit Service

#### Agent local

L'agent est le composant qui exécute les opérations Rea sur l'environnement réel.

Ses responsabilités :

- authentification sortante vers le backend ;
- déclaration des capacités réelles ;
- réception des tâches autorisées ;
- exécution de REA via CLI ou MCP ;
- remontée des résultats et des journaux ;
- signalement des limitations et indisponibilités ;
- respect du périmètre et des autorisations.

#### Runner distant

Les runners sont utilisés pour les analyses à isolation forte, pour les environnements Linux ou les modes conteneurisés.

Ils doivent être :

- isolés ;
- contrôlés ;
- visibles dans le catalogue des agents ;
- associé à des capacités explicitement déclarées.

#### Services externes

- OpenRouter ou autre fournisseur IA ;
- stockage objet compatible S3/R2 ;
- fournisseurs IA éventuels ;
- services de logging et d'authentification.

## 4. Intégration REA

### 4.1 Règle d'intégration

La plateforme ne réécrit pas REA. Elle s'appuie sur ses interfaces officielles et sur ses providers/capabilities réels.

Les éléments à intégrer sont :

- REA CLI
- REA MCP
- providers
- capabilities
- sessions
- evidence

### 4.2 Découverte des capacités

La plateforme ne doit pas supposer qu'un agent possède une capacité. Elle doit récupérer les informations réelles, notamment via :

- `rea providers --json`
- `rea capabilities --json`

Cela permet de distinguer :

- capacité détectée ;
- capacité demandée ;
- capacité utilisable ;
- capacité indisponible.

Le connecteur REA doit préserver les diagnostics d'un provider indisponible. Il ne doit pas masquer l'erreur ni inventer des capacités.

### 4.3 Connecteur REA

Le connecteur est la couche d'abstraction entre la plateforme et les interfaces REA. Il doit exposer des responsabilités cohérentes :

- connect()
- discoverTools()
- discoverProviders()
- discoverCapabilities()
- invokeTool()
- cancelOperation()
- collectEvidence()
- close()

Ces méthodes sont des abstractions de la plateforme ; elles ne doivent pas être confondues avec des commandes ou API officielles de REA.

### 4.4 Sessions et preuves

Chaque analyse doit être associée à :

- un projet ;
- une cible ;
- un agent ;
- une session REA ;
- un provider ;
- un outil ;
- des paramètres ;
- un résultat ;
- des références Evidence.

Une preuve doit conserver au minimum :

- sa source ;
- son type ;
- la date ;
- l'outil / version ;
- le projet et la cible ;
- le contenu ou une référence vers un artefact ;
- son niveau de fiabilité ;
- ses limites.

## 5. Gestion des preuves

La plateforme doit distinguer clairement :

- Observé
- Dérivé
- Hypothèse
- Inconnu
- Validé

Les preuves doivent être reliées à leur session REA, à la cible et au rapport final.

Une conclusion ne doit pas être présentée comme certaine si elle n'est qu'une hypothèse. Les rapports doivent séparer :

- faits observés ;
- hypothèses ;
- conclusions ;
- inconnues ;
- limites de l'investigation.

## 6. Modèle de données conceptuel

Stockage recommandé :

- PostgreSQL pour les métadonnées et les relations ;
- stockage objet (S3/R2 compatible) pour les artefacts lourds ;
- Redis + BullMQ pour les tâches asynchrones.

Entités principales :

- users
- workspaces
- projects
- targets
- analysis_jobs
- analysis_steps
- rea_agents
- rea_capabilities
- rea_sessions
- evidence_items
- artifacts
- ai_providers
- ai_models
- ai_calls
- resource_packages
- resource_components
- reports
- audit_logs

## 7. AI Model Router

Le routeur IA ne remplace pas REA.

Il sert à :

- classifier la cible ;
- organiser le plan d'investigation ;
- interpréter les preuves ;
- synthétiser les résultats ;
- rédiger le rapport technique ;
- vérifier les conclusions.

Le routeur sélectionne un modèle selon la catégorie de travail :

- classification rapide et économique ;
- plan d'investigation ;
- lecture de pseudocode ;
- analyse d'architecture ;
- rédaction de rapports ;
- vérification indépendante.

Politique de fallback :

1. vérifier disponibilité du modèle ;
2. vérifier cotisations / quotas / budget ;
3. sélectionner le meilleur modèle compatible ;
4. appliquer un délai et un nombre de tentatives limité ;
5. basculer sur un modèle de secours si nécessaire ;
6. enregistrer le modèle utilisé dans la trace d'analyse.

## 8. Sécurité et gouvernance

Les risques sont multiples :

- fichier malveillant ;
- exécution automatique de scripts ;
- fuite de données ;
- SSRF sur des cibles web ;
- mauvais rattachement des preuves ;
- agent compromis ;
- exécution sans validation.

Mesures prévues :

- quarantaine et validation des imports ;
- chemins sécurisés et pas de Zip Slip ;
- pas d'exécution automatique de ressource importée ;
- authentification, rôles et permissions ;
- jetons révoquables ;
- accès restreint aux agents ;
- audit des actions et des permissions ;
- minimisation des données envoyées aux modèles externes ;
- respect du périmètre autorisé pour chaque analyse.

## 9. Contrats internes de la plateforme

Les API de la plateforme sont des contrats internes, pas des endpoints REA.

Propositions principales :

- POST /api/projects
- POST /api/targets
- POST /api/analyses
- GET /api/analyses/:id
- POST /api/analyses/:id/cancel
- GET /api/agents
- POST /api/agents/enroll
- GET /api/models
- POST /api/resources/import
- GET /api/reports/:id

L'objectif est d'avoir un backend stable, même si REA évolue.

## 10. Stratégie d'implémentation recommandée

### 10.1 Première version

- monolithe modulaire côté backend ;
- agents locaux Windows prioritaires ;
- backend central à partir d'un serveur Node.js ;
- stockage objet pour artefacts ;
- PostgreSQL pour les métadonnées ;
- Redis + BullMQ pour le pipeline de tâches ;
- interface Next.js ;
- intégration REA via CLI/MCP et découverte dynamique des capacités.

### 10.2 Déploiement recommandé

- Frontend web : Cloudflare ou hébergement web adapté ;
- Backend : serveur Node.js / VPS ;
- Agents / runners : machines locales ou distantes ;
- Stockage : S3/R2 compatible ;
- Analyse technique : sur les machines qui disposent des outils natifs.

## 11. Conditions de réussite

La première version ne sera considérée comme valide que si :

- REA est utilisé sans fork ni modification arbitraire du moteur ;
- la plateforme découvre réellement les capacités disponibles ;
- un agent local peut exécuter une tâche autorisée ;
- les analyses sont suivies en temps réel ;
- les preuves restent consultables ;
- les erreurs de modèle ou de provider déclenchent un mécanisme explicite ;
- les conclusions distinguent faits, hypothèses et inconnues ;
- les fichiers importés ne sont jamais exécutés sans validation ;
- les opérations sont journalisées et les agents sont révocables.

## 12. Livrables de conception

La conception doit livrer :

- architecture cible et modules ;
- plan d'intégration REA ;
- flux d'analyse ;
- gouvernance de sécurité ;
- modèle de données conceptuel ;
- contrats internes ;
- feuille de route par phase ;
- ADR de décisions clés.

## 13. Point de vigilance

L'architecture doit rester compatible avec les évolutions du dépôt officiel REA. Le connecteur ne doit pas dépendre de commandes ou comportements non documentés ou non contraints par une version épinglée.

---

Ce document est une base de conception technique. Il ne décrit ni un code ni un prototype applicatif, mais la structure lourde de la plateforme et les décisions qui cadrent la suite du projet.
