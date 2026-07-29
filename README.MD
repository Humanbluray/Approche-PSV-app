# 🏗️ Pilotage BTP – Plateforme SaaS de pilotage de projets de construction

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue?logo=python)](https://python.org)
[![Flet](https://img.shields.io/badge/Flet-0.22%2B-brightgreen?logo=flet)](https://flet.dev)
[![Supabase](https://img.shields.io/badge/Supabase-Cloud-3ecf8e?logo=supabase)](https://supabase.com)
[![Status](https://img.shields.io/badge/Status-Refactoring%20→%20SaaS-orange)]()

---

## 📖 Table des matières

- [📌 Présentation](#-présentation)
- [🚀 Fonctionnalités principales](#-fonctionnalités-principales)
- [🧱 Architecture technique](#-architecture-technique)
- [⚙️ Démarrage rapide](#️-démarrage-rapide)
- [📁 Structure du projet](#-structure-du-projet)
- [🛠️ Technologies utilisées](#️-technologies-utilisées)
- [📚 Documentation](#-documentation)
- [🤝 Contribution](#-contribution)
- [📄 Licence](#-licence)
- [📧 Contact](#-contact)

---

## 📌 Présentation

**Pilotage BTP** est une plateforme SaaS de pilotage de projets de construction (BTP) qui permet aux chefs de projet, aux entreprises et aux consultants de :

- 🏗️ **Gérer** la hiérarchie complète de leurs projets (sites, unités fonctionnelles, sous‑unités, tâches).
- 📊 **Simuler** trois approches de pilotage (CPM, EVmax, PSv) pour comparer leur performance dans des conditions financières variées.
- 📈 **Suivre** en temps réel les KPI (SPI, CPI, VSR, GRI, FEI, etc.) à partir des données réelles saisies périodiquement.
- 💡 **Recevoir** des recommandations opérationnelles sur les tâches à prioriser.
- 📁 **Exporter** les résultats sous forme de graphiques, tableaux Excel et rapports PDF.

Ce projet a initialement été développé dans le cadre d’une thèse de doctorat. Il est aujourd’hui en train d’être transformé en un produit SaaS professionnel, multitenant et prêt pour une utilisation en entreprise.

---

## 🚀 Fonctionnalités principales

### 🏗️ Gestion de projets
- Création de projets avec saisie manuelle ou **import depuis Excel** (format standard).
- Hiérarchie : **Site → Unité fonctionnelle → Sous‑unité → Tâche**.
- Stockage sécurisé des données dans le cloud (Supabase).
- Suivi de l’avancement réel des tâches (saisie périodique).

### 📊 Simulations
- **Trois approches** :
  - 🟦 **CPM** (chemin critique)
  - 🟧 **EVmax** (maximisation de la valeur gagnée)
  - 🟩 **PSv** (priorisation par valeur fonctionnelle)
- **Deux types de simulation** :
  - *Standard* : paramètres fixes (λ, tension financière) par régime (stable, intermédiaire, critique).
  - *Dynamique* : paramètres aléatoires (λ, avancement initial, paiements) ; le régime émerge du stress financier.
- **Monte Carlo** : exécution de centaines ou milliers de simulations pour une analyse statistique robuste.

### 📈 Visualisation et statistiques
- Tableaux de bord avec KPI en temps réel.
- Graphiques interactifs :
  - Évolution des KPI
  - Boxplots
  - Fonctions de répartition (CDF)
  - Heatmap des écarts
  - Jauges de performance
- Export des graphiques et données au format Excel ou CSV.

### 💡 Recommandations
- Suggestion des prochaines tâches à prioriser, justifiée par le score de l’approche sélectionnée.
- Filtrage par priorité, approche et période.
- Suivi de l’exécution des recommandations.

### 🔐 Sécurité et multi‑tenant
- Authentification (email/mot de passe, OAuth) gérée par **Supabase Auth**.
- Isolation stricte des données par organisation (**Row Level Security** en base).
- Rôles utilisateurs : Admin, Chef de projet, Consultant (lecture seule).

---

## 🧱 Architecture technique

L’application suit une **architecture hexagonale (ports & adapters)** pour faciliter la maintenance et l’évolution.

```mermaid
graph LR
    subgraph UI
        A[Flet UI]
    end

    subgraph Core
        B[Services métier]
        C[Entités]
        D[Interfaces]
    end

    subgraph Adapters
        E[Supabase Repository]
        F[Excel Importer]
        G[Memory Repository]
    end

    A --> B
    B --> C
    B --> D
    D --> E
    D --> F
    D --> G

````
# Cloner le dépôt
git clone https://github.com/votre-org/pilotage-btp.git
cd pilotage-btp

# Créer un environnement virtuel
python -m venv venv
source venv/bin/activate  # sous Windows : venv\Scripts\activate


# Installer les dépendances
pip install -r requirements.txt


# Copier le fichier d’exemple .env et le renseigner
cp .env.example .env

# Éditer .env avec vos clés Supabase

# Lancer l’application (mode développement)
python main.py

SUPABASE_URL=https://votre-projet.supabase.co
SUPABASE_ANON_KEY=clé_anonyme_publique
SECRET_KEY=une_chaine_aleatoire

# 📁 Structure du projet 

```mermaid
    pilotage-btp/
    ├── src/
    │   ├── core/                     
    │   │   ├── entities/             
    │   │   ├── services/             
    │   │   └── interfaces/           
    │   ├── adapters/                 
    │   ├── ui/                       
    │   │   ├── pages/                
    │   │   └── components/           
    │   └── database/                 
    ├── docs/                         
    │   ├── decisions/                
    │   ├── functional/               
    │   └── technical/                
    ├── tests/                        
    ├── assets/                       
    ├── exports/                      
    ├── graphs/                       
    ├── .env.example                  
    ├── main.py                       
    ├── requirements.txt              
    ├── README.md                     
    └── LICENSE       
```                