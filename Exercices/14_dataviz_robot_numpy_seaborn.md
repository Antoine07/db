# Exercice guide — 14. Dataviz robot (Python + NumPy + Seaborn)

## Objectif

Construire un mini workflow de visualisation pour une collecte de donnees robot:
- generer un dataset de telemetrie (avec erreurs injectees)
- analyser les erreurs avec `numpy`
- produire des graphes `seaborn` pour detecter les anomalies

---

## Prerequis

- Python 3.10+ disponible
- dependances installees:

```bash
python3 -m pip install numpy seaborn matplotlib
```

---

## Etape 1 — Generer un jeu de donnees robot

Depuis la racine du projet:

```bash
python3 scripts/generate_robot_telemetry_demo.py
```

Fichier attendu:
- `data/robot_telemetry_demo.csv`

Colonnes:
- `timestamp`
- `robot_id`
- `battery_pct`
- `motor_temp_c`
- `vibration_mm_s`
- `distance_error_cm`
- `error_code`

---

## Etape 2 — Produire les graphes de detection d'erreurs

```bash
python3 scripts/dataviz_robot_errors.py
```

Dossier de sortie:
- `data/plots/robot_errors/`

Graphes attendus:
- `error_codes_count.png`
- `distance_error_distribution.png`
- `vibration_vs_distance_error.png`
- `error_rate_heatmap_robot_hour.png`

---

## Etape 3 — Lecture metier des graphes

1. Quel `error_code` est le plus frequent ?
2. A partir de combien de `distance_error_cm` les observations deviennent-elles majoritairement en erreur ?
3. Quels robots/heures presentent le plus fort taux d'erreur dans la heatmap ?
4. La zone "vibration forte + distance_error forte" est-elle corrigee par le label `error` ?

---

## Etape 4 — Bonus (analyse rapide avec NumPy)

Dans un script ou notebook:

```python
import numpy as np

data = np.genfromtxt(
    "data/robot_telemetry_demo.csv",
    delimiter=",",
    names=True,
    dtype=None,
    encoding="utf-8",
)

error_mask = data["error_code"] != "ok"
print("Taux erreur global:", round(np.mean(error_mask) * 100, 2), "%")

for robot in np.unique(data["robot_id"]):
    m = data["robot_id"] == robot
    rate = np.mean(error_mask[m]) * 100
    print(robot, "=>", round(rate, 2), "%")
```

Questions bonus:
1. Quel robot est le moins stable ?
2. Le taux d'erreur global depasse-t-il 10% ?
