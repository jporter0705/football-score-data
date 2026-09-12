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

`awayTeam` and `homeTeam` are normalized names used to match ESPN games. Once confidently matched, save the ESPN event identifier in `espnEventId`. Ambiguous or unmatched wagers must be flagged rather than silently attached to a game.

## Weekly archive

The betting week runs Tuesday through Monday. At rollover, copy the completed `current-week.json` to `weeks/YYYY-MM-DD.json`, where the filename is the Tuesday start date, then initialize the new current week.

## Prop grading

Supported prop legs may include a `gradingType` and grading parameters. Examples include `anytime_td`, `passing_tds_gte`, `receptions_over`, and `receiving_yards_over`. Unsupported props remain open until manually overridden on the phone or definitively graded by BetOnline.
