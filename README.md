# 🏠 Exploration SQL — Portefeuille Assurances Habitation

![SQL](https://img.shields.io/badge/SQL-4479A1?style=flat-square&logo=postgresql&logoColor=white)
![SQLite](https://img.shields.io/badge/SQLite-003B57?style=flat-square&logo=sqlite&logoColor=white)
![DB%20Browser](https://img.shields.io/badge/DB_Browser-003B57?style=flat-square&logo=sqlite&logoColor=white)
![Status](https://img.shields.io/badge/Status-Completed-22c55e?style=flat-square)

50 000+ contrats d'assurance habitation. Requêtes complexes (jointures, agrégations, sous-requêtes, vues) pour répondre aux questions business : quels profils coûtent le plus cher ? Quelles régions concentrent le CA ? Où sont les opportunités de croissance ?

---

## 📖 Contexte

Une compagnie d'assurance habitation veut exploiter sa base de données pour piloter sa stratégie commerciale. Les équipes métier posent des questions précises — l'analyse SQL doit y répondre avec des chiffres actionnables.

**La question centrale :** Où se trouvent les leviers de croissance dans un portefeuille de 50 000 contrats ?

---

## 🎯 Objectifs

- Identifier les régions les plus rentables
- Analyser la répartition géographique des contrats
- Segmenter les clients par profil de risque
- Formuler des recommandations stratégiques

---

## 🔍 Résultats clés

### 1️⃣ Concentration géographique

**Top 3 départements par nombre de contrats :**
- Paris (75) : 8 542 contrats (17,1%)
- Hauts-de-Seine (92) : 6 234 contrats (12,5%)
- Nord (59) : 4 891 contrats (9,8%)

### 2️⃣ Profil des assurés

- **88%** de maisons vs 12% d'appartements
- Surface moyenne : **98 m²**
- Cotisation moyenne : **342 € / an**

### 3️⃣ Segmentation par formule

```sql
-- Répartition des formules d'assurance
SELECT
    formule,
    COUNT(*) as nb_contrats,
    ROUND(AVG(cotisation), 2) as cotisation_moyenne,
    ROUND(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM Contrat), 2) as pourcentage
FROM Contrat
GROUP BY formule
ORDER BY nb_contrats DESC;

/* Résultats :
Formule Standard : 32 456 contrats (64,9%) - 298€/an
Formule Premium  : 12 891 contrats (25,8%) - 487€/an
Formule Eco      :  4 653 contrats (9,3%)  - 189€/an
*/
```

---

## 💡 Conclusion

> **Les contrats sont fortement concentrés en Île-de-France (45% du portefeuille) avec un potentiel d'expansion en régions sous-représentées.**

**Insights clés :**
1. Concentration géographique : 3 départements = 40% du CA
2. Opportunité premium : 25% des clients acceptent +63% de cotisation
3. Marché maisons sous-exploité : 88% des contrats mais potentiel appartements urbains

---

## 📋 Recommandations business

### 🚨 Court terme (0-6 mois)

**1. Campagne ciblée Île-de-France**
- Concentrer acquisition sur Paris (75), Hauts-de-Seine (92), Val-de-Marne (94)
- Budget prioritaire : 60% IDF, 40% reste France
- **Impact estimé :** +15% nouveaux contrats

### 🎯 Moyen terme (6-12 mois)

**2. Montée en gamme formule Premium**
- 26% des clients déjà en Premium (forte acceptation)
- Cibler clients Standard avec surface >120m² (potentiel +63% CA/client)
- **Impact estimé :** +8% CA sans acquisition

**3. Expansion géographique**
- Régions sous-représentées : Bretagne, Pays de la Loire, PACA
- Partenariats agences locales
- **Impact estimé :** +2500 contrats/an

### 🌱 Long terme (12-24 mois)

**4. Segmentation par risque**
- Créer scoring basé sur : département, surface, type local
- Ajuster cotisations par profil de risque
- **Impact estimé :** Optimisation marge +5%

**5. Offre appartements urbains**
- Actuellement 12% du portefeuille seulement
- Produit dédié jeunes actifs urbains (Formule Eco+)
- **Impact estimé :** Nouveau segment +3000 contrats/an

---

## 🛠️ Technologies

- **SQLite** : base de données relationnelle
- **SQL** : requêtes d'analyse (jointures, agrégations, sous-requêtes)
- **DB Browser for SQLite** : interface de gestion

---

## 📂 Structure du projet

```
.
├── README.md                      # Documentation du projet
├── data/
│   └── db_immobilier.db           # Base SQLite (50K+ contrats)
├── docs/
│   ├── requetes_sql.pdf           # Document technique avec requêtes
│   ├── methodologie.pdf           # Méthodologie d'analyse
│   └── dictionnaire.xlsx          # Dictionnaire des données
└── images/
    └── schema_relationnel.png     # Schéma de la base
```

---

## 🚀 Installation et utilisation

### Prérequis

```bash
# Télécharger DB Browser for SQLite
https://sqlitebrowser.org/
```

### Lancer l'analyse

```bash
git clone https://github.com/Heltondsm/sql-assurances-habitation.git
cd sql-assurances-habitation

# Ouvrir la base avec DB Browser
open data/db_immobilier.db  # macOS
# ou double-clic sur db_immobilier.db (Windows/Linux)
```

---

## 📊 Aperçu du code SQL

### Analyse géographique

```sql
-- Top 10 départements par nombre de contrats
SELECT
    r.nom_departement,
    COUNT(c.id_contrat) as nb_contrats,
    ROUND(AVG(c.cotisation), 2) as cotisation_moyenne,
    ROUND(SUM(c.cotisation), 2) as ca_total
FROM Contrat c
INNER JOIN Region r ON c.Code_dep_code_commune = r.Code_dep_code_commune
GROUP BY r.nom_departement
ORDER BY nb_contrats DESC
LIMIT 10;
```

### Segmentation par surface

```sql
-- Répartition des contrats par tranche de surface
SELECT
    CASE
        WHEN surface < 50 THEN '< 50 m²'
        WHEN surface BETWEEN 50 AND 100 THEN '50-100 m²'
        WHEN surface BETWEEN 100 AND 150 THEN '100-150 m²'
        ELSE '> 150 m²'
    END as tranche_surface,
    COUNT(*) as nb_contrats,
    ROUND(AVG(cotisation), 2) as cotisation_moyenne
FROM Contrat
GROUP BY tranche_surface
ORDER BY cotisation_moyenne DESC;
```

### Analyse de rentabilité

```sql
-- Départements les plus rentables (CA par contrat)
SELECT
    r.nom_departement,
    COUNT(c.id_contrat) as nb_contrats,
    ROUND(SUM(c.cotisation) / COUNT(c.id_contrat), 2) as ca_par_contrat,
    ROUND(SUM(c.cotisation), 2) as ca_total
FROM Contrat c
INNER JOIN Region r ON c.Code_dep_code_commune = r.Code_dep_code_commune
GROUP BY r.nom_departement
HAVING nb_contrats >= 100  -- Départements significatifs seulement
ORDER BY ca_par_contrat DESC
LIMIT 10;
```

---

## 📈 Compétences démontrées

### Techniques SQL
- ✅ Requêtes complexes (SELECT, WHERE, ORDER BY, LIMIT)
- ✅ Jointures entre tables (INNER JOIN)
- ✅ Agrégations (COUNT, SUM, AVG, MIN, MAX)
- ✅ Regroupements conditionnels (GROUP BY, HAVING)
- ✅ Sous-requêtes et requêtes imbriquées
- ✅ CASE statements pour segmentation

### Business acumen
- ✅ Traduction de questions business en requêtes SQL
- ✅ Identification d'opportunités de croissance
- ✅ Recommandations stratégiques chiffrées
- ✅ Segmentation clients et analyse de rentabilité

---

## 📧 Contact

**Helton Dos Santos Moreira**
Data Analyst | 10 ans d'expérience Business (retail + e-commerce) → Reconversion Data

- 📧 Email : heltonmail8@gmail.com
- 💼 LinkedIn : [in/helton-dsm-data](https://linkedin.com/in/helton-dsm-data)
- 🐙 GitHub : [Heltondsm](https://github.com/Heltondsm)

---

## 🔗 Autres projets

- [Prévision SARIMA des ventes e-commerce](https://github.com/Heltondsm/ecommerce-sales-analysis-sarima) — Séries temporelles, grid search sur 64 modèles, RMSE ±12%
- [Sous-nutrition mondiale — Données FAO](https://github.com/Heltondsm/etude-sante-publique-fao) — Python, 4 datasets, 528M personnes, conclusion : problème de distribution
- [Performance e-commerce — Le Grand Marché](https://github.com/Heltondsm/analyse-ventes-ecommerce) — Excel, KPIs, trafic ×30, segmentation 2 profils

---

**Projet réalisé en février 2026**
