# <Project title>

**Team:** <team name>
**Members:** <name 1>, <name 2>, ...
**Track / Problem:** <e.g. Track 4, Problem 7: A logical qubit with the Steane code>  (or "Original project" / "Combined: ...")
**Python version:** <e.g. 3.11.x>

## Summary

<One paragraph: what problem you tackled, what you built, and your main result in one sentence.>

## Main result

![Main result](figures/main_result.png)

<One or two sentences describing the figure and the key number(s), including the classical reference value.>

## How to reproduce (clean start)

```bash
python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install -r requirements.txt
python src/main.py                 # <replace with your real command(s)>
```

- Expected runtime: <e.g. about 5 minutes on a laptop>
- Random seed(s) used: <e.g. 42>
- Output: the main figure is written to `figures/main_result.png`.
- Needs an IBM Quantum account? <No / Yes: set `QISKIT_IBM_TOKEN` as an environment variable. A simulator-only path is available with `--simulator`.>

## Repository contents

| Path | What it is |
|------|------------|
| `WRITEUP.pdf` | Write-up (2-4 pages): Problem, Method, Results, Honest limits |
| `AI_USE_NOTE.md` | Tools used, one thing the AI got wrong, how we caught it |
| `src/` | Code |
| `tests/` | Checks against an exact or classical reference |
| `figures/` | All figures used in the write-up |
| `requirements.txt` | Pinned package versions |

## What is simulated

<State clearly: noiseless simulator / noise model / real IBM hardware. Say which parts are a statistical shortcut rather than a physical circuit simulation.>

## Hardware run (optional bonus)

<Backend name, date, job ID, and a side-by-side comparison with the simulator. Delete this section if you did not run on hardware.>

## Honest limits (short version)

<Two or three lines. The full discussion is in WRITEUP.pdf.>

## Credits

<Libraries, papers, tutorials and any code you built on.>
