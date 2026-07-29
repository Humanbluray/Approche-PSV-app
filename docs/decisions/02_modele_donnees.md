# 02 – Modèle de Données (Supabase/PostgreSQL)

**Date** : 2026-07-29  
**Auteur** : NGABA VANYA  
**Statut** : Approuvé

## 1. Contexte

Nous avons besoin d’un schéma robuste, multi-tenant, permettant de stocker à la fois la hiérarchie statique des projets (Sites, Unités, Tâches) et les données évolutives (simulations, données réelles, recommandations).

## 2. Principes directeurs

- **Multi-tenant** : Toutes les tables métier contiennent une référence vers `organizations.id` ou l'héritent via une clé étrangère (`project_id`).
- **UUID** : Utilisation d'UUIDs par défaut pour éviter les collisions et faciliter la fusion de données.
- **Audit** : Colonnes `created_at` et `updated_at` systématiques.
- **Données flexibles** : Utilisation de `JSONB` pour les configurations (ex : paramètres de simulation, prédécesseurs des tâches).

## 3. Structure des tables (Entités principales)

| Table | Rôle |
| :--- | :--- |
| `organizations` | Tenant racine (entreprises) |
| `profiles` | Utilisateurs liés à `auth.users` et à une `organization` |
| `projects` | Projet BTP, statut, paramètres globaux (`config`) |
| `sites` / `functional_units` / `sub_units` / `tasks` | Hiérarchie complète du projet |
| `real_data_periods` | Saisie périodique des données réelles (budget, paiements, etc.) |
| `real_task_progress` | Détail de l’avancement réel des tâches sur une période |
| `simulations` | En-tête d’une simulation (paramètres configurés) |
| `simulation_results` | Résultats par approche (CPM, EVmax, PSv) avec données détaillées en JSON |
| `recommendations` | Suggestions de tâches générées par le système |

## 4. Décisions de sécurité (RLS)

**Décision** : Nous utilisons les **Row Level Security (RLS)** de Supabase pour isoler les données. L'application n'aura jamais besoin de filtrer manuellement par `organization_id` ; Supabase le fera en fonction de l'utilisateur connecté.

**Politiques clés** :
- `USING (organization_id IN (SELECT organization_id FROM profiles WHERE id = auth.uid()))` pour les lectures.
- `WITH CHECK` similaire pour les écritures (sauf pour les admins).
- Les profils avec `role = 'admin'` héritent de droits étendus sur leur organisation.

## 5. Choix alternatifs écartés

- **SQLite** : Impossible pour le multi-tenant et l'échelle.
- **MongoDB** : Nous avons besoin de relations complexes (JOINs) pour la hiérarchie ; PostgreSQL avec JSONB est plus adapté.