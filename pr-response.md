## Comment 1 — Rename

**What I did:** Renamed `save_to_watchlist()` to `add_to_watchlist()` in `services/watchlist_service.py` to match the project's verb_to_noun naming convention (matching `add_to_collection()`). Updated the one call site in `routes/watchlist/watchlist.py`, including both the import statement and the function call inside `add_film()`.

**How I verified:** Searched the codebase for any remaining references to `save_to_watchlist` to confirm no call sites were missed, then ran `pytest tests/ -v` to confirm nothing broke.

## Comment 2 — Deduplication

**What I did:** Added a duplicate check to `add_to_watchlist()` following the same pattern as `add_to_collection()` in `collection_service.py`: check the film exists, then check for an existing `WatchlistEntry` with the same `user_id` and `film_id`, and raise a new `AlreadyInWatchlistError` if one is found before creating a new entry.

**How I verified:** Wrote a test (`test_add_to_watchlist_duplicate_raises` in `tests/test_watchlist.py`) that adds the same film twice and confirms the second call raises `AlreadyInWatchlistError`. Ran the full test suite to confirm it passes alongside the existing tests.

## Comment 3 — Missing test

**What I did:** Created `tests/test_watchlist.py` and added `test_add_to_watchlist_nonexistent_film_raises`, modeled directly on `test_add_to_collection_nonexistent_film_raises` in `test_collection.py` — same fixture structure (`app`, `sample_user`), same fake-UUID approach, asserting that `add_to_watchlist()` raises `FilmNotFoundError` for a film ID that doesn't exist.

**How I verified:** Ran `pytest tests/test_watchlist.py -v` to confirm it passes on its own, then `pytest tests/ -v` to confirm the full suite (6 tests) passes together.

## Comment 4 — Default visibility

**My position:** Public by default.

**Reasoning:** I want watchlists to be public because I like being able to talk to other people about a movie or show before either of us has finished it — recommending things, comparing reactions to specific scenes. That fits what CineLog is supposed to be: a community around films, not just a private list.

**Tradeoff acknowledged:** Someone who just wants a private list to track "movies I plan to watch" without anyone seeing it has no way to opt out under this default. That could be addressed later with a visibility toggle (letting `add_to_watchlist` accept an explicit `public` parameter), but that's a separate feature from this decision.

## Comment 5 — Sort order

**My position:** Sort by date added, newest first — agreeing with the maintainer's preference.

**Reasoning:** A watchlist reflects current intent, not a fixed catalog. Films added a while ago may no longer reflect what I actually want to watch — I might have lost interest, or already seen it somewhere else without removing it. Putting the most recent addition on top keeps the list focused on what I'm actually excited about right now, rather than making me scroll past older entries that have gone stale.

**Engagement with reviewer's point:** I agree with the maintainer's reasoning that most users want to see what they added recently — alphabetical order treats every entry as equally relevant regardless of when it was added, which doesn't match how interest in a film actually fades or grows over time. I switched `get_watchlist()` to sort by `date_added` descending, matching the pattern already used in `get_collection()`.

## Comment 6 — Rebase

**What conflicted:**
**How I resolved it:**
**How I verified no conflict remains:**

## PR Description

<!-- Written at the end — feature overview, design decisions, manual testing steps -->
