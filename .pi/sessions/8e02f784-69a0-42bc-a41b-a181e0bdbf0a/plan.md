---
status: done
updatedAt: 2026-04-03T13:36:51.819Z
---

# Make Graphviz an Optional Dependency

## 1. Objective

Make system Graphviz (`dot`, `gvpr` binaries) and `graphviz2drawio` Python package optional. Users can use terravision for DOT/JSON output without installing system Graphviz. Rendered formats (PNG, SVG, PDF) require Graphviz as optional install.

## 2. Context

### Key files:
- `pyproject.toml` / `requirements.txt` — dependency declarations
- `resource_classes/__init__.py` — `Canvas.render()` uses graphviz Python lib (keep as required)
- `modules/drawing.py` — `graphviz2drawio` import, `gvpr` and `dot` system calls
- `modules/helpers.py` — `check_dependencies()` currently hard-requires `dot`/`gvpr`
- `modules/tfwrapper.py` — `convert_dot_to_json()` calls system `dot`
- `terravision/terravision.py` — CLI, `preflight_check()`

### Strategy:
- Keep `graphviz` Python package as required (lightweight DOT builder)
- Move `graphviz2drawio` to optional extras
- System `dot`/`gvpr` only checked when format needs rendering
- Add `dot`/`gv` output that skips rendering
- Graceful errors when Graphviz needed but missing

## 3. Tasks

1. Update `pyproject.toml`: `graphviz2drawio` → optional extras `[project.optional-dependencies.drawio]`
2. Update `requirements.txt`: remove `graphviz2drawio`
3. Update `modules/helpers.py`: `check_dependencies()` split into required/optional
4. Update `modules/drawing.py`: lazy import `graphviz2drawio`, support `dot`/`gv` format without system graphviz, graceful errors
5. Update `modules/tfwrapper.py`: `convert_dot_to_json()` check for system graphviz
6. Update `terravision/terravision.py`: format-aware preflight
7. Update `Dockerfile` if needed

## 4. Validation

- [ ] `graphviz2drawio` in optional deps extras
- [ ] `dot`/`gv` output works without system Graphviz
- [ ] Clear error when system Graphviz missing for PNG/SVG
- [ ] `graphdata` command works without system Graphviz
- [ ] Existing tests pass
