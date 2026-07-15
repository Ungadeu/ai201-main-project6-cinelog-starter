## Review

## Comment 1 — Rename
**What I did:** Renamed save_to_watchlist to add_to_watchlist in services/watchlist_service.py and updated the call site in routes/watchlist/watchlist.py.

**How I verified:** Used my editor's global search to ensure zero remaining references to save_to_watchlist existed, then ran pytest tests/ -v to ensure the app still runs.

## Comment 2 — Deduplication
**What I did:** Created an AlreadyInWatchlistError and added a database query inside add_to_watchlist to check for an existing WatchlistEntry with the matching user_id and film_id. If an entry is found, the bouncer raises the new error.

**How I verified:** I studied add_to_collection to ensure I was matching the project's exact deduplication pattern, then ran pytest tests/ -v to ensure the train stayed on the tracks and no code was broken.

## Comment 3 — Missing test
**What I did:** Created a new file tests/test_watchlist.py and wrote test_add_to_watchlist_nonexistent_film_raises. I included the necessary app and sample_user fixtures to create an isolated database environment.

**How I verified:** I modeled the test after test_add_to_collection_nonexistent_film_raises in the collection tests. I ran pytest tests/test_watchlist.py -v to ensure the dummy test successfully caught the FilmNotFoundError, followed by running the entire test suite to ensure stability.

## Comment 4 — Default visibility
**My position:** I am keeping public=True as the default state.

**Reasoning:** Much like joining a massive multiplayer server, the core appeal here is community interaction. Leaving watchlists open by default acts as a passive multiplayer feature, maximizing film discovery and letting users easily share their cinematic side-quests with the rest of the player base. 

**Tradeoff acknowledged:** The clear tradeoff is the "accidental broadcast" risk—users expecting a private, single-player storage chest might be caught off guard to find their inventory is visible to the whole lobby. To balance this, I highly recommend we add a clear "Public/Private" toggle in a future UI patch, ensuring players always know exactly which privacy settings they have equipped.

## Comment 5 — Sort Order
**My position:** I am siding with @dev-lead and changing the default sort order to "date-added" (newest first).

**Reasoning:** To borrow a concept from managing massive storage rooms in Minecraft, when you dump your inventory after a long mining session, you usually want the newest blocks right at the top so you can grab them quickly for your next base build. In a movie tracking app, a user's most recently added films represent their "active quests"—the movies they are currently hyped to watch. Sorting alphabetically (putting *Alien* before *Zootopia*) scatters these new additions across the entire inventory, forcing the player to hunt for the exact item they just picked up.

**Engagement with reviewer's point:** While I initially leaned towards alphabetical sorting for a clean, encyclopedia-style inventory look, your point about practical user behavior is spot on. 'Date-added' optimizes for the player's immediate goals and active interests rather than just alphabetical neatness, so I have updated the database query to reflect this change.