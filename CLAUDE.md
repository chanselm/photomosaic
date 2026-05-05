# CLAUDE.md — photomosaic (chanselm fork)

This is a fork of [`worldveil/photomosaic`](https://github.com/worldveil/photomosaic) with three patches applied over the upstream `master` branch. Do not open PRs to the upstream repo — it appears inactive.

## Patches over upstream

### 1. Deterministic SHA-256 cache keys (`emosaic/caching.py`)
**Commit:** `028e164`

Python's `hash()` is randomised per-process since Python 3.3 (PEP 456), so the cache key changed on every run and the FAISS index was never reused. Replaced with `hashlib.sha256()` in both `EmbeddingsCacheConfig` and `MosaicCacheConfig`.

### 2. Optional dlib dependency (`emosaic/faces.py`)
**Commit:** `8a1c751`

`dlib` requires `cmake` to build from source and is only needed for face-detection features. Wrapped the import in a `try/except` and guarded all dlib-dependent code with `_DLIB_AVAILABLE`. The tool runs normally without dlib installed.

### 3. Removed ipdb debug breakpoints (`emosaic/__init__.py`)
**Commit:** `56ca203`

Two `ipdb.set_trace()` calls were left in exception handlers inside `mosaicify()`. Removed; the outer handler already logs the traceback and returns `None`.

## Key files

- `emosaic/__init__.py` — core `mosaicify()` function (tile search loop)
- `emosaic/utils/indexing.py` — FAISS nearest-neighbour indexing
- `emosaic/caching.py` — pickle-based multi-scale index cache
- `emosaic/utils/image.py` — image vectorization, tile division
- `mosaic.py` — CLI entry point

## Applying future patches

Make changes here in the fork, commit, push to `chanselm/photomosaic`, then update the submodule pointer in `photo-mosaic`:

```bash
# in photo-mosaic/
git -C photomosaic pull
git add photomosaic
git commit -m "chore: update photomosaic submodule to latest"
```
