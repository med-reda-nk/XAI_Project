# Projet 10 — PDP & ICE avancés

[Presentation](https://www.canva.com/design/DAG7JOxYM4M/Lmj21KX6VfEz-QfaXb2qrA/edit?utm_content=DAG7JOxYM4M&utm_campaign=designshare&utm_medium=link2&utm_source=sharebutton)

Ce dépôt accompagne un projet d’explicabilité de modèles non linéaires (Random Forest / XGBoost) sur données tabulaires à l’aide des Partial Dependence Plots (PDP) et Individual Conditional Expectation (ICE).

Message clé à illustrer dans vos livrables:
- Le PDP (moyenne) peut lisser/masquer des effets hétérogènes que les courbes ICE révèlent au niveau individuel; les PDP 2D aident à exposer les interactions responsables de ces hétérogénéités.

---

## Structure du dépôt

```
.
├─ rapport_projet.pdf        
├─ XAI_Mini_Projet_10_Nkira_MR.ipynb
└─ README.md                  # Ce fichier
```

## Prérequis

- Python 3.9–3.12
- pip ou conda

Python packages:
- scikit-learn ≥ 1.2
- pandas
- numpy
- matplotlib
- seaborn (optionnel)
- xgboost (optionnel, recommandé)

Installation rapide:
```bash
# via pip
python -m venv .venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -U pip
pip install scikit-learn pandas numpy matplotlib seaborn xgboost
```

Compatibilité scikit-learn:
- Si votre scikit-learn < 1.2, remplacez OneHotEncoder(sparse_output=False) par OneHotEncoder(sparse=False).
- La fonction partial_dependence peut être déplacée/dépréciée selon la version; le script gère cette différence.

---

## Paramétrage rapide

- Basculer de RF à XGBoost:
  - scripts/generate_figures.py: variable USE_XGB = True/False
  - Ajustez les hyperparamètres (n_estimators, max_depth, learning_rate…)
- Choisir les features à tracer:
  - Modifiez la liste features_1d dans les scripts (par défaut MedInc, HouseAge, Latitude ou age, hours-per-week, education-num).
- Lisibilité ICE:
  - Réduisez le nombre de courbes via subsample ou en traçant des enveloppes (médiane/quantiles).
- Vitesse/qualité des surfaces 2D:
  - Ajustez grid_resolution (30–50 typiquement).

---

## Reproductibilité

- Fixez la graine RNG (random_state=42) partout (split, modèles).
- Logguez:
  - Version Python et bibliothèques (pip freeze > requirements.txt).
  - Hyperparamètres des modèles.
  - Hash Git du commit de référence.

---

## Interprétation guidée (à inclure dans vos livrables)

- PDP (courbe épaisse):
  - Vue globale moyenne; identifie les grandes tendances (ex.: croissance de MedInc, plateau d’age).
- ICE (courbes fines):
  - Hétérogénéité inter-individuelle; sous-groupes, seuils, croisements.
- PDP 2D:
  - Interactions: isolignes non parallèles, pentes qui dépendent d’une seconde variable.

Message à marteler:
- Le PDP peut masquer des effets hétérogènes que l’ICE révèle; confrontez systématiquement PDP et ICE; complétez par ALE lorsque les prédicteurs sont corrélés.

---

## Dépannage (FAQ)

- Problème OneHotEncoder: TypeError: got an unexpected keyword argument 'sparse_output'
  - Solution: utilisez OneHotEncoder(handle_unknown="ignore", sparse=False) avec scikit-learn < 1.2.

- partial_dependence indisponible/dépréciée:
  - Solution: utilisez PartialDependenceDisplay.from_estimator pour les tracés; la récupération programmatique des ICE (pour c-ICE) peut nécessiter un fallback ou une lib tierce (PyALE, alibi).


- Temps d’exécution long:
  - Diminuez n_estimators, utilisez tree_method="hist" avec XGBoost, réduisez grid_resolution, et sous-échantillonnez pour ICE.



