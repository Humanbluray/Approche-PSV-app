# 08 – Stratégie de Test (Unit Tests & E2E)

**Date** : 2026-07-29  
**Statut** : En rédaction

## 1. Objectif
- Assurer la non-régression du moteur de simulation.
- Vérifier le comportement des RLS et de l'authentification.
- Valider les calculs de KPI et de scoring.

## 2. Types de tests
- **Unit Tests** (pytest) : Ciblent les classes métier et les services (Pas de base, pas de réseau).
- **Integration Tests** : Testent l'interaction avec Supabase (avec un mock local ou un projet de test).
- **E2E Tests** : Simulent des parcours utilisateur (inscription, création projet, simulation) via Flet test ou Selenium.

## 3. Outils
- `pytest` pour le backend.
- `pytest-asyncio` pour les async tests.
- `httpx` pour les tests d'API.
- `flet.test` (si disponible) ou `playwright` pour les tests E2E.

## 4. Couverture visée
- 90% de coverage sur le package `core/`.
- 70% de coverage sur le package `ui/` (plus difficile à tester).