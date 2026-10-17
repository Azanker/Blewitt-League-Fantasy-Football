# Blewitt League history dashboard: handover

Written 17 October 2026 for Andrew Zanker (Former Captains Club, Blewitt League). Companion to CATTERY_DASHBOARD_HANDOVER.md; same structure, same recipe, so either file can seed a rebuild of either league.

## 1. What exists now

| Item | Where | Notes |
|---|---|---|
| Hosted dashboard (Claude Dashboard artifact) | https://claude.ai/artifact/NQb7NXiifJ5bwvP1JwjTSm | Private until shared from its Share menu. Eleven CSV datasets attached as files; page is `files/index.html` (91 KB). Default highlighted team: Former Captains Club (`dash/params.team`). |
| Standalone HTML | `E:\UF Blewitt league\Blewitt_League_2015_to_2026.html` | Same page with all eleven CSVs and d3 v7 embedded and a small `dash` shim. About 6.4 MB. Opens from disk, works offline. |
| Derived CSVs | `E:\UF Blewitt league\dashboard_data\*.csv` | The eleven datasets in section 5. |
| Source folder | `E:\UF Blewitt league` | Workbooks listed in section 3. |

## 2. Dashboard tabs

Identical to the Cattery build: All-time, Season, Rivalry, Players, Drafts, Movement, Records, Premiership squads, League admin. Clicking a ladder, franchise or head-to-head row sets the highlighted team; the Rivalry tab opens on the highlighted team.

## 3. Source files and what each contributed

| File | Sheets used | Years | Used for |
|---|---|---|---|
| Buyla League Bible full details.xlsx | `YYYY Ladder`, `YYYY Draft`, `YYYY Player movement(s)`, `YYYY Detail(s) round Matchups`, `All team scores by round`, `Teams over time` | 2015 (ladder, draft only) and 2016 to 2022 | Ladders with post-finals rank, matchups, player scores with match stats, drafts (all 36 rounds), adds/drops/trades, franchise history |
| Bulya League 2016 and 17 drafts UF.xlsx | `Kept Delisted 16` | 2016 | Keeper lists |
| Blewitt League 2023 season ladder and draft results.xlsx | `Ladder`, `Sheet2`, `League Transactions` | 2023 | Home-and-away ladder, draft (picks 1 to 170 are keepers, 171 to 370 live), adds, delistings, trade headlines |
| AFL Fantasy Blewitt 2024 Full Details.xlsx | `Fixture`, `LAdders (2)`, `Teams by round` | 2024 | Matchups with scores (rounds 1 to 11, 16 to 23), ladder after round 20, squad scores per round |
| Blewitt league Scrape 2025.xlsx | `h2h matchups`, `h2h matchup with players`, `Preseason draft`, `Add drops during the season`, `End of season squad` | 2025 | Matchups, player scores, draft and keepers, full event log (adds, drops, cuts, trades, pick transfers, settings) |
| 2026 export (A_FantasyKeeperLeague_*.csv) | matchups, lineups, ladder, draft_picks, keepers, transactions, teams, players | 2026 | Everything for 2026, team colours, the player pool used to resolve 2025 initials |

Not used: NTP side-comp workbook, draft kits and rankings, 2019 season details (duplicates the Bible), `Blewitt League.xlsx` (duplicates the Bible), 2021 and 2023 FCC-only keeper sheets, delisting scratch sheets, logos folder.

Missing entirely: 2015 round results; all 2023 match results and finals; 2024 draft player names and 2024 transaction player names (both were pasted as images and are not in the cells); 2024 lineup slot and captain flags; keeper lists for 2017 to 2022 and 2024.

## 4. Parsing rules that matter

Bible ladders: `W-L-D` cells became dates; wins = day, losses = month, draws = year minus 2000. `Rank` is post-finals (rank 1 beat rank 2 in the last finals round every year 2016 to 2022, verified). Ladder ties are broken on points for, which is the league setting recorded in the 2025 log and matches the 2024 and 2026 platform ladders.

