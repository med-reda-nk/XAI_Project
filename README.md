# Projet 10 — PDP & ICE avancés

Ce dépôt accompagne un projet d’explicabilité de modèles non linéaires (Random Forest / XGBoost) sur données tabulaires à l’aide des Partial Dependence Plots (PDP) et Individual Conditional Expectation (ICE). Il contient un rapport LaTeX prêt à compiler, un deck Beamer, et des scripts Python pour entraîner le modèle et générer les figures.

Message clé à illustrer dans vos livrables:
- Le PDP (moyenne) peut lisser/masquer des effets hétérogènes que les courbes ICE révèlent au niveau individuel; les PDP 2D aident à exposer les interactions responsables de ces hétérogénéités.

---

## Structure du dépôt

```
.
├─ report.pdf        
├─ XAI_Project.ipynb
└─ README.md                  # Ce fichier


## Prérequis

- Python 3.9–3.12
- pip ou conda
- Outils LaTeX (pdflatex + bibtex/biber) pour compiler le rapport et les slides

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

- Figures manquantes dans le PDF:
  - Vérifiez le chemin des images (report/figures/ vs scripts/figures/). Adaptez \includegraphics ou déplacez les PNG dans report/figures/.

- Temps d’exécution long:
  - Diminuez n_estimators, utilisez tree_method="hist" avec XGBoost, réduisez grid_resolution, et sous-échantillonnez pour ICE.

---

## Checklist des éléments Présent dans le notebook

- [ ] Modèle non linéaire entraîné et évalué (scores reportés).
- [ ] Top-10 permutation importance + justification des 2–3 features retenues.
- [ ] PDP+ICE 1D pour 2–3 variables clés, commentaires “moyenne vs individus”.
- [ ] PDP 2D pour au moins une paire (interaction) + interprétation.
- [ ] (Option) c-ICE par sous-groupes pour illustrer des pentes distinctes.

---

## Références

- Friedman, J. H. (2001). Greedy Function Approximation: A Gradient Boosting Machine.
- Goldstein, A., Kapelner, A., Bleich, J., Pitkin, E. (2015). Peeking Inside the Black Box: ICE.
- Apley, D. W., Zhu, J. (2020). Visualizing the Effects of Predictor Variables (ALE).
- Pedregosa, F. et al. (2011). Scikit-learn: Machine Learning in Python.
- Chen, T., Guestrin, C. (2016). XGBoost: A Scalable Tree Boosting System.
