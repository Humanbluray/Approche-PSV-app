# 03 – Moteur de Simulation (Refactorisation)

**Date** : 2026-07-29  
**Auteur** : CARLOS NGABA  
**Statut** : Approuvé

## 1. Contexte

Nous possédons un moteur de simulation fonctionnel (`simulation_engine.py`) initialement couplé à des objets `Projet` construits par l'importateur Excel. Pour le SaaS, nous devons découpler ce moteur pour qu'il accepte indifféremment des données provenant de Supabase, d'Excel ou de l'API.

## 2. Décision architecturale

**Isoler le moteur dans un package `core/` sans dépendance externe** (ni SQL, ni Flet, ni Pandas si possible, sauf pour les calculs mathématiques).

### Structure cible

```python
# core/entities/task.py (dataclass)
# core/entities/project.py (contient listes de sites, etc.)
# core/services/simulation_service.py (contient SimulationEngine)
# core/services/monte_carlo_service.py (contient TestEngine)