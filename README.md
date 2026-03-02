# 📈 Gestion d'actifs & Data Mining - S&P 500

> Stratégie CTA sur les 15 plus grandes actions du S&P 500 · PCA · Graphe de corrélations  
> Cours *Gestion d'actifs et Data* - M2 ISIFAR · Université Paris Cité

---

## Vue d'ensemble

Ce projet implémente une **stratégie CTA (Commodity Trading Advisor)** basée sur des signaux de croisement entre cours et moyennes mobiles, appliquée à 15 actions majeures du S&P 500 sur la période 2024–2026. Il explore ensuite une approche par **réduction dimensionnelle (PCA)** pour construire des facteurs de risque systématiques plus exploitables.

---

## Données

- **Source** : `sp500.csv` — prix de clôture ajustés, 501 actions
- **Période analysée** : 2024-01-01 → 2026-01-01 (502 jours de trading)
- **Actions retenues** : 15 actions sans valeurs manquantes — NVDA, AAPL, MSFT, GOOG, GOOGL, AMZN, AVGO, META, TSLA, LLY, JPM, WMT, ORCL, V, XOM

---

## Méthodologie

### Q1 - Moyennes mobiles

Calcul de la moyenne des `m` derniers jours **avant** le jour courant (sans inclure `i`), cohérent avec une stratégie de trading réelle.

### Q2 - Signaux d'achat / vente

Détection des **croisements** cours / moyenne mobile :
- `+1` → cours passe au-dessus (achat / long)  
- `-1` → cours passe en dessous (vente / short)  
- `0` → pas de signal

### Q3 - Simulation du portefeuille

- Capital initial : **100 €** réparti équitablement entre les 15 actions
- Position maintenue jusqu'au prochain croisement
- Rendement journalier : `(St - St-1) / St-1`
- **Résultat pour m=10** : ~14 042 € (capital de départ 100 €)

### Q4 - Sensibilité à m (fenêtre de 5 à 20 jours)

| m | Rendement (%) |
|---|---|
| 5 | ~73 000% |
| 10 | ~13 900% |
| 20 | ~2 600% |

Les petites fenêtres génèrent plus de signaux sur une période haussière. Ces chiffres supposent exécution parfaite sans frais de transaction.

### Q5 - PCA sur les rendements logarithmiques

- **PC1** (~37% variance) : facteur marché global (toutes actions bougent ensemble)
- **PC2** (~11%) : facteur sectoriel financier / value
- **PC3** (~8%) : facteur tech
- **3 composantes retenues** : 56% de variance cumulée

### Q6 - Simplification des vecteurs propres

Seuil `0.2` pour éliminer le bruit, arrondi au multiple de ±0.5.  
Résultat : positions long/short entières interprétables économiquement.

### Q7 - Stratégie CTA sur vecteurs propres

Reconstruction de prix synthétiques → stratégie CTA sur les 3 composantes.  
**La CTA-PCA surperforme la CTA sur actions individuelles** : les composantes capturent des tendances plus persistantes (facteurs systématiques).

### Q8 & Q9 - Graphe de corrélations & Minimum Spanning Tree

- Seuil corrélation : **0.5** → cluster principal : AMZN–MSFT–META–GOOG–NVDA–AVGO
- **MST** : AMZN et MSFT sont les hubs centraux ; LLY, WMT, XOM en périphérie offrent la meilleure diversification

---

## Structure du projet

```
gestion-actifs-sp500/
├── datamining_code_python.ipynb   # Notebook complet avec résultats
├── sp500.csv                      # Données (non versionnées)
└── README.md
```

---

## Stack technique

`Python 3.10+` · `numpy` · `pandas` · `matplotlib` · `sklearn` (PCA, StandardScaler) · `networkx`

---

## Auteur

**Eude Géro Dekadjevi** · M2 ISIFAR · Université Paris Cité  
[LinkedIn](https://www.linkedin.com/in/eude-gero-dekadjevi-56924722a) · [GitHub](https://github.com/eude-data)
