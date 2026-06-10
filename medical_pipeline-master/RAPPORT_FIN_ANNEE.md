# Rapport de Fin d'Année — Projet *Medical Pipeline*

**Diagnostic IA à partir des symptômes — Scraping NHS & Machine Learning**

| | |
|---|---|
| **Projet** | Medical Pipeline — Diagnostic IA NHS |
| **Auteur** | ElKarizma |
| **Période** | Année 2025 |
| **Date du rapport** | 10 juin 2026 |
| **Dépôt** | `ElKarizma/medical_pipeline-master` |
| **Stack technique** | Python · Streamlit · scikit-learn · BeautifulSoup · DVC |

---

## 1. Résumé exécutif

Le projet **Medical Pipeline** est une application complète de **science des données médicales** qui couvre toute la chaîne de valeur d'un projet de Machine Learning : de la **collecte automatisée de données** (web scraping du site officiel du NHS) jusqu'à un **outil de diagnostic interactif** présenté dans une interface web moderne.

L'application permet, à partir d'une liste de symptômes cochés par l'utilisateur, de prédire si un patient est **« Malade »** ou **« Sain »**, d'estimer une **probabilité** et un **niveau de confiance**, puis d'identifier la **maladie la plus probable** par similarité symptomatique.

Le projet démontre la maîtrise d'un **pipeline de données de bout en bout** :
**Scraping → Nettoyage → Contrôle qualité → Entraînement de modèles → Prédiction → Interface utilisateur.**

> ⚠️ **Note importante** : l'outil est à vocation **éducative uniquement** et ne remplace en aucun cas un avis médical professionnel. Cet avertissement est d'ailleurs intégré directement dans l'interface.

---

## 2. Objectifs du projet

| # | Objectif | Statut |
|---|----------|--------|
| 1 | Collecter automatiquement des données de symptômes depuis une source fiable (NHS) | ✅ Atteint |
| 2 | Construire un dataset structuré et exploitable (X = symptômes, y = label) | ✅ Atteint |
| 3 | Mettre en place un pipeline de nettoyage et de contrôle qualité des données | ✅ Atteint |
| 4 | Entraîner et comparer plusieurs modèles de classification | ✅ Atteint |
| 5 | Identifier la maladie probable au-delà du simple diagnostic binaire | ✅ Atteint |
| 6 | Proposer une interface utilisateur claire et accessible | ✅ Atteint |
| 7 | Assurer la reproductibilité et le versionnement (Git + DVC) | 🟡 Partiel |

---

## 3. Architecture technique

### 3.1 Vue d'ensemble du pipeline

```
┌──────────────┐   ┌──────────────┐   ┌────────────────┐   ┌──────────────┐   ┌──────────────┐
│  scraper_nhs │ → │   cleaning   │ → │ quality_check  │ → │    model     │ → │     app      │
│  (collecte)  │   │ (nettoyage)  │   │   (qualité)    │   │  (ML / IA)   │   │ (interface)  │
└──────────────┘   └──────────────┘   └────────────────┘   └──────────────┘   └──────────────┘
       │                                                            │
       ▼                                                            ▼
 data/raw/*.csv                                              models/*.pkl
```

### 3.2 Description des modules

| Fichier | Rôle | Points clés |
|---------|------|-------------|
| **`scraper_nhs.py`** | Collecte des données | Scrape les pages de maladies du NHS, détecte **50 symptômes cibles** via un dictionnaire de mots-clés (EN), génère des profils « sains » synthétiques, et exporte un CSV. |
| **`cleaning.py`** | Nettoyage | Suppression des doublons, gestion des valeurs manquantes (remplissage par 0 pour les symptômes binaires), correction des types. |
| **`quality_check.py`** | Contrôle qualité | 4 contrôles automatiques (manquants, doublons, équilibre des classes, complétude) avec un **score global sur 100**. |
| **`model.py`** | Modélisation IA | Entraîne un **Random Forest** et une **Régression Logistique**, sauvegarde les modèles, et réalise des prédictions + identification de maladie par **similarité de Jaccard**. |
| **`app.py`** | Interface web | Application **Streamlit** en 3 onglets (Scraping → Pipeline → Diagnostic), avec un design sombre personnalisé en CSS. |

### 3.3 Stack technologique

- **Langage** : Python
- **Collecte de données** : `requests`, `BeautifulSoup` (bs4)
- **Manipulation de données** : `pandas`, `numpy`
- **Machine Learning** : `scikit-learn` (RandomForestClassifier, LogisticRegression, StandardScaler)
- **Interface** : `Streamlit`
- **Sérialisation des modèles** : `pickle`
- **Versionnement** : `Git` + `DVC` (Data Version Control)

---

## 4. Fonctionnalités réalisées

