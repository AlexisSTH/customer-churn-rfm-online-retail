# Prédiction de Churn & Segmentation RFM — Retailer E-commerce

> Identifier les clients qui risquent de ne plus revenir, et comprendre pourquoi — à partir de 1 million de transactions réelles d'un retailer en ligne britannique.

**Statut : projet en cours** — les étapes d'analyse et de modélisation sont terminées, le dashboard Power BI est en construction.

---

## De quoi il s'agit

Quand un client e-commerce arrête d'acheter, l'entreprise le découvre souvent trop tard. L'idée de ce projet est simple : **anticiper** ce départ pour pouvoir agir avant qu'il ne soit définitif.

Je suis parti d'un cas concret — la base de transactions d'un retailer en ligne — et je l'ai traitée comme si c'était une vraie mission client : comprendre les données, segmenter les clients, construire un modèle de prédiction, et préparer un dashboard exploitable par une équipe marketing.

Le projet répond à deux questions complémentaires :
- **Qui sont mes clients ?** (segmentation)
- **Lesquels vont probablement partir ?** (prédiction)

---

## Le dataset

**Online Retail II** (UCI Machine Learning Repository) — un retailer en ligne britannique spécialisé dans les articles cadeaux, avec une clientèle majoritairement composée de petites boutiques qui recommandent régulièrement.

| | |
|---|---|
| Période | Décembre 2009 → Décembre 2011 (24 mois) |
| Transactions | ~1 067 000 |
| Clients identifiés | 5 942 |
| Marché retenu | Royaume-Uni (92% des transactions) |

Pourquoi ce dataset : contrairement à beaucoup de jeux de données e-commerce où les clients n'achètent qu'une seule fois, celui-ci présente **75% de clients multi-acheteurs**. C'est essentiel pour parler de fidélité et de churn de façon crédible.

> Petite anecdote méthodologique : j'avais d'abord commencé ce projet sur un autre dataset (Olist), avant de réaliser que 97% de ses clients n'avaient commandé qu'une fois — ce qui rendait toute analyse de churn bancale. J'ai préféré changer de dataset plutôt que de forcer une méthode inadaptée. Savoir reconnaître qu'un jeu de données ne permet pas de répondre à la question fait partie du travail.

---

## La démarche

### 1. Exploration & qualité des données
Chargement des transactions, audit qualité (valeurs manquantes, annulations, prix négatifs), analyse de la saisonnalité et de la fréquence d'achat. Un point de validation explicite a confirmé que le dataset se prêtait bien à une analyse RFM avant d'aller plus loin.

### 2. Feature engineering
Transformation de 725 000 lignes de transactions en un tableau d'une ligne par client, avec une cible « churn » construite par **découpage temporel** (les features sont calculées avant une date de référence, le churn est observé après — pour éviter toute fuite d'information du futur).

Quatre familles de variables ont été construites :
- **RFM classique** : récence, fréquence, montant
- **Comportement d'achat** : panier moyen, régularité, diversité produit
- **Comportement temporel** : ancienneté, intervalle entre commandes
- **Insatisfaction** : taux d'annulation

### 3. Segmentation
Deux approches complémentaires :
- **RFM rules-based** : 10 segments marketing lisibles (Champions, Fidèles, À risque, Perdus…)
- **KMeans** : segmentation data-driven qui exploite toutes les variables et révèle des profils que le RFM seul ne capte pas

### 4. Modélisation prédictive
Comparaison de trois modèles (Logistic Regression, Random Forest, XGBoost), validation croisée, analyse des variables les plus prédictives, et calibrage du seuil de décision selon des considérations business.

### 5. Dashboard Power BI *(en cours)*
Restitution interactive : vue globale, segments, et liste des clients à risque scorés.

---

## Ce que les données ont révélé

Quelques résultats que je trouve parlants :

**La récence prime sur tout le reste.** Le facteur le plus prédictif du churn n'est pas combien un client a dépensé, mais depuis combien de temps il n'a pas commandé — un poids plus de deux fois supérieur à celui de la fréquence. Un gros client silencieux depuis 6 mois est plus à risque qu'un petit client actif.

**Une poignée de clients pèse énormément.** 22 clients (0,5% de la base) représentent à eux seuls une part disproportionnée du chiffre d'affaires. Une stratégie de rétention ne peut pas traiter tout le monde de la même façon.

**Le modèle le plus simple a gagné.** La régression logistique a égalé voire dépassé XGBoost et Random Forest sur ce problème, tout en restant beaucoup plus facile à expliquer à une équipe non technique.

| Modèle retenu | Logistic Regression |
|---|---|
| ROC-AUC | 0,81 |
| PR-AUC | 0,78 |
| Validation croisée (PR-AUC) | 0,77 ± 0,01 (stable) |

---

## Stack technique

| Usage | Outil |
|---|---|
| Extraction & feature engineering | SQL (DuckDB) |
| Analyse & modélisation | Python (pandas, scikit-learn, XGBoost) |
| Visualisation | Matplotlib, Seaborn |
| Dashboard | Power BI *(en cours)* |

Le choix d'une approche SQL pour la préparation des données n'est pas anodin : il reproduit l'architecture qu'on retrouve en entreprise (un entrepôt de données qu'on interroge, puis une analyse en Python), et il passe à l'échelle bien mieux que de tout charger en mémoire.

---

## Structure du repo

```
.
├── data/
│   ├── raw/                 # Dataset source (à télécharger, voir ci-dessous)
│   └── processed/           # Tables générées par les notebooks
├── notebooks/
│   ├── 01_exploration.ipynb
│   ├── 02_feature_engineering.ipynb
│   ├── 03_rfm_segmentation.ipynb
│   └── 04_modeling.ipynb
├── models/                  # Modèle entraîné (.pkl)
├── reports/                 # Visuels et exports
└── README.md
```

---

## Reproduire le projet

```bash
# 1. Cloner le repo
git clone https://github.com/<ton-username>/<ton-repo>.git
cd <ton-repo>

# 2. Installer les dépendances
pip install -r requirements.txt

# 3. Télécharger le dataset
# Online Retail II : https://archive.ics.uci.edu/dataset/502/online+retail+ii
# Placer le CSV dans data/raw/

# 4. Lancer les notebooks dans l'ordre (01 → 04)
```

---

## À propos

Projet réalisé par **Alexis Sayasith**, data analyst freelance spécialisé e-commerce.

Je documente la construction de ce projet étape par étape sur LinkedIn — n'hésitez pas à y suivre les coulisses, les choix méthodologiques et les résultats.

*Disponible pour des missions autour de l'analyse client, du churn et de la segmentation.*
