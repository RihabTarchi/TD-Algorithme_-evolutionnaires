# Algorithme génétique : cédule de maintenance de centrales

Résolution par algorithme génétique (Python) du problème d'ordonnancement de la maintenance de 7 centrales électriques sur 4 intervalles.

## 1. Formulation du problème

| Unité | Capacité (MW) | Intervalles de maintenance requis |
|---|---|---|
| 1 | 20 | 2 (consécutifs) |
| 2 | 15 | 2 (consécutifs) |
| 3 | 35 | 1 |
| 4 | 40 | 1 |
| 5 | 15 | 1 |
| 6 | 15 | 1 |
| 7 | 10 | 1 |

- Capacité totale : 150 MW
- Demande par intervalle : 80, 90, 65, 70 MW
- Contrainte : la maintenance d'une unité commence au début d'un intervalle et se termine à la fin d'un intervalle ; la réserve doit rester positive.

## 2. Encodage

- **Gène** : 4 bits par unité (1 = en maintenance pendant l'intervalle, 0 = en service).
- Valeurs valides : `1100, 0110, 0011` (unités 1 et 2) ; `1000, 0100, 0010, 0001` (unités 3 à 7).
- **Chromosome** : 7 gènes = 28 bits, par exemple `0110 0011 0001 1000 0100 0010 1000`.

## 3. Fonction objectif

```
Réserve(t)  = Capacité totale − Σ (capacité des unités en maintenance à t) − Demande(t)
Fitness(c)  = max(0, min over t of Réserve(t))
```

On **maximise la plus petite réserve** sur l'année. Une réserve négative donne une performance de 0.

Exemple du cours : réserves [20, 25, 35, 30] → fitness = 20.

## 4. Opérateurs génétiques

- **Sélection par loterie (roulette)** : probabilité proportionnelle à la fitness.
- **Croisement** à un point, placé entre deux gènes (blocs de 4 bits).
- **Mutations** (toujours sur des blocs de 4 bits, donc cédules toujours valides) :
  - **Inversion** (demandée) : inversion de l'ordre des 4 bits d'un gène (`1100 → 0011`).
  - Remplacement : un gène remplacé par une autre valeur valide.
  - Échange : permutation de deux gènes d'unités de même type.

## 5. Paramètres

Population 50 (≈ 7 × le nombre de variables), 100 générations, croisement 0,8, mutation 0,2, élitisme.

## 6. Exécution

```bash
python ga_maintenance.py
```

## 7. Résultats (30 exécutions par mutation)

Optimum exact par force brute (9216 cédules) : **fitness 25**.

| Mutation | Moyenne | Optimum trouvé |
|---|---|---|
| Inversion | 25,00 | 30/30 |
| Remplacement | 24,83 | 29/30 |
| Échange | 24,83 | 29/30 |