### 4.1 Collecte de données (Scraping NHS)
- Récupération automatique de la liste des maladies depuis `https://www.nhs.uk/conditions/`.
- Détection de **50 symptômes** par correspondance de mots-clés (ex. `fever` ↔ *"fever", "high temperature", "pyrexia"*).
- Extraction ciblée des **sections « Symptoms »** des pages.
- Génération de **profils sains synthétiques** pour équilibrer le dataset.
- Paramétrage du nombre de maladies (10 à 100) et export CSV automatique.

### 4.2 Pipeline de traitement
- **Nettoyage** des doublons, valeurs manquantes et types.
- **Contrôle qualité** automatisé avec score :
  - Valeurs manquantes < 15 %
  - Absence de doublons
  - Équilibre malades/sains (ratio entre 30 % et 70 %)
  - Au moins 1 symptôme par ligne

### 4.3 Modélisation et diagnostic
- Entraînement comparé de **2 modèles** (Random Forest & Régression Logistique) avec `class_weight="balanced"`.
- Séparation train/test (80/20) stratifiée.
- Prédiction avec **probabilité** et **niveau de confiance** (Haute / Moyenne / Faible).
- **Identification de la maladie probable** via similarité de Jaccard entre les symptômes du patient et les profils appris.

### 4.4 Interface utilisateur
- Application web fluide en **3 onglets** guidant l'utilisateur étape par étape.
- Visualisations : métriques, top 10 des symptômes, cartes de résultats colorées.
- Design sombre soigné et message d'avertissement médical.

---

## 5. Bilan technique

### 5.1 Points forts ✅
- **Pipeline complet et cohérent** couvrant tout le cycle de vie d'un projet ML.
- **Séparation claire des responsabilités** (un module = une étape).
- **Code lisible et documenté** (docstrings, nommage explicite en français).
- **Bonnes pratiques ML** : stratification, gestion du déséquilibre des classes, normalisation pour la régression logistique.
- **Valeur ajoutée** au-delà du diagnostic binaire grâce à l'identification de maladie.
- **Interface professionnelle** et expérience utilisateur soignée.

### 5.2 Limites et axes d'amélioration 🟡
| Limite | Recommandation |
|--------|----------------|
| Absence de fichier `requirements.txt` | Ajouter les dépendances figées pour la reproductibilité. |
| Pas de tests automatisés | Mettre en place des tests unitaires (`pytest`) sur le nettoyage et le modèle. |
| Données « saines » synthétiques | Envisager des données réelles ou une stratégie de génération plus réaliste. |
| Détection par mots-clés simple | Explorer le NLP (embeddings) pour une extraction de symptômes plus robuste. |
| Pas de README ni documentation | Rédiger un `README.md` (installation, lancement, captures). |
| Évaluation limitée à l'*accuracy* | Ajouter précision, rappel, F1-score et matrice de confusion. |
| DVC configuré mais non finalisé | Compléter le suivi des datasets/modèles avec DVC. |
| Pas de CI/CD | Ajouter un workflow GitHub Actions (lint + tests). |

---

## 6. Indicateurs du projet

| Indicateur | Valeur |
|------------|--------|
| Nombre de modules Python | 5 |
| Symptômes pris en charge | 50 |
| Modèles de ML | 2 (Random Forest, Régression Logistique) |
| Modèles sérialisés (`models/`) | 5 fichiers `.pkl` |
| Contrôles qualité automatiques | 4 |
| Onglets de l'interface | 3 |
| Source de données | NHS (officielle) |
| Commits Git | 1 (« first commit ») |

---

## 7. Perspectives pour l'année prochaine

1. **Fiabiliser la base** : `requirements.txt`, `README.md`, tests unitaires et CI/CD.
2. **Enrichir les données** : davantage de maladies, données saines réelles, mise à jour périodique automatisée.
3. **Améliorer les modèles** : métriques avancées, validation croisée, exploration de modèles supplémentaires (XGBoost, etc.).
4. **Renforcer l'extraction de symptômes** par des techniques de NLP.
5. **Déploiement** : héberger l'application (Streamlit Community Cloud, Docker) pour la rendre accessible en ligne.
6. **Finaliser le versionnement** des données et modèles avec DVC.

---

## 8. Conclusion

Le projet **Medical Pipeline** constitue une **réalisation solide et aboutie** illustrant la mise en œuvre complète d'un projet de data science, depuis la collecte des données jusqu'à la livraison d'un produit utilisable. Il combine **compétences techniques** (scraping, ingénierie de données, machine learning) et **souci de l'expérience utilisateur**.

Les bases posées cette année sont saines et bien structurées. Les principaux chantiers de l'année à venir concernent la **professionnalisation** (tests, documentation, déploiement) et l'**amélioration de la qualité des données et des modèles**. Le projet est ainsi très bien positionné pour évoluer vers une version plus robuste et déployable.

---

*Rapport généré le 10 juin 2026 — Projet Medical Pipeline (ElKarizma).*
*Outil à finalité éducative — ne se substitue pas à un avis médical professionnel.*
