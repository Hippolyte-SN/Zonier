# Zonier - Modélisation, Lissage et Classification

## Objectif : 
Mettre en lumière de façon précise l’effet géographique dans la répartition du risque en assurance santé :
- Une première partie consacrée à l’élaboration d’un modèle
- Une seconde partie, consistant à la mise en place d’une méthodologie de lissage des résidus
- Une troisième partie, pour la classification des résidus lissés afin de déterminer des groupes de bassins de vie, à la fois homogènes en leur sein et hétérogènes entre eux

---

## Modélisation
Cette section décrit les différentes approches utilisées pour modéliser le montant des sinistres et améliorer la qualité de prédiction.
L’ensemble de ces approches permet de :
* comparer modèles statistiques et machine learning
* sélectionner les meilleures prédictions
* alimenter les étapes suivantes (lissage et classification)

### Les modèles utilisés :
Approche GLM
- loi Gamma 

Approche Machine learning
- Méthode CART
- Méthode du Gradient Boosting
- Méthode Random Forest

### Évaluation des modèles
Les performances sont évaluées via une erreur de prédiction (type RMSE) sur des jeux train / test :
* Erreur de prédiction (RMSE)
* Performance sur données de test
* Stabilité des résultats

--- 

## Lissage
Il permet de :

* réduire la volatilité des zones peu exposées
* intégrer l’information spatiale
* produire des estimations de risque plus robustes

### Choix de la granularité

Par cohérence avec la granularité des données externes: à l’échelle des **bassins de vie**.

### Approche : Lissage par distance (théorie de la crédibilité)

On note $R_i$ le **risque lissé** associé au bassin de vie $i$ :

$$
R_i = Z_i \cdot r_i + (1 - Z_i) \cdot \bar{r}_i
$$

### Définitions

* $r_i$ : risque initial (résidu) du bassin de vie $i$ (avant lissage)
* $Z_i$ : facteur de crédibilité du bassin $i$

$$
Z_i = \left( \frac{w_i}{w_i + a} \right)^m
$$

* $w_i$ : exposition du bassin de vie $i$
* $a$ : paramètre de pénalisation de la crédibilité

$$
a = \frac{1}{w_i}
$$

### Risque collectif

Le risque collectif $\bar{r}_i$ est défini comme une moyenne pondérée des bassins voisins :

$$
\bar{r}*i =
\frac{
\sum*{j \ne i} \frac{r_j \cdot w_j}{d_{i,j}^p}
}{
\sum_{j \ne i} \frac{w_j}{d_{i,j}^p}
}
$$

Avec :

- $d_{i,j}$ : distance euclidienne entre les bassins $i$ et $j$
- $p$ : paramètre contrôlant l’influence de la distance
- $w_j$ : exposition du bassin $j$
- $r_j$ : risque du bassin $j$

### Paramétrisation

Les paramètres ont été calibrés empiriquement :

* $m \in {0.5, 1, 2}$
* $p \in {1, 1.5, 2}$

Ces tests permettent d’ajuster :

* le **niveau de crédibilité individuelle**
* l’**influence spatiale des bassins voisins**

---

## Classification

La classification repose sur :

les résidus lissés issus des étapes précédentes
des variables explicatives synthétisées via des analyses en composantes principales (ACP) :
- variables fonctionnelles
- variables de services
- variables démographiques
Ces informations sont fusionnées pour obtenir une base enrichie décrivant chaque bassin de vie.

### Méthode de classification

Une Classification Ascendante Hiérarchique (CAH) est utilisée, avec la méthode de Ward.
Cette approche permet de :
- minimiser la variance au sein des classes
- maximiser les différences entre classes
Le nombre de classes est déterminé à partir de l’analyse de l’inertie, afin d’obtenir un compromis entre lisibilité et précision.

### Construction du zonier

Chaque bassin de vie est ensuite affecté à une classe, définissant une zone homogène de risque.
Ces classes sont croisées avec les coordonnées géographiques (barycentres) afin de permettre leur représentation spatiale.
Les classes obtenues sont réordonnées en fonction d’un indicateur métier (montant moyen), afin de donner une lecture cohérente du zonier :
* classes faibles -> risque faible
* classes élevées -> risque élevé

---

## Technologies

* R(4.4.3)
* tidyverse
* dplyr
* questionr
* ggplot2
* caret
* gbm
* rpart
* randomForest
* tidyr
* cowplot
* maps
* classInt
* openxlsx
* readr
* FactoMineR
* factoextra
* stats

--- 

## Données

- data_sim.CSV
- correspondance_code_insee_code_postal_codebv.CSV
- Variables_externes.xlsx
- Composition_communale.xlsx

Données sauvegardées à chaque étapes : 
* error_frame.RData
* table_lissage

---

## Images

- img_mtt_total
- img_performance_mdl
- img_lissage1
- img_lissage2
- img_lissage3
- img_zonier

--- 

