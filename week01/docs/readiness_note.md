# Readiness note

Record anything that did not work, exactly as it happened. An honest incomplete
attempt is more useful evidence than copied output, and there is no penalty for
a setup or platform-access problem.

## Toolchain

| Tool | Working? | Version, or the error |
| --- | --- | --- |
| Python 3 | Yes | 3.11.5 |
| pandas and NumPy | Yes now | pandas 2.3.3; NumPy 2.4.6. The notebook runs after reinstalling pandas. |
| VS Code, with the Python and Jupyter extensions | Yes | Notebook cells execute in VS Code; extension versions not checked. |
| Git and a GitHub account | Git works; GitHub account not verified | Git 2.55.0.windows.5 |
| pytest | Yes | 8.4.2; Week 1 test suite passed (6 tests). |

## Anything that failed

For each failure give the exact command and the full error message.

- **Command:** `import pandas as pd` in the notebook setup cell, using `D:\AIDS\AIDC\.venv`.
- **Error:** The initial traceback reported `ImportError: cannot import name 'CHAINED_WARNING_DISABLED' from 'pandas.compat._constants' (d:\AIDS\AIDC\.venv\Lib\site-packages\pandas\compat\_constants.py)`, followed by `ImportError: C extension: pandas.compat._constants not built. If you want to import pandas from the source directory, you may need to run 'python -m pip install -ve . --no-build-isolation -Ceditable-verbose=true' to build the C extensions first.` Reinstalling pandas 2.3.3 resolved the issue; notebook cells now execute.

## Git evidence

- **Latest commit hash:** `0840b355a71d05114c7614624f08309678cb3d22` (current course-repository `HEAD`; the Week 1 completion commit has not been made).
- **Pushed to the remote?** No Week 1 completion commit has been pushed; current `HEAD` matches `origin/main`.
- **If no, the access limitation was:** The course repository is read-only in Week 1. The Week 1 completion commit should remain local; no completion commit has been made yet.
