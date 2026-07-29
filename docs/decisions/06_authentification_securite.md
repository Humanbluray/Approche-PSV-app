# 06 – Authentification, Sécurité et Multi-tenant

**Date** : 2026-07-29  
**Auteur** : [Votre nom]  
**Statut** : Approuvé

## 1. Contexte

Nous construisons un SaaS multi-entreprises. Chaque entreprise (organisation) doit avoir un accès strictement isolé à ses propres données (projets, simulations, utilisateurs). Nous utilisons **Supabase Auth** et **Row Level Security (RLS)** pour garantir cette isolation sans surcharger la logique applicative.

## 2. Choix techniques

| Composant | Technologie | Justification |
| :--- | :--- | :--- |
| **Gestion des utilisateurs** | Supabase Auth (built-in) | Gère l'inscription, la connexion, la réinitialisation de mot de passe, OAuth (Google, GitHub) et la session JWT. |
| **Isolation des données** | RLS (PostgreSQL) | Les politiques sont définies directement en base. L'application n'a pas besoin de filtrer manuellement par `organization_id`. |
| **Rôles utilisateurs** | Colonne `role` dans `profiles` | `admin`, `manager`, `viewer`. Permet de restreindre les actions (ex: seul un admin peut supprimer un projet). |
| **Session côté client** | JWT stocké dans le `secure_storage` de Flet | Le token est envoyé automatiquement par la librairie `supabase-py` dans les en-têtes `Authorization`. |

## 3. Organisation des rôles

| Rôle | Droits principaux | Exemples d'actions |
| :--- | :--- | :--- |
| **Admin** (Chef d'entreprise) | Tous les droits sur son organisation. | Ajouter/supprimer des utilisateurs, modifier les paramètres de l'organisation, supprimer des projets. |
| **Manager** (Chef de projet) | Droits de gestion sur les projets. | Créer/modifier/supprimer des projets, lancer des simulations, exporter des rapports, saisir des données réelles. |
| **Viewer** (Consultant, Client) | Lecture seule. | Consulter les tableaux de bord, les KPI et les résultats des simulations. Ne peut ni modifier ni lancer de simulations. |

## 4. Implémentation des politiques RLS (Exemples clés)

### 4.1. Politique pour `projects` (Lecture)
```sql
CREATE POLICY "Users can view projects in their org" ON projects
    FOR SELECT
    USING (
        organization_id IN (
            SELECT organization_id FROM profiles WHERE id = auth.uid()
        )
    );