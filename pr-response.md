# PR Response Doc — CineLog Watchlist Feature

## AI Usage
<!-- Fill in at the end — how you used AI tools during this project -->

## Comment 1 — Rename
**What I did:** Renamed `save_to_watchlist()` to `add_to_watchlist()` in `services/watchlist_service.py` to match the project's `verb_to_noun` convention used by `add_to_collection()`, `remove_from_collection()`, and `get_collection()` (documented in `CONTRIBUTING.md`).

**How I verified:** Ran `grep -rn "save_to_watchlist" --include="*.py" .` across the whole repo before and after the change. Before: three hits (the definition in `services/watchlist_service.py`, the import in `routes/watchlist/watchlist.py`, and the call site in the same file's `add_film` view). After the rename, the same grep returned zero hits — confirming no call site was missed. Also re-ran `pytest tests/ -v` to confirm the rename didn't break anything already covered.

## Comment 2 — Deduplication
**What I did:** Followed `add_to_collection()`'s pattern exactly: added an `AlreadyInWatchlistError` exception class (mirroring `AlreadyInCollectionError`), and in `add_to_watchlist()` I query for an existing `WatchlistEntry` with the same `user_id`/`film_id` before creating a new one, raising `AlreadyInWatchlistError` if found. I also updated `routes/watchlist/watchlist.py`'s `add_film` view to catch both `FilmNotFoundError` (404) and the new `AlreadyInWatchlistError` (409) — matching `routes/collection.py`'s `add_film` view's status codes exactly. Note: the route previously didn't catch `FilmNotFoundError` at all (an uncaught exception would have 500'd), so this fix closes that gap too, not just the new duplicate case — "handling" a duplicate has to mean the caller gets a clean response, not a crash, and leaving the sibling exception unhandled while I was already touching this code path felt like leaving an obvious follow-on bug in a PR I could see would need review again.

**How I verified:** Used the Flask test client (not the dev server — see note in Comment 6/environment about a pre-existing `app.py` double-import bug unrelated to this change) to add a film, then call `add_to_watchlist` again for the same user/film. First call returned `201`; second returned `409` with the expected error message. Also called with a nonexistent `film_id` to confirm the previously-silent 500 path now returns a clean `404`. Ran `pytest tests/ -v` — all 4 existing collection tests still pass.

## Comment 3 — Missing test
**What I did:** Created `tests/test_watchlist.py`, using `tests/test_collection.py` as the direct model — same `app`/`sample_user`/`sample_film` fixture structure, same in-memory SQLite config. I wrote `test_add_to_watchlist_nonexistent_film_raises`, the specific test requested in the comment, modeled line-for-line on `test_add_to_collection_nonexistent_film_raises` (same fake-UUID sentinel, same `pytest.raises(FilmNotFoundError)` pattern). I also included `test_add_to_watchlist_creates_entry` (happy path) and `test_add_to_watchlist_duplicate_raises` (dedup from Comment 2), since `CONTRIBUTING.md` states new service functions need all three test categories — happy path, duplicate/conflict, nonexistent ID — and `test_collection.py` already establishes that as the working precedent for this codebase, not just a guideline.

**How I verified:** Ran `pytest tests/test_watchlist.py -v` — all 3 tests pass. Then ran the full suite (`pytest tests/ -v`) to confirm the new file doesn't interfere with `test_collection.py`'s fixtures or tests (each test file gets its own isolated in-memory DB per the `app` fixture's `create_all`/`drop_all` lifecycle, so there's no cross-file state).

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
