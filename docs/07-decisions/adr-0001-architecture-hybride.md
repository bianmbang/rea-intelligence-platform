# ADR-0001 — Architecture hybride

- Statut : acceptée comme orientation de conception
- Date : 2026-11-10

## Contexte

Les analyses techniques peuvent nécessiter des outils natifs, des fichiers locaux, des outils installés au poste ou un environnement isolé. Un simple environnement serverless ne peut pas être supposé capable de tout exécuter.

## Décision

Nous retenons une architecture hybride :

- frontend web indexé / hébergé de façon adaptée ;
- backend centralisé ;
- agents locaux ou runners dédiés ;
- analyse technique exécutée sur l'environnement le plus adapté.

## Conséquences

- la plateforme reste flexible et rentable ;
- les analyses natives restent possibles ;
- l'agent doit être fiable et sécurisé ;
- la couche cloud reste orientée orchestration et gouvernance.

## Alternatives évaluées

- cloud-only ;
- local-only ;
- multi-microservices immédiat.

## Décision d'architecture

L'architecture hybride est la bonne base pour une première version sans surconception.
