# Updating Old Panel/Binder Deployments (2020-2021 → 2025+)

**Context:** This guide is for updating Panel apps that were built in 2020-2021 and work locally but no longer deploy on Binder. Panel deployment patterns have changed significantly.

**Updated:** January 2026 — Based on real-world debugging of wind-calculator project.

---

## TL;DR: The Reliable Approach (JupyterLab)

After extensive testing, the **most reliable approach** for Panel apps on Binder is:

1. **URL pattern:** `?urlpath=lab/tree/NotebookName.ipynb` (opens JupyterLab)
2. **User action:** Run → Run All Cells
3. **App displays:** Inline in notebook output

The direct Panel serve approach (`?urlpath=/panel/NotebookName`) via `jupyter-panel-proxy` often times out.

---

## Quick Diagnosis Checklist

### 1. Check the `environment.yml`

**Old pattern (broken):**
```yaml
dependencies:
  - panel
  - bokeh
  - notebook
```

**New pattern (working):**
```yaml
channels:
  - conda-forge
  - pyviz        # Required for jupyter-panel-proxy
  
dependencies:
  - panel
  - bokeh
  - jupyter-panel-proxy   # ← Keep this (helps, doesn't hurt)
  - notebook
```

---

### 2. Check the Notebook Structure

**Old pattern (broken on Binder):**
```python
# Just uses pn.serve() at the end
app = pn.Column(...)
pn.serve(app)  # Works locally, fails on Binder
```

**New pattern (works both locally and on Binder):**
```python
# Cell 1: Imports
import panel as pn
pn.extension()

# Cell 2: App definition
app = pn.Column(...)

# Make servable (displays inline when cell runs)
app.servable()

# Cell 3 (optional, for local dev only - comment out for Binder):
# pn.serve(app)
```

**Key insight:** `app.servable()` displays the app inline in the notebook output when the cell is run.

---

### 3. Use the JupyterLab URL Pattern

**Recommended (reliable):**
```
https://mybinder.org/v2/gh/OWNER/REPO/BRANCH?urlpath=lab/tree/NotebookName.ipynb
```

**Alternative (often fails with timeouts):**
```
https://mybinder.org/v2/gh/OWNER/REPO/BRANCH?urlpath=/panel/NotebookName
```

---

### 4. Remove Obsolete Configuration Files

These often cause more problems than they solve:

| File | Status | Recommendation |
|------|--------|----------------|
| `postBuild` | NOT needed | Delete unless doing something specific |
| `start` | NOT needed | Delete — Binder doesn't reliably execute custom start scripts |
| `jupyter_panel_config.py` | NOT needed | Delete |
| `app.py` | Optional | Keep if you want a standalone serve option |

---

### 5. Check Panel Version Compatibility

**Known issues:**
- `style=` → `styles=` (Panel 1.0+)
- `pn.extension()` should only be called once per notebook
- Remove `sizing_mode` parameter if layout is too wide
- Panel API changes between versions
- `styles` vs `style` parameter (Panel 1.0+ uses `styles`)
- Widget parameter names may have changed

**Recommendation:** Check the Panel changelog for breaking changes between your old version and current version.

---

## Migration Steps

### Step 1: Update `environment.yml`
```yaml
channels:
  - conda-forge
  - pyviz
  
dependencies:
  - python=3.12          # Or your preferred version
  - panel>=1.0           # Modern Panel
  - bokeh
  - jupyter-panel-proxy  # Keep for potential future use
  - notebook
  - ipykernel
  # ... your other dependencies
```

### Step 2: Update Notebook Structure

**Cell 1 — Imports:**
```python
import warnings
warnings.filterwarnings("ignore", category=FutureWarning, module="param")

import panel as pn
# ... your other imports

pn.extension()  # Only call once!
```

**Cell 2 — App definition:**
```python
# Your app code...
app = pn.Column(
    pn.pane.Markdown("# My App"),
    # ... widgets and content
)

# Add this at the END of the cell for Binder
app.servable()
```

**Cell 3 — Local development (commented out):**
```python
# For local development only (uncomment to use):
# pn.serve(app)
```

### Step 3: Update README with Binder Badge and Instructions

```markdown
[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/OWNER/REPO/BRANCH?urlpath=lab/tree/NotebookName.ipynb)

### How to Run on Binder

1. Click the **Launch Binder** badge above (allow 1-2 minutes for the environment to load)
2. Once JupyterLab opens, you'll see the notebook
3. From the menu, select **Run → Run All Cells**
4. The interactive app will appear below the main cell
5. Adjust the widgets to explore different scenarios
```

### Step 4: Remove Obsolete Files
Delete if present:
- `postBuild`
- `start`
- `jupyter_panel_config.py`

### Step 5: Test
1. **Local test:** Run notebook cells, verify app works
2. **Push to GitHub**
3. **Binder test:** Click badge, wait for build (2-5 minutes first time)
4. **Verify:** Run All Cells, app displays, widgets work

---

## Troubleshooting

### "500: Internal Server Error - could not start panel in time"
This happens with `?urlpath=/panel/NotebookName`. **Solution:** Use JupyterLab approach instead:
```
?urlpath=lab/tree/NotebookName.ipynb
```

### "Binder inaccessible" after container starts
- Session may have timed out waiting for Panel
- Try JupyterLab URL instead
- May need to wait for fresh build after commits

### App loads but widgets don't work
- Check for Panel API changes (especially `styles` vs `style`)
- Verify all dependencies are in environment.yml
- Check browser console for JavaScript errors

### Build fails
- Check environment.yml syntax (YAML is whitespace-sensitive)
- Ensure channels include `conda-forge` and `pyviz`
- Check for package version conflicts

### App stretches too wide
- Remove `sizing_mode='stretch_width'` from `pn.extension()`
- Just use `pn.extension()` with no parameters

### Local jupyter_bokeh errors
```
TypeError: standalone_docs_json_and_render_items() takes 1 positional argument but 2 were given
```
This is a local environment version mismatch. **Solutions:**
- Update: `conda update jupyter_bokeh -c conda-forge`
- Or use `pn.serve(app)` for local testing instead of inline display

### FutureWarning from param module
Add to the top of your imports:
```python
import warnings
warnings.filterwarnings("ignore", category=FutureWarning, module="param")
```

---

## Key Resources

- **Panel deployment docs:** https://panel.holoviz.org/how_to/deployment/index.html
- **Panel Binder docs:** https://panel.holoviz.org/how_to/deployment/binder.html
- **mybinder.org docs:** https://mybinder.readthedocs.io/

---

## Summary: The Reliable Fix

For most 2020-2021 Panel apps:

1. **Add to environment.yml:** `jupyter-panel-proxy` (with `pyviz` channel)
2. **Add to notebook:** `app.servable()` after app definition
3. **Comment out:** `pn.serve(app)` (keep for local dev)
4. **Use URL:** `?urlpath=lab/tree/NotebookName.ipynb` (JupyterLab approach)
5. **Add to README:** Step-by-step "Run All Cells" instructions

The `?urlpath=/panel/NotebookName` direct approach often times out — JupyterLab is more reliable.

---

*Updated: January 2026*
*Based on successful migration of wind-calculator project*