Bible matchups: `All team scores by round` is already one row per team per matchup. Finals = label mentions "final" or the round has fewer than 10 team rows. Home-and-away rounds skip the AFL byes (e.g. 2016 rounds 13 to 15 absent; 2020 is 14 rounds plus finals in rounds 16 to 18).

Bible detailed matchups: blocks of round / team1 / team2 / scores / `Player` header; left team columns A to K, position in column L, right team M onward; rows after `Emergencies` are bench; status lines start `W,` `L,` `D,`. Trailing ` C` / ` V` on a name = captain / vice. Some round headers are missing or duplicated in the source, so each block's round is resolved by matching its two scores to the matchup table (four fixes). A blank team name (2017 round 1) is resolved from the matchup table. Team score = starters + same-line emergency for a starter on zero + captain again; 1,305 of 1,326 team-rounds reconcile exactly, the `counted` flag records the emulation.

Bible drafts: per-team vertical blocks; `Pick` is `round.pick` as a float (1.1 can mean pick 10), so overall = minus the second column. Player string `Name CLUB - POS [note]` with the old two-letter club codes (AD, BL, CA, CW, ES, FR, GE, GC, GWS, HW, ME, NM, PA, RI, SK, SY, WC, WB) and positions B/C/R/F.

Bible movements: two-row pairs (player + team, then type + date). `Traded To` sits in column C of the player row with the destination in column D. Dates carry no year.

2024 fixture sheet: one column per round, blocks of 16 rows (team, coach, position, score, "Full time", opponent score, opponent). Rounds 21 to 23 are finals (2 + 3 + 2 games). The `Teams by round` dump lists about 27 players per team per round in 14-row blocks with no slot or captain flag; the captain's score is doubled in it, so the top score of a team-round is halved when it is even and at least 1.3 times the next score [ASSUMED, 163 of 190 team-rounds].

