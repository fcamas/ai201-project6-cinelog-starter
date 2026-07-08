# PR Response Doc — CineLog Watchlist Feature

## AI Usage
<!-- Fill in at the end — how you used AI tools during this project -->

## Comment 1 — Rename
**What I did:** Renamed `save_to_watchlist()` to `add_to_watchlist()` in `services/watchlist_service.py` to match the project's `verb_to_noun` convention used by `add_to_collection()`, `remove_from_collection()`, and `get_collection()` (documented in `CONTRIBUTING.md`).

**How I verified:** Ran `grep -rn "save_to_watchlist" --include="*.py" .` across the whole repo before and after the change. Before: three hits (the definition in `services/watchlist_service.py`, the import in `routes/watchlist/watchlist.py`, and the call site in the same file's `add_film` view). After the rename, the same grep returned zero hits — confirming no call site was missed. Also re-ran `pytest tests/ -v` to confirm the rename didn't break anything already covered.

## Comment 2 — Deduplication
**What I did:**
**How I verified:**

## Comment 3 — Missing test
**What I did:**
**How I verified:**

## Comment 4 — Default visibility
**My position:**
**Reasoning:**
**Tradeoff acknowledged:**

## Comment 5 — Sort order
**My position:**
**Reasoning:**
**Engagement with reviewer's point:**

## Comment 6 — Rebase
**What conflicted:**
**How I resolved it:**
**How I verified no conflict remains:**

## PR Description
<!-- Written at the end — feature overview, design decisions, manual testing steps -->
