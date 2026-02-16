# MarkItDown (Hanabi fork — magika optional)

Fork of [microsoft/markitdown](https://github.com/microsoft/markitdown) v0.1.4 with `magika` made optional for serverless deployments.

## Why this fork?

MarkItDown's core dependency `magika` (file type detection) pulls in `onnxruntime` + `numpy` + `sympy` = **~176 MB** of transitive deps. This exceeds Vercel's 250 MB serverless function limit when combined with `pymupdf`.

Since our API already knows the MIME type from the client, magika's file detection is unnecessary.

## What changed?

- `pyproject.toml`: removed `magika~=0.6.1` and `onnxruntime` from core dependencies
- `_markitdown.py`: `import magika` wrapped in `try/except`, `_MAGIKA_AVAILABLE` flag guards all magika usage
- When magika is absent, `_get_stream_info_guesses()` falls back to extension/mimetype-based detection via `StreamInfo`

## Branch layout

- **`slim`** (default) — flat layout, used in production (`pip install` / `uv` compatible)
- `main` — original monorepo layout (kept for upstream sync reference)

## Syncing with upstream

When Microsoft releases a new version:

```bash
git fetch upstream
git checkout main
git merge upstream/main
# Apply the magika-optional patch (3 locations in _markitdown.py + pyproject.toml)
git checkout slim
# Copy updated files from main to slim root
```

## When to remove this fork

When [microsoft/markitdown#1234](https://github.com/microsoft/markitdown/issues/1234) is resolved (magika made optional upstream), switch back to `markitdown[docx,pptx,xlsx]>=X.Y.Z` in `requirements.txt`.
