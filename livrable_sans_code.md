# RECHERCHE OPERATIONNELLE - PROJET 
#### Groupe 2 - Mattéo BOULITEAU -  Kagnana ITH - Nejma MOUALHI

# Table des matières

- [Contexte](#contexte)
- [Problématique](#problématique)
- [Contraintes](#contraintes)
- [Livrables attendus](#livrables-attendus)
- [Caractéristiques du problème](#caractéristiques-du-problème)
- [1. Modélisation du TSP](#1--modélisation-du-tsp)
  - [1.1 Données](#11-données)
  - [1.2 Variables de décision](#12-variables-de-décision)
  - [1.3 Fonction objectif](#13-fonction-objectif)
  - [1.4 Contraintes](#14-contraintes)
- [2. Méthodes de résolution](#2--méthodes-de-résolution)
  - [2.1 Méthode exacte : PLNE (Programme Linéaire en Nombres Entiers)](#21-méthode-exacte--plne-programme-linéaire-en-nombres-entiers)
    - [2.1.1 Description de la méthode PLNE](#211-description-de-la-méthode-plne)
    - [2.1.2 Solveur Open Source (PuLP)](#212-solveur-open-source-pulp)
    - [2.1.3 Génération des données](#213-génération-des-données)
    - [Analyse des résultats](#analyse-des-résultats)
    - [2.1.3 Résultats de la méthode PLNE](#213-résultats-de-la-méthode-plne)
  - [2.2 Méthodes heuristiques](#22-méthodes-heuristiques)
    - [2.2.1 Heuristiques classiques pour le TSP](#221-heuristiques-classiques-pour-le-tsp)
    - [2.2.2 Algorithme du plus proche voisin (Nearest Neighbor Algorithm)](#222-algorithme-du-plus-proche-voisin-nearest-neighbor-algorithm)
  - [2.3 Métaheuristiques](#23-métaheuristiques)
    - [2.3.1 Méthodes de voisinage](#231-méthodes-de-voisinage)
    - [2.3.2 Métaheuristique GRASP](#232-métaheuristique-grasp)
- [3. Plan d'expérience](#3-plan-dexpérience)
  - [3.1 Objectifs](#31-objectifs)
  - [3.2 Paramètres étudiés](#32-paramètres-étudiés)
  - [3.3 Métriques](#33-métriques)
  - [3.4 Protocole](#34-protocole)
  - [3.5 Analyses prévues](#35-analyses-prévues)
- [4. Étude expérimentale de la qualité](#4-étude-expérimentale-de-la-qualité)
  - [4.1 Comparaison sur même instance fixe](#41-comparaison-sur-même-instance-fixe)
    - [4.1.1 Analyse comparative des performances](#411-analyse-comparative-des-performances)
  - [4.2 Résultats globaux](#42-résultats-globaux)
    - [4.2.1 Statistiques globales par méthode](#421-statistiques-globales-par-méthode)
    - [4.2.2 Statistiques par taille d'instance](#422-statistiques-par-taille-dinstance)
    - [4.2.3 Statistiques GRASP par paramètres](#423-statistiques-grasp-par-paramètres)
  - [4.3 Comparaison des performances de PLNE et GRASP](#43-comparaison-des-performances-de-plne-et-grasp)
    - [4.3.1 Tableau comparatif PLNE vs GRASP](#431-tableau-comparatif-plne-vs-grasp)
  - [4.4 Synthèse finale des résultats](#44-synthèse-finale-des-résultats)
- [5. Conclusion](#5-conclusion)
- [Bibliographie](#bibliographie)




## Contexte
L’ADEME a lancé un appel à manifestation d’intérêt pour expérimenter de nouvelles solutions de mobilité adaptées aux territoires.

Nous sommes CesiCDP, déjà engagés dans la mobilité multimodale intelligente, et nous répondons à cet appel pour obtenir de nouveaux financements. Le projet vise à réduire les déplacements et la consommation des véhicules lors des livraisons, en résolvant un problème algorithmique d’optimisation de tournée sur un réseau routier.

## Problématique
Nous nous demandons comment optimiser les tournées de livraison pour réduire les déplacements et la consommation des véhicules. Nous devons prendre en compte plusieurs contraintes, telles que la capacité des véhicules, les fenêtres de temps pour les livraisons, et la distance entre les points de livraison.

## Contraintes
Nous avons décidé de rajouter des contraintes pour rendre le problème plus réaliste en se basant sur un système de livraison de colis. Voici les contraintes que nous avons retenues :
1. **Dépendances entre les visites** : Une ville ne peut être visitée qu’après une autre.
    - Définir un ordre de visite entre les villes.
2. **Routes dynamiques ou perturbations** : Les routes peuvent changer pendant la tournée (ex : accidents, météo, fermeture soudaine).
    - Simuler des perturbations dans les données.

## Livrables attendus
1. Modélisation

2. Livrable final du projet


## Caractéristiques du problème

- **Variables de décision** : $x_{ij}$  
    $x_{ij}$ représente si le trajet entre la ville $i$ et la ville $j$ est emprunté ou non.

- **Contraintes** : 
    - Chaque ville doit être visitée une seule fois.
    - Le point de départ et d'arrivée doit être le même.
    - Dépendances entre les visites : une ville ne peut être visitée qu’après une autre.
    - Routes dynamiques et perturbations : Prévision de perturbations dans les données.

- **Objectif** : Trouver un itinéraire optimisé qui nous permet de passer par toutes les villes de la tournée en une seule fois et retourner au point de départ.

Une fois que nous avons explicité le problème, nous allons le modéliser de manière mathématique.  
Nous remarquons que notre problème est une version métrique du problème du voyageur de commerce (TSP) avec des contraintes supplémentaires le rendant plus réaliste.

### Version algorithmique du problème :

- **Donnée** : Un graphe complet pondéré $G(S, A)$ avec un ensemble de sommets $S$ et un ensemble d'arêtes $A$.
- **Tâche** : Trouver un cycle hamiltonien dans $G$ en minimisant la somme des poids des arêtes du cycle.

## 1 - Modélisation du TSP

### 1.1 Données

- Un ensemble de villes $V = \{v_1, v_2, \ldots, v_n\}$
- Un graphe complet pondéré $G = (V, E)$
- Une fonction de coût $c_{ij} \geq 0$, représentant la distance ou le temps entre les villes $i$ et $j$

La nature du graphe est la suivante :
- On reste sur un graphe complet : on suppose qu'il existe une route (au moins théorique) entre chaque paire de villes. 

### 1.2 Variables de décision

- $x_{ij} \in \{0,1\}$ : vaut 1 si l'on va de la ville $i$ à la ville $j$, 0 sinon

###     

Minimiser la somme des coûts de déplacement :

$$\min \sum_{i=1}^{n} \sum_{j=1}^{n} c_{ij} \cdot x_{ij}$$

### 1.4 Contraintes

1. Chaque ville est quittée une seule fois :

$$\sum_{j=1}^{n} x_{ij} = 1 \quad \forall i \in \{1, \dots, n\}$$

2. Chaque ville est visitée une seule fois :

$$\sum_{i=1}^{n} x_{ij} = 1 \quad \forall j \in \{1, \dots, n\}$$

3. Dépendances entre les visites : <br>

$$\forall (i,j) \in D, \quad x_{ij} = 1 \implies x_{ji} = 0$$

$D \subseteq S \times S$ est l'ensemble des dépendances entre les visites. <br>
Une contrainte $(i,j) \in D$ signifie que la ville $j$ ne peut être visitée qu'après la ville $i$. 

4. Routes dynamiques ou perturbations :
$$\forall i,j \in \{1, \dots, n\}, \quad c_{ij} = c_{ij}(t)$$
où $c_{ij}(t)$ est la distance entre les villes $i$ et $j$ à l'instant $t$. Cette contrainte permet de prendre en compte les changements de conditions de circulation.

5. Le point de départ et d'arrivée doit être le même :
$$\sum_{i=1}^{n} x_{0i} = 1$$
$$\sum_{j=1}^{n} x_{j0} = 1$$

---

Ce problème est connu pour être **NP-difficile** : il n'existe pas d'algorithme polynomial connu permettant de le résoudre dans le cas général.
Nous allons nous intéresser à des algorithmes d'approximation, ou méta-heuristiques, qui permettent de trouver des solutions approchées en un temps raisonnable.

## 2 - Méthode de résolution

Dans un premier temps, nous allons aborder la méthode exacte, qui est la méthode de programmation linéaire en nombres entiers (PLNE).

### 2.1 Méthode exacte : Programme linéaire en nombres entiers (PLNE)

#### 2.1.1 Description de la méthode PLNE

La méthode PLNE consiste à modéliser le problème sous forme de programme linéaire, en utilisant des variables de décision binaires pour représenter les choix de parcours entre les villes.

#### 2.1.2 Solveur Open Source (PuLP)

Nous allons utiliser PuLP, une bibliothèque Python, combinée avec un solveur open source pour résoudre le problème d'optimisation linéaire et entière. PuLP permet de modéliser le problème et d'utiliser différents solveurs pour trouver des solutions optimales ou approchées à des problèmes complexes.

Nous allons générer les données pour le problème du TSP, puis utiliser PuLP avec un solveur open source pour résoudre le problème.

### 2.1.3 Génération des données

Pour créer des instances réalistes du problème de tournées de livraison, nous avons développé un générateur de données qui simule les conditions réelles d'un réseau de distribution.

#### Paramètres clés

- **Temps de trajet de base**  
    - Chaque route a un temps entre 1 et 300 minutes.  
    - Correspond aux distances réelles en milieu urbain.

- **Perturbations dynamiques**  
    - 20% des routes subissent des perturbations.  
    - Augmentation jusqu'à +20% du temps initial.  
    - Simule : embouteillages, travaux, accidents.

- **Dépendances entre livraisons**  
    - 20% des villes ont des contraintes d'ordre.  (ex : 2 villes sur 10)
    - Exemple : collecte avant livraison.  
    - Système anti-cycle pour éviter les boucles et l'inaccessibilité. (ex : A dépend de B, B dépend de C, C dépend de A donc A ne peut pas être visitée avant B et C et B et C ne peuvent pas être visitées avant A)

#### Mécanisme de génération

- **Graphe complet** : Toutes les villes sont connectées.  
- **Poids aléatoires** : Distribution uniforme des temps.  
- **Perturbations sélectives** : Choix aléatoire des routes affectées.  
- **Dépendances validées** : Vérification automatique des cycles.

Cette approche garantit des instances à la fois réalistes (reflet des contraintes terrain) et résolubles mathématiquement.

Pour nous permettre de visualiser les résultats, nous allons également utiliser la bibliothèque Matplotlib pour tracer le graphe du TSP et afficher la solution trouvée par le solveur.

![graph TSP ](image.png)


Ensuite, nous allons utiliser PuLP, une bibliothèque Python de modélisation linéaire, pour résoudre le problème du TSP en utilisant la programmation linéaire en nombres entiers (PLNE). PuLP permet d'utiliser différents solveurs, et nous utiliserons un solveur open source pour résoudre le modèle.

Il faut installer le package `pulp` pour utiliser PuLP.

### Analyse des résultats
En prenant un petit échantillon de données, nous avons trouvé une solution optimale en un court temps. Cependant, si nous augmentons la taille de l'instance, le temps de calcul augmente de façon exponentielle, et nous pourrions ne pas trouver de solution optimale dans un temps raisonnable (par exemple, plusieurs heures ou jours). De plus, la méthode PLNE ne garantit pas de solution optimale pour des instances de grande taille, car elle peut être bloquée par la complexité combinatoire du problème. Il est donc crucial d'utiliser des méthodes alternatives pour les grandes instances.

Testons avec différentes tailles d'instances pour voir comment le temps de calcul varie. Nous allons également introduire une limite de temps maximale pour chaque instance, afin de mieux illustrer les limitations de la méthode exacte.

![croissance exponentielle PLNE](image-1.png)


Résumé des résultats:

| Taille | Temps   | Statut  | Objectif | Optimal |
|--------|---------|---------|----------|---------|
| 5      | 0.13s   | Résolu  | 13.43    | True    |
| 8      | 0.09s   | Résolu  | 9.47     | True    |
| 10     | 0.12s   | Résolu  | 12.12    | True    |
| 15     | 0.20s   | Résolu  | 7.80     | True    |
| 20     | 6.95s   | Résolu  | 8.93     | True    |
| 25     | 50.67s  | Résolu  | 9.67     | True    |
| 30     | 10.22s  | Résolu  | 8.95     | True    |
| 35     | 1.36m   | Résolu  | 9.53     | True    |

Projections pour des instances plus grandes:

| Taille | Temps estimé  |
|--------|---------------|
| 40     | 2.6 minutes   |
| 50     | 10.6 minutes  |
| 75     | 6.1 heures    |
| 100    | 8.7 jours     |

Points clés de la performance:
• Le temps de calcul double approximativement tous les 4.9 villes supplémentaires
• La croissance est exponentielle avec un facteur de 0.1416
• Coefficient de détermination (R²): 0.721

Limites pratiques estimées:
• Au-delà de 33.4 villes, le temps de calcul dépasse 1 minute
• Au-delà de 62.3 villes, le temps de calcul dépasse 1 heure
• Au-delà de 84.7 villes, le temps de calcul dépasse 1 jour


### 2.1.3 Résultats de la méthode PLNE

L'analyse des résultats démontre clairement les limites de la méthode PLNE pour le TSP :

- **Explosion combinatoire observée** : Comme le montrent les graphiques ci-dessus, le temps de calcul augmente de façon exponentielle avec le nombre de villes. Par exemple, on passe d'environ 0.03 secondes pour 5 villes à plus de 20 secondes pour 25 villes.

- **Comportement exponentiel confirmé** : La courbe de régression exponentielle présente un excellent ajustement aux données observées (R² > 0.9), confirmant la nature exponentielle de la complexité temporelle.

- **Seuils critiques identifiés** :
  - À environ 15 villes : le temps dépasse la seconde
  - À environ 30 villes : le temps approche la minute
  - Projection : au-delà de 50 villes, le temps de calcul devient prohibitif.

- **Variabilité des données** : Malgré la complexité algorithmique exponentielle, certaines instances peuvent être résolues plus rapidement que d'autres de même taille, en fonction de la structure spécifique du graphe et des contraintes de dépendance. Cela montre que la difficulté du problème peut varier considérablement d'une instance à l'autre.

Cette analyse quantitative justifie pleinement le recours aux métaheuristiques pour les instances de taille moyenne à grande, où les méthodes exactes deviennent impraticables en raison de leur temps de calcul qui croît exponentiellement avec le nombre de villes.

## 2.2 Méthode heuristique

Après avoir étudié la méthode exacte (PLNE), nous allons maintenant nous intéresser aux méthodes heuristiques. Ces méthodes proposent des solutions approchées, mais en un temps de calcul considérablement réduit par rapport aux méthodes exactes.

### 2.2.1. Heuristiques classiques pour le TSP

Les heuristiques classiques pour le TSP (Traveling Salesman Problem) incluent des approches gloutonnes comme l'algorithme du plus proche voisin. Ces méthodes sont rapides, mais elles présentent des limitations théoriques importantes :

*   **Optimalité locale vs. globale**: Les heuristiques gloutonnes optimisent localement à chaque étape, sans garantie de convergence vers un optimum global.
*   **Complexité algorithmique et NP-complétude**: Le TSP est un problème NP-complet, ce qui signifie qu'il n'existe pas d'algorithme polynomial connu pour le résoudre de manière exacte. Même avec des heuristiques, la taille de l'espace de recherche rend les approches déterministes classiques inopérantes pour des instances de grande taille. 
*   **Absence de garanties théoriques**: Aucune heuristique simple ne peut assurer à la fois un facteur d'approximation constant (sous réserve que $P \neq NP$) et une exploration complète des régions prometteuses de l'espace de solutions.

### 2.2.2 Algorithme du plus proche voisin (Nearest Neighbor Algorithm)

L'algorithme du plus proche voisin est une heuristique gloutonne classique pour le TSP. Il consiste à partir d'une ville de départ et à visiter à chaque étape la ville la plus proche non encore visitée, jusqu'à revenir à la ville de départ.

**Principe de l'algorithme :**
1. Choisir une ville de départ
2. Identifier la ville non visitée la plus proche
3. Se déplacer vers cette ville
4. Répéter les étapes 2-3 jusqu'à ce que toutes les villes soient visitées
5. Retourner à la ville de départ


**Limites de l'algorithme du plus proche voisin :**

L'algorithme du plus proche voisin peut donner des résultats acceptables pour de très petites instances du TSP (par exemple, 4 ou 5 villes). Cependant, comme il s'agit d'une heuristique gloutonne, il ne garantit pas l'optimalité, même pour ces petites instances. De plus, sa performance se dégrade rapidement avec l'augmentation du nombre de villes.

Le TSP est un problème NP-difficile. Cela signifie que le temps de calcul nécessaire pour trouver la solution optimale augmente de manière exponentielle avec la taille du problème.

Comme nous l'avons observé avec la méthode PLNE, le temps de calcul devient prohibitif pour des instances de grande taille. De même, l'algorithme du plus proche voisin, bien que rapide, ne peut pas garantir une solution de qualité acceptable pour des problèmes de taille réaliste. 


Nous allons donc explorer des méthodes plus avancées, telles que les métaheuristiques, qui combinent des éléments de recherche locale et globale pour trouver des solutions de haute qualité dans un temps raisonnable.

## 2.3. Métaheuristiques

Les métaheuristiques sont des stratégies algorithmiques de haut niveau conçues pour résoudre des problèmes d'optimisation complexes, en particulier ceux pour lesquels les méthodes exactes sont inefficaces. Elles combinent des heuristiques de base avec des mécanismes d'exploration et d'exploitation pour échapper aux optima locaux et explorer l'espace de recherche de manière plus efficace.

Cependant, il est important de noter que les métaheuristiques ne garantissent pas toujours la convergence vers une solution optimale. Elles sont souvent utilisées pour obtenir des solutions approchées dans des délais raisonnables, mais elles peuvent également être adaptées pour fournir des garanties de performance dans certains cas.

**Types de métaheuristiques :**

*   **Recherche locale itérative** : Part d'une solution initiale et explore itérativement le voisinage pour trouver une meilleure solution. Exemples : descente de gradient, recuit simulé (Simulated Annealing).
*   **Algorithmes évolutionnaires** : S'inspirent des mécanismes de l'évolution biologique (sélection, mutation, croisement) pour faire évoluer une population de solutions. Exemple : algorithmes génétiques.
*   **Algorithmes basés sur la mémoire** : Utilisent une mémoire pour stocker les solutions déjà visitées et guider la recherche. Exemple : recherche tabou (Tabu Search).
*   **Algorithmes inspirés de la nature** : Imitent des phénomènes naturels ou des comportements collectifs. Exemples : optimisation par colonies de fourmis (Ant Colony Optimization), optimisation par essaims de particules (Particle Swarm Optimization).

### 2.3.1. Méthodes de voisinnage
Les méthodes de voisinage est une notion clé dans les métaheuristiques. Elles consistent à explorer l'espace de recherche en se déplaçant d'une solution à une autre en modifiant légèrement la solution actuelle. Ces modifications sont appelées "voisinages".

Définition : Soit $X$ l'ensemble des configurations possibles d'une solution. On appelle _voisinage_ toute application $N : $X \to X$ qui associe à chaque solution $x \in X$ un ensemble de solutions voisines $N(x)$.
Une solution $x$ est dite _optimum local_ si aucune solution voisine $x'$ n'est meilleure que $x$ selon la fonction objectif.

Une méthode typique de voisinage débute avec une configuration initiale $x_0$ et explore les solutions voisines en appliquant la fonction de voisinage $N$. À chaque itération, la méthode choisit une solution voisine $x' \in N(x)$ et l'évalue. Si $x'$ est meilleure que $x$, elle devient la nouvelle solution courante. Ce processus se répète jusqu'à ce qu'aucune amélioration ne soit trouvée. 

L'un des principaux avantages des méthodes de voisinage est leur simplicité et leur flexibilité. Elles permettent de contrôler le temps de calcul, car l'exploration du voisinage peut être interrompue à tout moment. Cependant, elles sont souvent limitées par leur tendance à se coincer dans des optima locaux. C'est pourquoi elles sont souvent utilisées en combinaison avec d'autres techniques, comme la randomisation ou les mécanismes de diversification, pour échapper à ces optima locaux.

### 2.3.2. Métaheuristique GRASP
Nous avons choisi GRASP (Greedy Randomized Adaptive Search Procedure) pour les raisons suivantes :

*   **Simplicité et efficacité**: GRASP est relativement simple à implémenter et peut fournir de bonnes solutions en un temps raisonnable.
*   **Flexibilité**: GRASP peut être adapté à différents types de problèmes d'optimisation combinatoire.
*   **Capacité à échapper aux optima locaux**: La phase de construction randomisée et la recherche locale permettent à GRASP d'explorer différentes régions de l'espace de recherche.
*   **Preuves empiriques**: GRASP a été appliqué avec succès à de nombreux problèmes de TSP et de VRP (Vehicle Routing Problem).


##### Justification théorique du recours aux métaheuristiques

1.  **Théorème de non-linéarité (No Free Lunch)**: Aucun algorithme d'optimisation ne surpasse universellement les autres sur tous les problèmes. Les métaheuristiques offrent une flexibilité stratégique pour adapter la recherche aux spécificités du problème (exploration/exploitation).

2.  **Réduction des biais de recherche**: Contrairement aux heuristiques déterministes, GRASP intègre :

    *   **Randomisation contrôlée**: Construction de solutions via des listes restreintes de candidats (RCL), évitant les choix prématurés.
    *   **Intensification/diversification**: Phase de recherche locale couplée à une multi-exécution pour couvrir divers bassins d'attraction.

Exemple d'implémentation de la solution :

![graph animated](animation.gif)

Parfois la solution initiale est déjà la meilleure solution.

Nous allons calculer le temps d'exécution à partir du nombre d'itérations et de la taille de l'instance.

Test avec 10 nœuds...
  Temps: 0.00s, Coût: 769.00
Test avec 20 nœuds...
  Temps: 0.03s, Coût: 899.00
Test avec 50 nœuds...
  Temps: 0.06s, Coût: 1475.00
Test avec 100 nœuds...
  Temps: 3.26s, Coût: 2312.00

![temps exec GRASP](image-3.png)

Nous remarquons que plus il y a d'itérations, plus le temps d'exécution est long. Cependant ce dernier relativement court par rapport à la méthode PLNE. 

#### Complexité de GRASP

La complexité de GRASP est de :
- Complexité globale
    - O(max_iterations_grasp · max_iterations_local · n² · log(d))
    - max_iterations_grasp : nombre d'itérations externes de GRASP
    - max_iterations_local : nombre d'itérations de la recherche locale
    - n : nombre de villes
    - d : nombre de dépendances

Décomposition de la complexité

- Phase de construction initiale : O(n²)
    - Parcours des n villes, chacune nécessitant O(n) comparaisons pour sélectionner le prochain nœud

- Recherche locale (2-opt optimisée) : O(max_iterations · n² · log(d))
    - O(n²) paires d'arêtes à considérer pour l'échange
    - O(1) pour l'inversion d'un segment grâce aux pointeurs intelligents
    - O(log(d)) pour vérifier uniquement les contraintes affectées (avec indexation efficace)

- Vérification des dépendances : O(log(d))
    - Utilisation d'un index optimisé pour ne vérifier que les dépendances pertinentes
    
- Mise à jour des structures de données : O(1) par opération

Dictionnaire de positions des nœuds pour des accès et mises à jour en O(1)

## 3. Plan d'expérience

### 3.1 Objectifs
Comparer les performances de GRASP vs PLNE pour résoudre le TSP avec contraintes en analysant :
- Impact des paramètres GRASP sur qualité et temps
- Impact de la taille des instances
- Limites pratiques de chaque méthode

### 3.2 Paramètres étudiés
| Paramètre | Valeurs testées |
|-----------|----------------|
| Alpha (GRASP) | 0.1, 0.3, 0.5, 0.7 |
| Itérations (GRASP) | 3, 5, 10, 20 |
| Taille (villes) | 10, 20, 30, 50, 100 |
| Taux dépendances | 10%, 20%, 30% |
| Perturbations | 0%, 10%, 20% |

### 3.3 Métriques
- Temps d'exécution (moyenne, écart-type)
- Qualité de la solution (coût)
- Gap par rapport à l'optimal (quand disponible)
- Taux de succès PLNE

### 3.4 Protocole
1. Générer instances aléatoires
2. Exécuter PLNE (limite 60s) et GRASP
3. Collecter métriques pour chaque exécution
4. Analyser statistiquement les résultats

### 3.5 Analyses prévues
- Impact des paramètres GRASP (ANOVA)
- Comparaison PLNE vs GRASP (temps, qualité)
- Identification du seuil de viabilité PLNE
- Recommandations par taille d'instance

## 4. Étude expérimentale de la qualité

Cette section présente les résultats de nos expériences comparant GRASP et PLNE. Nous analysons l'impact des paramètres sur les performances et fournissons des recommandations pratiques basées sur des analyses statistiques rigoureuses.

### 4.1 Comparaison sur même instance fixe
Nous allons tester GRASP sur une instance spécifique avec une graine aléatoire fixe, et rapporter la meilleure solution trouvée parmi toutes les configurations. Puis, on va comparer cette solution avec la solution optimale trouvée par PLNE et voir l'écart entre la solution trouvée par GRASP et la solution optimale trouvée par PLNE.


![Comparaison des couts](image-4.png)
![Comparaison des temps](image-5.png)

### 4.1.1 Analyse comparative des performances

Les deux graphiques ci-dessus comparent les méthodes PLNE (exacte) et GRASP (approximative) en termes de **coût des solutions** et de **temps d'exécution** pour différentes tailles d'instances.

- **Coût (qualité de la solution)** :  
  GRASP donne des solutions aussi bonnes que PLNE pour les petites tailles (10 villes), mais l’écart de qualité augmente avec la taille (jusqu’à +36% pour 30 villes).

- **Temps d’exécution** :  
  GRASP est **beaucoup plus rapide** que PLNE. L’écart devient énorme dès 30 villes, avec un facteur x1187 en faveur de GRASP.

Nous pouvons vite remarquer que PLNE est plus précis mais devient vite inutilisable pour des grandes tailles. GRASP, bien qu’approximatif, est **beaucoup plus scalable** et reste **très rapide**. Il est donc préférable pour de grandes instances.

Les résultats les plus proches de la solution optimale sont obtenus avec un alpha de 0.1 et une itération qui augmente avec la taille de l'instance comme l'on peut le voir sur les graphiques ci-dessus.

![alt text](image-6.png)
![alt text](image-7.png)    


### 4.2 Résultats globaux
Nous avons testé GRASP sur plusieurs instances de TSP avec des tailles allant de 10 à 100 villes. Nous avons mesuré le temps d'exécution et la qualité de la solution trouvée par GRASP, ainsi que le gap par rapport à la solution optimale trouvée par PLNE.

Comparé aux résultats optimaux d'avant sur des instances fixes. Ici nous nous intéressons à la distribution des résultats de GRASP sur plusieurs instances aléatoires. Nous avons mesuré le temps d'exécution et la qualité de la solution trouvée par GRASP, ainsi que le gap par rapport à la solution optimale trouvée par PLNE.
Les graphs pouvant être difficiles ou faciles à résoudre cela peut expliquer la variabilité des résultats et donc le gap entre les deux méthodes.

### 4.2.1 Statistiques globales par méthode 

| Méthode | Nb d'essais | Temps moyen (s) | Écart-type temps | Min temps | Max temps | Coût moyen | Écart-type coût | Min coût | Max coût | Taux de succès |
|---------|-------------|------------------|------------------|-----------|-----------|-------------|------------------|----------|----------|----------------|
| GRASP   | 7470        | 0.271            | 0.706            | 0.00      | 11.708    | 1165.145    | 545.622          | 310.0    | 3461.0   | 1.0            |
| PLNE    | 270         | 25.225           | 66.457           | 0.05      | 299.471   | 540.822     | 94.739           | 277.0    | 875.0    | 1.0              |

**Synthèse des résultats :**
On observe que GRASP est 93x plus rapide que PLNE en moyenne. Cependant, le coût moyen de GRASP est deux fois plus élevé que celui de PLNE. Cela est dû à la nature approximative de GRASP, qui peut parfois donner des solutions sous-optimales. 
Mais dès lors que le temps d'exécution est un facteur critique, GRASP est la méthode à privilégier au détriment de la qualité de la solution.

### 4.2.2 Statistiques par taille d'instance
| Méthode | Taille | Temps moyen (s) | Écart-type temps | Coût moyen | Écart-type coût |
|---------|--------|------------------|------------------|-------------|------------------|
| GRASP   | 10     | 0.003            | 0.003            | 630.420     | 155.274          |
| GRASP   | 20     | 0.034            | 0.031            | 885.229     | 167.197          |
| GRASP   | 30     | 0.106            | 0.100            | 1190.596    | 226.820          |
| GRASP   | 50     | 0.623            | 0.595            | 1740.424    | 349.875          |
| GRASP   | 100    | 2.381            | 2.250            | 2591.226    | 239.479          |
| PLNE    | 10     | 0.245            | 0.233            | 530.078     | 104.415          |
| PLNE    | 20     | 6.564            | 12.559           | 541.144     | 99.490           |
| PLNE    | 30     | 68.866           | 101.400          | 551.244     | 78.107           |


### 4.2.3 Statistiques GRASP par paramètres
| Alpha | Itérations | Temps moyen (s) | Écart-type temps | Coût moyen | Écart-type coût |
|--------|-------------|------------------|-------------------|-------------|------------------|
| 0.1    | 3           | 0.034            | 0.060             | 1080.117    | 375.292          |
| 0.1    | 5           | 0.056            | 0.087             | 1041.242    | 370.037          |
| 0.1    | 10          | 0.121            | 0.186             | 986.661     | 345.507          |
| 0.1    | 20          | 0.247            | 0.366             | 942.478     | 326.215          |
| 0.1    | 30          | 0.362            | 0.526             | 911.997     | 310.743          |
| 0.3    | 3           | 0.230            | 0.531             | 1432.178    | 732.484          |
| 0.3    | 5           | 0.429            | 1.000             | 1380.982    | 716.602          |
| 0.3    | 10          | 0.945            | 2.021             | 1304.611    | 680.179          |
| 0.3    | 20          | 0.241            | 0.352             | 968.697     | 350.492          |
| 0.3    | 30          | 0.373            | 0.546             | 933.264     | 336.703          |
| 0.5    | 3           | 0.043            | 0.073             | 1303.203    | 547.714          |
| 0.5    | 5           | 0.075            | 0.124             | 1214.419    | 497.822          |
| 0.5    | 10          | 0.142            | 0.221             | 1131.703    | 471.551          |
| 0.5    | 20          | 0.285            | 0.423             | 1073.808    | 437.075          |
| 0.5    | 30          | 0.435            | 0.648             | 1039.117    | 423.264          |
| 0.7    | 3           | 0.052            | 0.086             | 1454.933    | 675.016          |
| 0.7    | 5           | 0.089            | 0.146             | 1357.172    | 624.036          |
| 0.7    | 10          | 0.172            | 0.260             | 1267.911    | 564.764          |
| 0.7    | 20          | 0.351            | 0.535             | 1179.511    | 533.340          |
| 0.7    | 30          | 0.537            | 0.825             | 1143.317    | 513.664          |

#### Heat map des interactions entre alpha et itérations sur le coût

![heatmap alpha itérations coût](image-8.png)

**Interprétation de la heatmap :**
On peut observer que le coût moyen de la solution trouvée par GRASP varie en fonction des paramètres alpha et du nombre d'itérations.
- **Alpha** : Un alpha plus faible (0.1) donne généralement de meilleures solutions.
- **Itérations** : Un nombre d'itérations plus élevé (20 ou 30) tend à donner de meilleures solutions.

### 4.3 Comparaison des performances de PLNE et GRASP
![Comparaison des temps d'exécution PLNE vs GRASP](image-9.png)

![Ecart de qualité GRASP vs Solution optimale PLNE](image-10.png)

### 4.3.1 Tableau comparatif PLNE vs GRASP
| Méthode | Taille | Temps moyen (s) | Temps std (s) | Coût moyen | Coût std | Taux de succès |
|---------|--------|------------------|----------------|------------|----------|----------------|
| GRASP   | 10     | 0.003            | 0.003          | 630.420    | 155.274  | 1.0            |
| GRASP   | 20     | 0.034            | 0.031          | 885.229    | 167.197  | 1.0            |
| GRASP   | 30     | 0.106            | 0.100          | 1190.596   | 226.820  | 1.0            |
| PLNE    | 10     | 0.245            | 0.233          | 530.078    | 104.415  | 1.0            |
| PLNE    | 20     | 6.564            | 12.559         | 541.144    | 99.490   | 1.0            |
| PLNE    | 30     | 68.866           | 101.400        | 551.244    | 78.107   | 1.0            |



**Synthèse des résultats :**
- PLNE donne de meilleures solutions, mais devient lent dès 20 villes (6.5s) et quasi inutilisable à 30 villes (>1min).
- GRASP reste très rapide (0.1s à 30 villes) avec un coût acceptable, bien que plus élevé qu’avec PLNE.


### 4.4 Synthèse finale des résultats

1. CONFIGURATION OPTIMALE GRASP
- Alpha: 0.1
- Itérations: 3.0
- Coût moyen: 1080.12 minutes
- Temps moyen: 0.034 secondes

2. RECOMMANDATIONS PAR TAILLE D'INSTANCE
- 10 villes: PLNE (taux de succès: 100%)
- 20 villes: PLNE (taux de succès: 100%)
- 30 villes: PLNE (taux de succès: 100%)
- 50 villes: GRASP (PLNE impraticable)
- 100 villes: GRASP (PLNE impraticable)

3. POINTS CLÉS
- Gap moyen GRASP vs optimal: 66.2%
- Speedup moyen GRASP: 305.0x

4. CONCLUSION GÉNÉRALE
- GRASP offre un excellent compromis qualité/temps
- Paramètres optimaux identifiés pour différents scénarios
- PLNE reste préférable pour petites instances (≤20 villes)
- GRASP indispensable pour grandes instances (>30 villes)


## 5. Conclusion

Ce projet a permis de modéliser un problème de tournées de livraison avec contraintes réalistes, et de comparer deux approches : une méthode exacte (PLNE) et une métaheuristique (GRASP). 

Les résultats montrent que PLNE est adaptée pour de petites instances, mais devient rapidement impraticable sur de grandes instances. GRASP, bien que moins précis, propose des solutions de qualité en un temps extrêmement réduit, ce qui le rend incontournable pour les grandes instances. 

Nous recommandons donc une approche hybride : utiliser PLNE pour les petits cas, et GRASP pour les instances plus larges avec les paramètres optimisés identifiés (alpha = 0.1, itérations = 20 à 30).


# Bibliographie

1. **Voyageur de commerce - Approche métrique**  
    [https://www.lri.fr/~hellouin/Agreg/Approx-TSP-m%C3%A9trique.pdf](https://www.lri.fr/~hellouin/Agreg/Approx-TSP-m%C3%A9trique.pdf)

2. **Recherche opérationnelle - F. Olive**  
    [https://pageperso.lis-lab.fr/~frederic.olive/Materiel/roM1/cours.pdf](https://pageperso.lis-lab.fr/~frederic.olive/Materiel/roM1/cours.pdf)

3. **Recherche opérationnelle - Nicolas Bousquet**  
    [https://perso.liris.cnrs.fr/nbousquet/ECL_RO/ro_bousquet.pdf](https://perso.liris.cnrs.fr/nbousquet/ECL_RO/ro_bousquet.pdf)

4. **Le voyageur de commerce**  
    [http://polymorphe.free.fr/cours/ia/tsp/these_chap_4(TSP).pdf](http://polymorphe.free.fr/cours/ia/tsp/these_chap_4(TSP).pdf)


5. **Méthodes de résolution exactes, heuristiques et métaheuristiques**  
    [http://www.fsr.ac.ma/DOC/cours/maths/Souad%20Bernoussi/Cours%20C2SI.pdf](http://www.fsr.ac.ma/DOC/cours/maths/Souad%20Bernoussi/Cours%20C2SI.pdf)

6. **Méthaheuristiques pour l’optimisation combinatoire et l’affectation sous contraintes**
[https://www.researchgate.net/publication/220724882_Methaheuristiques_pour_l'optimisation_combinatoire_et_l'affectation_sous_contraintes](https://www.researchgate.net/publication/220724882_Methaheuristiques_pour_l'optimisation_combinatoire_et_l'affectation_sous_contraintes)

7. **ChatGPT, Claude, et Perplexity on était utilisés en tant qu'outils d'aide à la rédaction et à la recherche d'informations.**  
    [https://www.openai.com/](https://www.openai.com/)  
    [https://claude.ai/](https://claude.ai/)  
    [https://www.perplexity.ai/](https://www.perplexity.ai/)

