# MedChemKit 🧪

> A Python toolkit for medicinal chemistry: molecular property calculation, drug-likeness filtering, and ADMET screening — built on top of [RDKit](https://www.rdkit.org/).

[![Python](https://img.shields.io/badge/python-3.9%2B-blue.svg)](https://www.python.org/)
[![RDKit](https://img.shields.io/badge/RDKit-2023.09%2B-orange.svg)](https://www.rdkit.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

---

## 📖 Overview

**MedChemKit** helps medicinal chemists and computational scientists move from a raw set of SMILES strings to actionable insight. It wraps common cheminformatics workflows behind a clean, well-documented Python API so you can compute descriptors, apply drug-likeness rules, and triage compound libraries reproducibly.

Typical use cases:

- Screening a virtual library against drug-likeness filters before docking.
- Computing physicochemical descriptors for QSAR model building.
- Flagging structural alerts (PAINS, toxicophores) in hit lists.
- Generating a standardized property report for a project compound set.

---

## ✨ Features

| Module | What it does |
| --- | --- |
| `descriptors` | Molecular weight, LogP, TPSA, H-bond donors/acceptors, rotatable bonds, aromatic rings |
| `druglikeness` | Lipinski's Rule of Five, Veber, Ghose, Egan, lead-likeness filters |
| `admet` | Heuristic absorption/permeability estimates and structural alerts (PAINS) |
| `standardize` | Salt stripping, neutralization, tautomer canonicalization |
| `io` | Read/write SMILES, SDF, and CSV; batch processing with progress bars |
| `report` | Generate per-compound and summary reports (CSV / HTML) |

---

## 🚀 Installation

RDKit is the core dependency. The recommended path is a conda environment:

```bash
# Create and activate an environment
conda create -n medchem python=3.11 -y
conda activate medchem

# Install RDKit
conda install -c conda-forge rdkit -y

# Install MedChemKit
pip install medchemkit
```

Or, from source:

```bash
git clone https://github.com/yourname/medchemkit.git
cd medchemkit
pip install -e ".[dev]"
```

---

## ⚡ Quick Start

```python
from medchemkit import descriptors, druglikeness

smiles = "CC(=O)Oc1ccccc1C(=O)O"  # Aspirin

# 1. Compute key descriptors
props = descriptors.compute(smiles)
print(props)
# {'mw': 180.16, 'logp': 1.31, 'hbd': 1, 'hba': 3, 'tpsa': 63.6, 'rotatable_bonds': 2}

# 2. Check drug-likeness
result = druglikeness.lipinski(smiles)
print(result)
# {'passes': True, 'violations': 0, 'details': {...}}
```

### Batch screening a library

```python
import pandas as pd
from medchemkit import io, druglikeness

# Load a CSV with a 'smiles' column
df = io.read_csv("library.csv", smiles_col="smiles")

# Apply Lipinski filter to the whole set
df["lipinski_pass"] = df["smiles"].apply(
    lambda s: druglikeness.lipinski(s)["passes"]
)

hits = df[df["lipinski_pass"]]
print(f"{len(hits)} / {len(df)} compounds passed Lipinski's Rule of Five")
hits.to_csv("filtered_library.csv", index=False)
```

---

## 📊 Drug-Likeness Rules Reference

| Rule | Criteria (typical cutoffs) |
| --- | --- |
| **Lipinski (Ro5)** | MW ≤ 500, LogP ≤ 5, HBD ≤ 5, HBA ≤ 10 |
| **Veber** | Rotatable bonds ≤ 10, TPSA ≤ 140 Å² |
| **Ghose** | 160 ≤ MW ≤ 480, −0.4 ≤ LogP ≤ 5.6, 40 ≤ MR ≤ 130, 20 ≤ atoms ≤ 70 |
| **Egan** | TPSA ≤ 131.6, LogP ≤ 5.88 |
| **Lead-likeness** | MW ≤ 350, LogP ≤ 3.5 |

> ⚠️ These are heuristic filters for *prioritization*, not hard pass/fail gates. Many approved drugs (especially in oncology and antibiotics) violate one or more rules.

---

## 🗂️ Project Structure

```
medchemkit/
├── medchemkit/
│   ├── __init__.py
│   ├── descriptors.py      # Physicochemical descriptor calculation
│   ├── druglikeness.py     # Rule-based filters
│   ├── admet.py            # ADMET heuristics & structural alerts
│   ├── standardize.py      # Molecule cleanup / canonicalization
│   ├── io.py               # File readers/writers
│   └── report.py           # Reporting utilities
├── tests/
├── examples/
│   ├── screen_library.ipynb
│   └── qsar_descriptors.ipynb
├── pyproject.toml
└── README.md
```

---

## 🧪 Testing

```bash
pytest tests/ -v
```

---

## 🤝 Contributing

Contributions are welcome! Please:

1. Fork the repo and create a feature branch.
2. Add tests for new functionality.
3. Run `ruff` and `pytest` before submitting.
4. Open a pull request with a clear description.

---

## ⚖️ Disclaimer

MedChemKit provides **computational estimates** intended for research and triage. It does **not** replace experimental ADMET/toxicity assays or expert medicinal chemistry judgment. Do not use it as the sole basis for safety, regulatory, or clinical decisions.

---

## 📚 References

- Lipinski, C.A. et al. *Adv. Drug Deliv. Rev.* (2001) — Rule of Five
- Veber, D.F. et al. *J. Med. Chem.* (2002) — Oral bioavailability
- Baell, J.B. & Holloway, G.A. *J. Med. Chem.* (2010) — PAINS filters
- RDKit: Open-source cheminformatics — https://www.rdkit.org/

---

## 📄 License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.
