# CLAUDE.md — AI Assistant Guide for ThinkStats2

## Project Overview

**ThinkStats2** is the companion code repository for *Think Stats, 2nd Edition* by Allen B. Downey (Green Tea Press). It provides:

- A Python statistics library (`thinkstats2.py`, `thinkplot.py`)
- Chapter-by-chapter exercise and solution scripts
- Jupyter notebooks for interactive exploration
- Real-world datasets (NSFG, BRFSS, population data)
- LaTeX source for the textbook (`book/`)

**License**: GNU GPLv3
**Primary language**: Python (2/3 compatible via `from __future__` imports)

---

## Repository Layout

```
ThinkStats2/
├── CLAUDE.md              # This file
├── README.md              # Brief project description
├── LICENSE                # GPLv3
├── .gitignore
├── book/                  # LaTeX source for the textbook
│   ├── Makefile
│   ├── book.tex           # Full textbook (~414 KB)
│   └── *.png              # Navigation images
└── code/                  # All Python code and data
    ├── Makefile
    ├── requirements.txt
    ├── thinkstats2.py     # Core statistics library (2,801 lines)
    ├── thinkplot.py       # Matplotlib wrapper/plotting helpers
    ├── nsfg.py            # NSFG dataset reader
    ├── first.py           # First-babies analysis
    ├── brfss.py           # BRFSS dataset reader
    ├── analytic.py        # Analytical distribution helpers
    ├── estimation.py      # Parameter estimation
    ├── hypothesis.py      # Hypothesis testing
    ├── regression.py      # Regression analysis
    ├── linear.py          # Linear models
    ├── survival.py        # Survival analysis
    ├── timeseries.py      # Time series analysis
    ├── density.py         # Kernel density estimation
    ├── cumulative.py      # CDF analysis examples
    ├── scatter.py         # Scatter plot examples
    ├── normal.py          # Normal distribution examples
    ├── probability.py     # Probability examples
    ├── populations.py     # Population statistics
    ├── hinc.py / hinc2.py # Household income analysis
    ├── relay.py           # Marathon relay analysis
    ├── mystery.py         # Identify unknown distributions
    ├── chap01ex.py ...    # Chapter exercise starters
    ├── chap01soln.py ...  # Chapter solutions
    ├── *.ipynb            # Jupyter notebooks (exercises + solutions)
    ├── *_test.py          # Unit test files
    └── data/              # Datasets (see Data Files section)
```

---

## Core Library: `thinkstats2.py`

The central module. Key classes and their roles:

| Class / Function | Description |
|---|---|
| `Hist` | Histogram — maps values to integer frequencies |
| `Pmf` | Probability Mass Function — maps values to probabilities |
| `_DictWrapper` | Private base class for `Hist` and `Pmf` |
| `Cdf` | Cumulative Distribution Function (discrete) |
| `Pdf` | Probability Density Function (continuous, abstract base) |
| `GaussianPdf` | Gaussian PDF implementation |
| `EstimatedPdf` | KDE-based PDF from data |
| `RandomSeed(x)` | Set seeds for `random` and `np.random` simultaneously |
| `Odds(p)` / `Probability(o)` | Convert between probability and odds |
| `Mean(t)`, `Var(t)`, `MeanVar(t)` | Basic statistics on sequences |
| `Corr(xs, ys)`, `Cov(xs, ys)` | Correlation and covariance |
| `SpearmanCorr(xs, ys)` | Rank-based Spearman correlation |
| `Interpolator` | Linear interpolation helper |

All classes use Python 2/3 compatible syntax (`from __future__ import print_function, division`).

---

## Plotting: `thinkplot.py`

A thin wrapper around `matplotlib` providing:
- Consistent color schemes (Brewer palette)
- Convenience functions to plot `Hist`, `Pmf`, `Cdf`, and `Pdf` objects
- Figure management helpers

Always import as `import thinkplot` alongside `import thinkstats2`.

---

## Development Workflows

### Setting Up

```bash
cd code
pip install -r requirements.txt
```

The `requirements.txt` pins older versions (circa 2015). In modern environments you may need to relax pinned versions:

```
matplotlib, numpy, scipy, pandas, statsmodels, nose, mock, six
```

For Jupyter notebooks:

```bash
pip install jupyter
jupyter notebook
```

### Running the Tests

Tests use Python's `unittest` framework. The `nose` runner (pinned in `requirements.txt`) provides test discovery.

```bash
# From the code/ directory:

# Run all tests with nose:
nosetests

# Run all tests with unittest discovery:
python -m unittest discover

# Run a specific test file:
python -m unittest thinkstats2_test
python -m unittest survival_test
python -m unittest test_models
```

Test files:
- `code/thinkstats2_test.py` — main library unit tests
- `code/survival_test.py` — survival analysis tests
- `code/test_models.py` — statistical model tests

### Generating Documentation

```bash
# From code/:
make docs        # Runs pydoc to generate thinkstats2.html and thinkplot.html
```

### Generating Figures

```bash
# From code/:
make all_figs    # Runs descriptive.py, cumulative.py, etc. to regenerate figures
make figs        # Rsyncs generated figures to ../book/figs/
```

