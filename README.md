# Gestion d'actifs et data mining sur le S&P 500

Projet du cours Gestion d'actifs et data, M2 ISIFAR (Université Paris Cité). On teste une stratégie de suivi de tendance (type CTA) fondée sur des moyennes mobiles, d'abord sur 15 grandes actions du S&P 500, puis sur des facteurs obtenus par analyse en composantes principales (ACP). On étudie enfin la structure de corrélation de ces actions avec un graphe et un arbre couvrant minimal.

Tout le code est dans `datamining_code_python.ipynb`.

## Données

- Fichier `sp500.csv` : prix de clôture ajustés de 501 actions du S&P 500, réduits à la période étudiée.
- Période : du 2 janvier 2024 au 31 décembre 2025, soit 502 jours de bourse.
- Actions retenues : les 15 premières colonnes sans valeur manquante sur la période, à savoir NVDA, AAPL, MSFT, GOOG, GOOGL, AMZN, AVGO, META, TSLA, LLY, JPM, WMT, ORCL, V et XOM.

## 1. Stratégie sur moyennes mobiles

- Moyenne mobile : moyenne des m jours qui précèdent le jour courant, sans l'inclure.
- Signal d'achat quand le cours passe au-dessus de sa moyenne mobile, signal de vente quand il passe en dessous.
- Le capital de 100 € est réparti à parts égales entre les 15 actions. Après le premier signal, chaque ligne reste en position (achat ou vente à découvert) jusqu'au croisement suivant.
- Le signal observé à la clôture d'un jour est appliqué à partir du jour suivant.

| m | Rendement sur la période |
|---|---|
| 5 | -12.4 % |
| 6 | -14.0 % |
| 10 | +4.1 % |
| 11 | +7.1 % |
| 15 | -2.8 % |
| 20 | -5.0 % |

Sur deux ans, la stratégie gagne peu ou perd selon la fenêtre. Les fenêtres courtes changent souvent de position pour rien, et les meilleurs résultats sont obtenus autour de 10 jours. À titre de comparaison, acheter les 15 actions à parts égales et les garder rapporte environ +102 % sur la même période : sur un marché aussi haussier, les ventes à découvert coûtent cher.

Ces résultats ne tiennent compte ni des frais de transaction ni du coût de la vente à découvert.

## 2. ACP sur les rendements logarithmiques

L'ACP est faite sur les rendements logarithmiques standardisés.

| Composante | Variance expliquée | Variance cumulée | Lecture |
|---|---|---|---|
| CP1 | 37.9 % | 37.9 % | facteur de marché : toutes les actions ont un poids positif |
| CP2 | 10.8 % | 48.6 % | valeurs défensives et financières (JPM, WMT, V, XOM) contre semi-conducteurs et logiciels (NVDA, AVGO, ORCL) |
| CP3 | 8.1 % | 56.7 % | Alphabet (GOOG, GOOGL) contre NVDA, AVGO, ORCL et LLY |

On garde 3 composantes. Pour les rendre lisibles, les poids inférieurs à 0.2 en valeur absolue sont mis à zéro et les autres sont arrondis à ±0.5, ce qui revient à acheter ou vendre une unité de chaque action concernée.

## 3. Stratégie sur les composantes

On reconstruit un prix fictif pour chaque composante à partir de ses rendements, puis on applique la même stratégie de moyennes mobiles.

La stratégie perd entre 66 % et 85 % selon m, bien plus que sur les actions. Les portefeuilles obtenus sont très concentrés : la première composante revient à acheter une demi-unité de 9 actions à la fois, ce qui donne une volatilité annuelle d'environ 124 %, contre 34 % en moyenne pour une action seule. Avec une telle volatilité, chaque faux signal coûte très cher. Il faudrait normaliser les composantes au même niveau de risque que les actions pour comparer les deux stratégies à armes égales.

## 4. Graphe des corrélations et arbre couvrant minimal

- Graphe des paires dont la corrélation dépasse 0.5 : on obtient un seul groupe, formé de AMZN, MSFT, META, GOOG, GOOGL, NVDA, AVGO et ORCL. Les 7 autres actions n'ont aucune corrélation au-dessus de ce seuil.
- Arbre couvrant minimal, construit avec la distance 1 − ρ : AMZN est au centre de l'arbre, relié à MSFT, META, GOOGL, AAPL et JPM. LLY, WMT, XOM, TSLA et ORCL sont en bout de branche et sont donc les plus intéressantes pour diversifier.

Bibliothèques : numpy, pandas, matplotlib, scikit-learn, networkx.
