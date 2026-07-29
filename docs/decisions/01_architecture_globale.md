# 01 – Architecture Globale

**Date** : 30 juillet 2026  
**Auteur** : [Votre nom]  
**Statut** : Approuvé

## Contexte

Nous transformons un prototype de simulation Monte Carlo (développé pour une thèse) en une plateforme SaaS professionnelle de pilotage de projets BTP. L’objectif est de permettre à des entreprises de gérer leurs projets, saisir des données réelles, lancer des simulations et obtenir des recommandations stratégiques.

## Options envisagées

1. **Backend traditionnel (FastAPI + PostgreSQL) + Frontend React**.
2. **Backend as a Service (Supabase) + Frontend Flet** (Python unifié).
3. **Full stack Django** (déploiement rapide).

## Décision retenue

**Option 2 : Supabase + Flet**

### Justifications

- **Unification du langage** : Python de bout en bout (simulation engine existant, Flet UI, logique métier). Pas de barrière JS.
- **Réduction du temps de développement** : Supabase gère l’auth, la base, le stockage et les politiques RLS.
- **Flexibilité** : On peut évoluer vers une architecture API (FastAPI) plus tard si besoin, sans tout réécrire.
- **Coût** : Supabase a un plan gratuit généreux pour le MVP.

### Conséquences

- Le code métier (`simulation_engine`, `test_engine`) sera encapsulé dans des **services** indépendants de la couche de persistance.
- Le frontend Flet interagira directement avec Supabase via `supabase-py` (approche Smart Client).
- Les simulations lourdes (Monte Carlo) s’exécuteront côté client ou dans une Edge Function Supabase si nécessaire.

## Règles de conception

1. **Séparation des responsabilités** : `domain/`, `adapters/`, `services/`, `ui/`.
2. **Tests unitaires** pour chaque couche (pytest).
3. **Documentation** des APIs (pydantic / OpenAPI si on ajoute FastAPI).

## Prochaines étapes

- Valider le modèle de données.
- Mettre en place le projet Supabase.
- Réécrire les repositories pour interagir avec Supabase.
- Adapter le moteur de simulation pour qu’il accepte un objet `Project` construit à partir de la base.