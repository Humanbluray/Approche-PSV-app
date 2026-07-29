# 07 – Système de Recommandations Opérationnelles

**Date** : 2026-07-29  
**Auteur** : [Votre nom]  
**Statut** : Approuvé  
**Version** : 2.0 (Révision approfondie)

---

## 1. Contexte et Valeur Ajoutée

L'un des objectifs majeurs du SaaS est de fournir **des suggestions concrètes et actionnables** au chef de projet : "Quelle tâche dois-je exécuter en priorité pour rester dans les clous financiers et temporels ?"

La recommandation ne doit pas être une simple liste de tâches. Elle doit :
- Être **justifiée** par une logique claire (scoring issu des approches CPM, EVmax, PSv).
- Être **contextualisée** (période actuelle, avancement réel, stress financier).
- Être **hiérarchisée** (priorité haute, moyenne, basse).
- Proposer une **raison compréhensible** (ex: "Cette tâche est critique pour le délai" ou "Elle active une unité fonctionnelle à 80%").

---

## 2. Principe Général et Chaîne Logique

La recommandation s'appuie sur le **scoring des tâches** déjà implémenté dans le moteur de simulation (`simulation_engine.py`). Ce scoring est utilisé pour classer les tâches par ordre de priorité à chaque période.

**Chaîne logique complète** :
