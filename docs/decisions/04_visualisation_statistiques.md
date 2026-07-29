# 04 – Visualisation et Statistiques (Décisions Métier & Techniques)

**Date** : 2026-07-29  
**Auteur** : [Votre nom]  
**Statut** : Approuvé  
**Version** : 2.0 (Révision post-implémentation)

---

## 1. Contexte

Nous devons prouver l'efficacité du PSv en régime critique. Une simple comparaison des **moyennes** des KPI est insuffisante pour démontrer la robustesse. Les premiers résultats de simulation ont montré que les moyennes peuvent être proches entre les approches, masquant des différences significatives dans les **queues de distribution** (pires cas).

---

## 2. Décision fondamentale : Les moyennes ne suffisent pas

**Raison** : La moyenne cache la dispersion. En régime critique, ce qui importe est d'éviter les "effondrements" (valeurs extrêmement basses). Une approche avec une moyenne légèrement inférieure mais une variance faible est souvent préférable en gestion de crise.

**Exemple concret** (issu de nos simulations) :
- **EVmax** : SPI moyen = 0.69, écart-type = 0.25, min = 0.10.
- **PSv** : SPI moyen = 0.65, écart-type = 0.18, min = 0.30.

Bien que la moyenne du PSv soit légèrement inférieure, son **minimum** est beaucoup plus élevé (0.30 vs 0.10). En situation critique, le PSv évite les échecs catastrophiques. C'est cette propriété que nous devons mettre en évidence.

---

## 3. Indicateurs statistiques retenus

Nous affichons systématiquement pour chaque KPI (SPI, CPI, VSR, GRI, FEI, Taux d'avancement, Unités activées) :

| Indicateur | Rôle | Calcul |
| :--- | :--- | :--- |
| **Moyenne** | Tendance centrale | `mean(values)` |
| **Écart-type** | Dispersion absolue | `std(values)` |
| **Min / Max** | Extrêmes observés | `min(values)` / `max(values)` |
| **Médiane** | Centrale robuste | `median(values)` |
| **Percentile 5%** | Pire 5% (risque) | `quantile(0.05)` |
| **Percentile 95%** | Meilleur 5% | `quantile(0.95)` |
| **VaR (Value at Risk)** | Perte maximale probable (5%) | `quantile(0.05)` (identique à P5 pour la cohérence) |
| **CVaR (Conditional VaR)** | Perte moyenne dans le pire 5% | `mean(values <= VaR_5)` |

**Décision technique** : Ces indicateurs sont calculés **à la volée** dans la page des résultats à partir des données brutes stockées en session. Ils sont ensuite exportés dans l'Excel (onglet "Statistiques" et "Risque").

---

## 4. Visualisations retenues

### 4.1. Boxplots (Distribution complète)

**Objectif** : Visualiser la médiane, les quartiles, l'étendue et les outliers pour chaque combinaison (Régime, Approche).

**Décision** :
- Un seul boxplot par KPI (ex : SPI) avec 9 boîtes (3 régimes × 3 approches).
- Les régimes sont séparés par des lignes verticales en pointillés.
- Les couleurs sont fixes : CPM (bleu), EVmax (orange), PSv (vert).
- Une ligne horizontale rouge en pointillés indique le seuil idéal (1.0).

**Implémentation** :
- Utilisation de `matplotlib.boxplot()`.
- Les données proviennent de `resultats_bruts` (liste des scores finaux par approche et régime).

### 4.2. CDF (Fonctions de répartition)

**Objectif** : Montrer la probabilité que le KPI soit en dessous d’un seuil. Une courbe plus à droite signifie une meilleure performance robuste.

**Décision** :
- Tracer une CDF pour chaque combinaison (Régime, Approche).
- Utiliser le style : couleur = approche, type de ligne = régime (ex: stable = plein, intermédiaire = tireté, critique = pointillé).
- Ajouter une ligne verticale au seuil idéal (1.0).

**Interprétation** : En régime critique, la courbe du PSv doit être **à droite** de celle d'EVmax, signifiant que le PSv a une probabilité plus faible d'être en dessous d'un seuil critique.

### 4.3. Range plots (Moyenne ± P5-P95)

**Objectif** : Visualiser la moyenne et la dispersion en un seul coup d'œil.

**Décision** :
- Pour chaque combinaison (Régime, Approche), afficher un point pour la moyenne et une barre verticale allant de P5 à P95.
- Idéal pour comparer la dispersion entre les approches.

### 4.4. Heatmap des écarts (PSv - EVmax)

**Objectif** : Visualiser rapidement où PSv surpasse EVmax.

**Décision** :
- Matrice : Régimes en lignes, Indicateurs (SPI, CPI, VSR, GRI, FEI, Taux, Unités activées) en colonnes.
- Valeur = Moyenne(PSv) - Moyenne(EVmax).
- Palette de couleurs : Rouge (négatif = EVmax meilleur) → Vert (positif = PSv meilleur).
- Permet une lecture synthétique des avantages relatifs.

### 4.5. Jauges de performance (Gauge)

**Objectif** : Vue d'ensemble rapide des KPI principaux par approche.

**Décision** :
- Trois indicateurs clés : SPI, VSR, FEI.
- Barres horizontales avec seuil à 1.0.
- Utile pour le dashboard.

---

## 5. Gestion des données pour les graphiques (Contrainte forte)

**Problème identifié** : Pour générer des boxplots et CDF, il est impératif de stocker les **résultats bruts** de chaque simulation (les 1000 valeurs) et non pas uniquement les moyennes.

**Solution implémentée** :
- La session de l'utilisateur (`page.session`) stocke un dictionnaire `resultats_bruts` contenant la liste des scores finaux par combinaison (Approche, Régime).
- Ce dictionnaire est construit lors de l'exécution du Monte Carlo dynamique (dans `test_engine_dynamique.lancer_test_complet()`).
- Les graphiques sont générés à la volée lors de l'affichage de la page.

**Alternative écartée** : Stocker les données brutes en base de données. Cette option a été rejetée pour des raisons de performance (3000 simulations × 9 combinaisons = 27 000 lignes par KPI, ce qui alourdirait inutilement la base). Le stockage en mémoire vive (session) est suffisant pour une session utilisateur.

---

## 6. Synthèse des décisions

| Décision | Justification |
| :--- | :--- |
| **Ne pas se contenter des moyennes** | Les moyennes masquent les différences de dispersion, critiques en régime critique. |
| **Ajouter des indicateurs de risque** | VaR et CVaR quantifient les pires cas. |
| **Utiliser boxplots, CDF, Range plots** | Ces visualisations montrent la dispersion et les queues de distribution. |
| **Stocker les données brutes en session** | Permet de générer les graphiques sans surcharger la base. |
| **Utiliser des couleurs et styles standardisés** | Facilite la comparaison entre les graphiques. |

---

## 7. Prochaines étapes

- Implémenter le stockage de `resultats_bruts` dans la session.
- Générer les boxplots, CDF et range plots à partir de ces données.
- Ajouter les indicateurs statistiques (VaR, CVaR) dans l'export Excel.
- Intégrer les jauges de performance dans le dashboard.

---

## 8. Références croisées

- Document 03 (Simulation Engine) : Détail de la génération des données brutes.
- Document 05 (Roadmap) : Planification de l'implémentation des visualisations.