### Building the Book (LaTeX)

```bash
# From book/:
make all         # pdflatex + makeindex + dvips → PDF
make hevea       # Convert to HTML via hevea
make clean       # Remove build artifacts
```

---

## Code Conventions

### Style
- Python 2/3 compatible: always include `from __future__ import print_function, division` at the top of new files.
- Follow existing docstring style: one-line summary, blank line, extended description, then parameter/return docs.
- Function and class names use `CamelCase` for classes, `CamelCase` for standalone functions (the library uses this convention — e.g., `MakeCdf`, `RandomSeed`).
- Variable names are typically short and domain-specific (e.g., `pmf`, `cdf`, `xs`, `ys`, `mu`, `sigma`).

### Testing
- Use `unittest.TestCase` subclasses.
- Test method names begin with `test` (e.g., `testOdds`, `testMean`).
- Use `assertEqual` for exact values and `assertAlmostEqual` for floating-point comparisons.
- Each chapter solution file contains a `main()` function that demonstrates and spot-checks the results — run it directly to verify correctness.

### Data Reading Pattern
Data readers (e.g., `nsfg.py`) expose top-level functions like `ReadFemPreg()`, `ReadFemResp()`, which return `pandas.DataFrame` objects. Cleaning is applied in `CleanFemPreg()` or similar functions called inside the reader.

### Notebooks
- All notebooks should include `%matplotlib inline` at the top.
- Notebooks import `thinkstats2` and `thinkplot` directly (they must be run from the `code/` directory or with `code/` on the Python path).

---

## Data Files (`code/data/` and `code/`)

| File(s) | Description |
|---|---|
| `2002FemPreg.dat.gz` + `.dct` | NSFG 2002 female pregnancy data |
| `2002FemResp.dat.gz` + `.dct` | NSFG 2002 female respondent data |
| `2002Male.dat.gz` + `.dct` | NSFG 2002 male data |
| `2006_2010_FemPreg*` | NSFG 2006–2010 pregnancy data |
| `2006_2010_FemResp*` | NSFG 2006–2010 respondent data |
| `2011_2013_*Data.dat.gz` | NSFG 2011–2013 data |
| `CDBRFS08.ASC.gz` | BRFSS 2008 data (~44 MB) |
| `babyboom.dat` | Baby birth timing data |
| `hinc06.csv` | Household income data |
| `populations.csv` | Population statistics |
| `PEP_2012_PEPANNRES_*.csv` | US population estimates |
| `mystery0.dat` … `mystery7.dat` | Unknown-distribution samples for exercises |

The `.dct` files are Stata dictionary files that describe the fixed-width format of the corresponding `.dat.gz` files.

---

## Chapter Structure

Each chapter follows a consistent pattern:

| File pattern | Purpose |
|---|---|
| `chap##ex.py` | Exercise starter file (incomplete functions to fill in) |
| `chap##soln.py` | Complete solutions |
| `chap##ex.ipynb` | Exercise notebook |
| `chap##soln.ipynb` | Solution notebook |

Chapters covered: 1–14 (not all have all four files).

---

## Key Dependencies

| Package | Role |
|---|---|
| `numpy` | Array operations, numerical computing |
| `scipy` | Statistical distributions, special functions |
| `pandas` | DataFrames for dataset loading and manipulation |
| `matplotlib` | Plotting backend (used via `thinkplot`) |
| `statsmodels` | OLS regression, survival analysis |
| `nose` | Test runner / discovery |
| `mock` | Mocking in tests |
| `six` | Python 2/3 compatibility shim |

---

## Common Tasks for AI Assistants

### Adding a new statistical function
1. Add it to `thinkstats2.py` following the existing docstring and naming conventions.
2. Add a corresponding test in `thinkstats2_test.py` using `unittest.TestCase`.
3. Run `python -m unittest thinkstats2_test` to verify.

### Fixing a chapter exercise or solution
1. The relevant file is `chap##ex.py` or `chap##soln.py` in `code/`.
2. Run the file directly (`python chap##soln.py`) to check output — the `main()` function exercises the solution.
3. If a notebook version exists (`chap##soln.ipynb`), update it to stay consistent.

### Adding a new dataset reader
1. Follow the pattern in `nsfg.py`: a top-level `Read*()` function that returns a cleaned `pandas.DataFrame`.
2. Store raw data files in `code/` alongside existing data files.

### Working with Jupyter notebooks
- Always run from the `code/` directory so relative imports and data paths resolve correctly.
- Ensure `%matplotlib inline` is the first cell.
- Notebooks import `thinkstats2` and `thinkplot` without package prefixes.

---

## What NOT to Do

- Do not add Python 3-only syntax (f-strings, walrus operator, etc.) without ensuring Python 2 compatibility is no longer a concern for the project.
- Do not move data files — scripts use relative paths assuming data is in `code/`.
- Do not rename `thinkstats2.py` or `thinkplot.py` — they are imported by name throughout the codebase.
- Do not alter pinned versions in `requirements.txt` without testing, as the codebase targets older API surfaces.
