# ADR-0002 — Monolithe modulaire initial

- Statut : acceptée comme orientation de conception
- Date : 2026-11-10

## Contexte

Le projet doit d'abord stabiliser ses interfaces REA, ses workflows et ses gardes-fous de sécurité avant d'entrer dans la complexité d'une architecture distribuée.

## Décision

Le backend est conçu comme un monolithe modulaire, avec des services ou modules métier distincts, mais sans microservices immédiats.

## Conséquences

- développement plus simple au démarrage ;
- déploiement plus simple ;
- meilleure traçabilité ;
- possibilité de découper plus tard si le projet grossit.

## Alternatives évaluées

- microservices immédiats ;
- monolithe non structuré ;
- séparation très tôt en services d'analyse et services métiers.

## Décision d'architecture

Un monolithe modulaire est plus adapté à la phase de conception et à la validation de l'intégration REA.
