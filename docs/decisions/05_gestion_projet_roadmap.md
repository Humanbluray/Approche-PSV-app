# 05 – Plan de Développement (Roadmap SaaS)

**Date** : 2026-07-29  
**Auteur** : CARLOS NGABA  
**Statut** : Approuvé

## 1. Objectif global

Transformer le prototype en un outil SaaS professionnel, maintenable et commercialisable.

## 2. Phases de développement (Cycle en V adapté)

### Phase 0 : Cadrage (Terminée)
- Définition des périmètres.
- Rédaction des documents de conception (ce présent fichier).

### Phase 1 : Fondations (Sprint 1-2)
- **Supabase** : Création du projet, implémentation du schéma SQL et des RLS.
- **Refacto Core** : Création du package `domain/` (entités pures) et `core/` (services).
- **Authentification** : Implémentation de la connexion/inscription dans Flet via Supabase.

### Phase 2 : Gestion des Projets (Sprint 3-4)
- **UI** : Création de la vue "Mes Projets" (Liste, Création, Suppression).
- **Import Excel** : Interface de drag & drop pour importer la structure de projet Excel dans Supabase.
- **Hiérarchie** : Visualisation de l'arborescence du projet (Arbre des tâches).

### Phase 3 : Données Réelles & KPI (Sprint 5-6)
- **Saisie** : Formulaire de saisie des données réelles par période (AC, avancement, paiements).
- **Calcul** : Calcul et stockage des KPI réels (SPI, CPI, etc.) à partir des données saisies.
- **Dashboard** : Affichage des KPI réels dans des jauges et graphiques d'évolution (Évolution du SPI/CPI dans le temps réel).

### Phase 4 : Simulations (Sprint 7-8)
- **Configuration** : Interface permettant de choisir le type de simulation (Standard par Régime ou Dynamique).
- **Paramètres** : Sliders et inputs pour λ, Budget initial, Tensions.
- **Exécution** : Lancement des simulations avec barre de progression (threading).
- **Résultats** : Affichage des tableaux (Moyennes par Régime/Approche) et des graphiques (Boxplots, CDF, Heatmap).

### Phase 5 : Recommandations (Sprint 9)
- **Logique** : Service de recommandation qui analyse le résultat de simulation pour suggérer les prochaines tâches prioritaires (basé sur le score de l'approche choisie).
- **UI** : Affichage d'une liste "Tâches recommandées" sur le dashboard du projet.

### Phase 6 : Polissage & Déploiement (Sprint 10)
- **Performance** : Optimisation des requêtes SQL et du moteur Python.
- **Dockerisation** : Conteneurisation de l'application Flet pour faciliter le déploiement.
- **Tests** : Mise en place des tests end-to-end pour les flux critiques.

## 3. Maintenance et évolutions

- **Feature flags** : Pour activer/désactiver des fonctionnalités sans déploiement.
- **Centralisation des logs** : Utilisation d'un logger structuré (JSON) pour faciliter le debug en production.