2025 scrape: matchup numbers within a round follow the `h2h matchups` sheet order (verified on round 1 rosters). Captain scores are already doubled; when the captain did not play the vice-captain is doubled instead. Names are initials: resolved by initial + surname against the pool of full names from the 2026 export, 2024 dump, Bible and 2023 draft, with the AFL club breaking ties and recent names preferred; spelling variants (Matt/Matthew, Lachie/Lachlan, Zac/Zach, Cam/Cameron and so on) are merged on a normalised key. Only `J. Blight` is unresolved. The event log parses Add/Drop, Add, Drop, Add (Commish), Preseason Cuts, Trade (both sides' assets), Pick Transfer and Commish Update blocks.

Grand final rule for 2024 to 2026: the last finals-round game between the two teams that had not lost a final (top seeds with a bye count as alive). 2024 Belair over Dumpster Fire, 2025 Langy's Beard over Dumpster Fire, 2026 VP Phoenix over Belair.

Name collisions: Josh Kennedy (SYD/WCE), Tom Lynch (RIC/NTH/ADE), Bailey Williams (WBD/WCE) carry the club in brackets where known, "(?)" otherwise.

## 5. Franchise mapping (by coach, assumed)

| Historical names | Current franchise | Coach |
|---|---|---|
| Former Capt Club, Former Capts Club, FCC, "You" (2023 draft sheet) | Former Captains Club | Andrew Z |
| The Wanger Bangers (2015), Moore's Mob | Moores Mob | Kane M |
| Lang City Potatoes (2020), Carry On McCluggage (2021), Lucky's Dad's Team (2024) | Langy's Beard | Zack L |
| Kelsicles (2015 to 2020), Dumpster fire | The Dumpster Fire | Matthew K |
| Parriah Park (2015 to 2021) | VP Phoenix | Steve P |
| Legion of Blew (James B, 2015 to 2024) | former franchise | |
| Blink City Bitaches, The Walking Wounded (Lincoln B, 2015 to 2023) | former franchise | |
| #1. Z (Zoey, 2015 to 2016) | former franchise | |
| Belair Express II (David S, 2017 on), The RBri Express (Rory O), Dossatrons (Aidan M) | unchanged | |
| I fought the Law (Adam L, 2024 on), MAN BEAR PIG (Damien H, 2025 on) | new franchises | |

Whether I fought the Law inherited Blink City's list in 2024, and MAN BEAR PIG Legion of Blew's in 2025, is not recorded; they are kept as separate franchises.

## 6. Derived datasets

| Dataset | Rows | Key columns |
|---|---|---|
| teams | 13 | team_id, team, coach, colour, status, historical_names |
| hist_ladders | 120 | year, pos, ha_pos, team, franchise, coach, w, l, d, pf, pa, pct, pts, source, key |
| hist_matchups | 1,960 | key, year, round, round_label, team, franchise, score, opponent, opp_franchise, opp_score, stage, result, margin |
| hist_seasons | 12 | year, premier, runner_up, minor_premier, wooden_spoon, top_pf, top_pf_team (+ _franchise), teams, rounds, note |
| hist_drafts | 3,487 | key, year, overall, round, pick_in_round, team, franchise, player, afl_team, position, note, source |
| hist_player_scores | 50,461 | key, year, round, team, franchise, player, pos, slot, captain, points, counted, ki, hb, ma, ho, ta, ff, fa, gl, bh |
| player_years | 4,926 | key, year, franchise, team, player, how, games, pts |
| hist_moves | 3,247 | key, year, date, type, player, position, from_team, from_franchise, to_team, to_franchise, detail |
| premier_squads | 255 | key, year, franchise, team, player, pos, slot, captain, points, counted, gf_round, opponent |
| keepers | 863 | key, year, team, franchise, player, position, afl_club |
| admin | 62 | key, section, year, item, text (includes every build note) |

## 7. Honour roll as built

| Year | Premier | Runner-up | Minor premier | Wooden spoon |
|---|---|---|---|---|
| 2015 | Kelsicles | Langy's Beard | Kelsicles | The RBri Express |
| 2016 | Parriah Park | Langy's Beard | Langy's Beard | #1. Z |
| 2017 | Kelsicles | Legion of Blew | Legion of Blew | Moores Mob |
| 2018 | Former Captains Club | Parriah Park | Moores Mob | Blink City Bitaches |
| 2019 | Former Captains Club | The RBri Express | The RBri Express | Blink City Bitaches |
| 2020 | Dossatrons | Kelsicles | Kelsicles | Blink City Bitaches |
| 2021 | Blink City Bitaches | Belair Express II | Belair Express II | Parriah Park |
| 2022 | Belair Express II | Langy's Beard | Belair Express II | Blink City Bitaches |
| 2023 | [UNVERIFIED] not in source | not in source | Moores Mob | Blink City Bitaches |
| 2024 | Belair Express II | The Dumpster Fire | Moores Mob | I fought the Law |
| 2025 | Langy's Beard | The Dumpster Fire | VP Phoenix | The RBri Express |
| 2026 | VP Phoenix | Belair Express II | Dossatrons | MAN BEAR PIG |

2015 premier is the Bible's rank 1 (no match results to confirm a grand final). The 2015 wooden spoon is The RBri Express on the points-for tiebreak (four teams on 20 points); the Bible itself ranks Dossatrons last by percentage.

## 8. Build files (session workspace, not on your drive)

`common.py` (team aliases, franchise map, player-string parser, name normaliser), `parse_bible.py`, `parse_recent.py` (2023 to 2026), `build.py` (joins, validation, name resolution, the eleven CSVs, notes), `standalone.py` (embeds d3 and the CSVs with a dash shim), `page/part1_style.html` to `part4_pages.html` (the dashboard page). Re-run order: parse_bible, parse_recent, build, standalone.

## 9. Open items

- Ask the commissioner for 2023 results and finals (the platform's 2023 season page or screenshots of each round).
- 2024 draft names and 2024 transactions: re-export from the platform if it still holds 2024; the workbook copy only has images.
- Confirm the franchise lineage for I fought the Law (2024) and MAN BEAR PIG (2025), and the 2024 captain rule.
- Keeper lists 2017 to 2022 and 2024 would let `how` show Keeper rather than Pick N for those seasons.
- Bible per-player match stats (kicks, handballs, marks, hitouts, tackles, frees, goals, behinds) are in `hist_player_scores` for 2016 to 2022 and 2026 but not yet shown on any tab.
