# EIA SEDS Notebooks

Notebooks that pull State Energy Data System (SEDS) data from the [EIA Open Data API](https://www.eia.gov/opendata/) into pandas.

## Quick start

1. **Install Python 3.12 or newer** (3.10 is the minimum; 3.12+ is recommended).
   - macOS: `brew install python@3.12` or the installer from python.org
   - Windows: `winget install Python.Python.3.12` or the python.org installer
   - Linux: your package manager, or pyenv
2. **Clone this repo and open the folder in VS Code** with the Python and Jupyter extensions installed.
3. **Run `00_setup_environment.ipynb`** top to bottom. It creates `.venv/`, installs `requirements.txt`, registers a Jupyter kernel, and creates your `.env`. It needs only the standard library, so any Python 3.9+ kernel can run it.
4. **Add your free EIA API key** to `.env` (`EIA_API_KEY=...`).
5. **Open an analysis notebook and select the kernel "Python (EIA SEDS)"** (kernel picker, top right).

### Prefer the terminal?

```bash
python3.12 -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
cp .env.example .env               # then add your key
```

## Keeping things up to date

| I want to... | Do this |
|---|---|
| See what's outdated / upgrade packages | Run `01_update_environment.ipynb` (safe by default; set `DRY_RUN = False` to upgrade) |
| Move to a newer Python | Install it, then run `00_setup_environment.ipynb` with `RECREATE_VENV = True` from a kernel outside `.venv` |
| Cap a package version | Edit `requirements.txt` (for example `pandas<3`) |

A notebook cannot upgrade the Python that is running it, which is why moving to a newer Python means building a new environment.

## Files

| File | Purpose |
|---|---|
| `requirements.txt` | Packages the project needs (loose lower bounds) |
| `requirements-lock.txt` | Exact versions that last worked together (written by the update notebook) |
| `.env.example` | Template for your secrets file |
| `.env` | Your real key. **Git-ignored. Never commit it.** |
| `.gitignore` | Keeps `.env`, environments, caches, and checkpoints out of git |

## Good practice

- **Never commit `.env`.** If a key is ever committed, treat it as leaked and request a new one.
- **Clear notebook outputs before committing** (*Clear All Outputs*), so no data or credentials end up in git history.
- **Start analysis notebooks with a guard cell** instead of `pip install ...`. It fails early with a clear message if the wrong kernel is selected:

```python
import importlib.util
import sys

missing = [m for m in ("pandas", "requests", "dotenv") if importlib.util.find_spec(m) is None]
if missing:
    raise ImportError(
        "Missing packages: %s. Running on %s. Run 00_setup_environment.ipynb, "
        "then select the 'Python (EIA SEDS)' kernel." % (", ".join(missing), sys.executable)
    )
```

## Troubleshooting

- **`No module named pandas`**: the notebook is on the wrong kernel. Run `import sys; print(sys.executable)`. The path should point inside `.venv`.
- **`SyntaxError` on `pip install ...`**: that is a terminal command. In a notebook cell use `%pip install ...`, or better, rerun `00_setup_environment.ipynb`.
- **`KeyError: 'EIA_API_KEY'`**: `.env` is missing, empty, or in the wrong folder. Re-run step 5 of `00_setup_environment.ipynb`.
