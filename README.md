# REA Intelligence Platform

Plateforme indépendante d'orchestration et d'analyse technique intégrant le moteur officiel [REA (morluto/rea)](https://github.com/morluto/rea).

> **Statut : conception uniquement.** Aucun code applicatif n'est ajouté à ce stade.

## Documentation

- [Cahier d'architecture et de conception](docs/technical-design.md) — à ajouter après vérification de son contenu.
- [Feuille de route](docs/06-roadmap/roadmap.md) — à préparer.
- [Décisions d'architecture](docs/07-decisions/) — à préparer.

## Principes

1. REA reste le moteur spécialisé : pas de réécriture ni de fork principal.
2. La plateforme orchestre les projets, les agents, les preuves, l'IA et les rapports.
3. Les analyses qui nécessitent des fichiers locaux ou des outils natifs s'exécutent sur un agent ou un runner adapté.
4. Les résultats, erreurs, limites et preuves d'origine de REA sont préservés.
5. Les observations, hypothèses et conclusions validées sont distinguées.
6. Les analyses sont limitées aux cibles que l'utilisateur possède ou est autorisé à examiner.

## Dépôt officiel

Le moteur est maintenu séparément dans le dépôt [morluto/rea](https://github.com/morluto/rea).