# Panel/Binder Migration Project Instructions for Claude

**Updated:** January 2026 — Based on real-world debugging

## Critical Context

This is a **Panel app from 2020-2021** that works locally but **no longer deploys on Binder**. Panel deployment patterns have changed significantly since then.

## Your Task

Update this project so the app deploys successfully on mybinder.org while maintaining local functionality.

## IMPORTANT: Use the JupyterLab Approach

The direct Panel serve approach (`?urlpath=/panel/NotebookName`) via `jupyter-panel-proxy` **often times out** with "500: Internal Server Error - could not start panel in time".

**Use the JupyterLab approach instead:**
```
?urlpath=lab/tree/NotebookName.ipynb
```

Users will need to run cells manually, but it's **reliable**.

## Required Changes (in order)

### 1. Update `environment.yml`

Add the `pyviz` channel and `jupyter-panel-proxy` package:

```yaml
channels:
  - conda-forge
  - pyviz          # ← ADD THIS

dependencies:
  # ... existing dependencies ...
  - jupyter-panel-proxy   # ← ADD THIS (keep for potential future use)
```

### 2. Update the Notebook Structure

**Cell 1 — Imports (add warning suppression):**
```python
import warnings
warnings.filterwarnings("ignore", category=FutureWarning, module="param")

import panel as pn
# ... other imports

pn.extension()  # Only call ONCE, no parameters needed
```

**Cell 2 — App definition:**
Find where the Panel app is defined and add `.servable()` at the END:

```python
# ... app code ...
app.servable()  # or app_panel.servable(), whatever the variable is called
```

**Cell 3 — Comment out pn.serve():**
```python
# For local development only (uncomment to use):
# pn.serve(app)
```

### 3. Check for API Changes

Panel has evolved. Watch for:
- `style=` → `styles=` (Panel 1.0+)
- Remove `sizing_mode` from `pn.extension()` if layout is too wide
- Only call `pn.extension()` once per notebook

Fix any deprecation warnings or errors.

### 4. Update README with Binder Badge AND Instructions

```markdown
[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/OWNER/REPO/BRANCH?urlpath=lab/tree/NotebookName.ipynb)

### How to Run on Binder

1. Click the **Launch Binder** badge above (allow 1-2 minutes for the environment to load)
2. Once JupyterLab opens, you'll see the notebook
3. From the menu, select **Run → Run All Cells**
4. The interactive app will appear below the main cell
5. Adjust the widgets to explore different scenarios
```

**Note:** Include `.ipynb` extension in the URL for JupyterLab approach.

### 5. Remove Obsolete Files (if present)

Delete these — they often cause more problems than they solve:
- `postBuild`
- `start`
- `jupyter_panel_config.py`

## Testing Checklist

1. **Local test:** Run notebook cells 1-2, verify app displays inline
2. **Local serve test:** Uncomment and run Cell 3, verify app opens in browser
3. **Push to GitHub**
4. **Binder test:** Click badge, wait for build (2-5 min first time)
5. **Verify:** Run All Cells in JupyterLab, app displays, widgets work

## Troubleshooting

### 500 timeout errors with /panel/ URL
- Switch to JupyterLab approach: `?urlpath=lab/tree/NotebookName.ipynb`

### Local jupyter_bokeh TypeError
- Version mismatch — use `pn.serve()` for local testing instead
- Or: `conda update jupyter_bokeh -c conda-forge`

### App too wide
- Remove `sizing_mode` parameter from `pn.extension()`

## When Uncertain

- Check current Panel docs: https://panel.holoviz.org/how_to/deployment/binder.html
- If the app structure is complex, ask the user for clarification
- Preserve existing functionality — don't refactor unless necessary

## Do NOT

- Change the app's logic or calculations (unless broken)
- Remove features that work
- Invent new functionality
- Change file names without asking
- Try to make `?urlpath=/panel/NotebookName` work if it times out — use JupyterLab instead
