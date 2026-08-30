# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

Teaching material (Jupyter notebooks) for the Jyväskylä Summer School 2022 on 2D quantum
materials. There is no application, no build, and no test suite — the notebooks *are* the
deliverable, and they are meant to be read on GitHub with their outputs visible.

## Tracked vs. local-only — read this first

Only these files are in git: `sessions/session[1-5].ipynb`, `README.md`, `update.py`,
`.gitignore`, `LICENSE.md`.

`lecture_slides/`, `lecture_notes/`, and `extra_calculations_lectures/` exist on disk but are
gitignored in full. Edits there will never be committed — check `git ls-files` before assuming
a file is under version control.

## Running the notebooks

Notebooks depend on [pyqula](https://github.com/joselado/pyqula), which is **not installed in
this environment**:

```bash
pip install --upgrade pyqula
```

The header cell of each notebook also accepts a `PYQULAROOT` env var pointing at a source
checkout, as an alternative to the pip install.

Running cells drops pyqula artifacts (`*.OUT`, `MF.pkl`) into the notebook's working directory.
They are gitignored, safe to delete, and must not be committed.

## Notebook conventions

- Every session opens with the **same header cell**: pyqula path setup, then matplotlib
  defaults (`figsize [20,8]`, `font.size 22`, `image.cmap 'coolwarm'`), a commented-out
  `parallel.set_cores("max")`, and `import numpy as np`. Keep it identical across notebooks.
- Structure per session: title → `### Contents` → `### Learning outcomes`, then alternating
  demo code cells and `### Exercise` markdown cells that ask the student to vary a parameter.
  Some sections also carry an `#### In the lab` markdown cell linking a real paper.
- Code style is deliberately compact: several statements per line separated by `;`, with a
  trailing `# comment` explaining each. Match it rather than reformatting.
- **Committed cell outputs are intentional** — do not strip them. A dirty diff on a session
  notebook usually just means it was re-executed.

## pyqula idioms used throughout

The core chain is geometry → Hamiltonian → observable:

```python
from pyqula import geometry
g = geometry.honeycomb_lattice()      # or chain, kagome_lattice, honeycomb_zigzag_ribbon(N)
h = g.get_hamiltonian(has_spin=False) # Hamiltonian for that lattice
(k, e) = h.get_bands()                          # plain bands
(k, e, c) = h.get_bands(operator="sublattice")  # with an operator -> 3-tuple, c colors the scatter
```

Physics is added by mutating `h` in place: `add_onsite`, `add_haldane`, `add_swave`,
`add_rashba`, `add_exchange`, `add_zeeman`, `add_sublattice_imbalance`,
`add_orbital_magnetic_field`, `add_soc`. Other observables: `get_dos`, `get_ldos`,
`get_fermi_surface`, `get_bands_map`, `get_berry_curvature`, `get_chern`. Interacting cases use
`set_filling` + `get_mean_field_hamiltonian`. Other modules in play: `potentials`, `ribbon`,
`islands`, `embedding`, `topology`, `specialhamiltonian`.

## Committing

The author's workflow is a single script that stages everything, commits, and pushes:

```bash
./update.py --m "message"   # message defaults to "Update"
```
