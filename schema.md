# Football Score Data schema

`current-week.json` is the live Tue–Mon betting feed used by the phone app.

## Source priority

1. BetOnline scraper status is authoritative for final wager results.
2. A local phone override may temporarily display Won/Lost/Auto for a wager or leg while BetOnline is still open.
3. ESPN supplies live score/game state and may auto-grade supported markets and props while games are in progress.

Phone overrides are intentionally not written back to this repository.

## Updating during the week

Repeated BetOnline scrapes should merge by `betId`. Existing bets are updated rather than duplicated; newly placed bets are appended. Do not remove an existing wager merely because a later scrape fails to return it without first checking whether it was graded/filtered by BetOnline.

## Matching games

`awayTeam` and `homeTeam` are normalized names used to reconcile a BetOnline wager with ESPN. Once confidently matched, save the stable ESPN event identifier in `espnEventId` and set `matchConfidence` to `exact` (or another explicit confidence level if added later).

The phone app should use `espnEventId` as its primary lookup key. Team-name matching is a fallback only.

If a wager or leg cannot be confidently reconciled, leave `espnEventId` as `null`, set `matchConfidence` to `needs_review`, and list the unresolved matchup in the top-level `reconciliation.unmatchedGames` array. Never silently attach an ambiguous wager to an ESPN event.

## Structured parlays

Parlays and Same Game Parlays use a `legs` array. Each leg may contain:

- `legNumber`
- `sport`
- `awayTeam` / `homeTeam`
- `market`
- `selection`
- `side`
- `line`
- `odds`
- `period`
- `player`
- `propType`
- `eventDate` / `eventTime`
- `status`
- `espnEventId`
- `matchConfidence`
- `raw`

For a Same Game Parlay, each leg normally points to the same ESPN event ID. For a traditional multi-game parlay, each leg gets its own ESPN event ID.

BetOnline leg status is authoritative when supplied. One LOST leg makes the parent parlay LOST; all WON legs make the parent parlay WON; otherwise the parent remains open unless BetOnline itself provides a final parent status.

## Weekly archive

The betting week runs Tuesday through Monday. At rollover, copy the completed `current-week.json` to `weeks/YYYY-MM-DD.json`, where the filename is the Tuesday start date, then initialize the new current week.

## Prop grading

Supported prop legs may include a `propType` and grading parameters. Examples include `anytime_td`, `passing_tds_gte`, `receptions`, `receiving_yards`, `rushing_yards`, and `passing_interceptions`. Unsupported props remain open until manually overridden on the phone or definitively graded by BetOnline.
