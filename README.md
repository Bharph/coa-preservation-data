# CoA preservation data

Recovered **Conquest of Azeroth (CoA)** data from the last days of the live realms
(shutdown ~2026-09-04). Packed for the Conquest of AzerothCore community.

This is **not** a server, client, or `Spell.dbc`. It is player/client-side data that
is easy to lose and useful for rebuilding classes, spells, items, and quests.

**Not affiliated with Blizzard or the original Ascension team.** No account files,
login data, or character names.

---

## What is in this repo

### `changelog/changelog-entries.ndjson`

**70,437 patch-note rows** from the public Ascension news API
(`api.ascension.gg/api/v3/article/changelog`), 2016-07-23 through 2026-08-31.

Each line is one JSON object: id, date (`group_key` / `created_at`), category,
realm type, and the text of the change.

**Why it helps:** this is the mechanical history (numbers, spec reworks, 4th-spec
notes). Node graphs that never shipped in the frozen client JSON can often be
reconstructed from these entries.

### `spell-tooltips/all-spell-tooltips-merged.ndjson`

**~180,282 in-game spell tooltips** captured with a client addon before shutdown.

Each line: `{ "id", "name", "tt" }`. `tt` is the tooltip the live client showed
(name, range, cast time, description). Empty `tt` means the id existed but had
no tooltip text.

**Why it helps:** covers custom CoA spell ids (including post-February-2026 4th-spec
abilities) that are missing from stock `Spell.dbc` and from the watered-down
web DB. This is names + tooltip text, **not** a binary `Spell.dbc` (no effect
points, aura ids, or targeting bytes).

### `client-content/CharacterAdvancementData.json`

The live client's classless catalog (**23,709** talent/ability/trait nodes).
Shipped in `Data/Content/`. Frozen by Ascension on **2026-02-08** — later 4th-spec
**node graphs** are not in this file.

Each record: class, tab, name, icon, required level, `Spells: [ids]`, AE/TE cost,
tree geometry (`PositionX/Y`, `ConnectedNodes`, …).

**Why it helps:** maps “Bloodmage node X” → spell ids and the pre-Feb-2026 tree
layout. Join dump `class` codes to JSON keys with `class-spellbooks/class-name-map.md`.

### `client-content/SpellRankData.json`

Spell rank chains: first spell id → later ranks by level. Client file, no account
data.

### `class-spellbooks/by-class.json`

Sanitized **in-game spellbooks** for all **21** custom classes, captured 2026-09-02
on CoA realms (Vol'jin and one Wildwalker on Rexxar).

One snapshot per class (highest-level character kept). Character names, gold, and
account data stripped. `realm_mode` is only `Voljin-CoA` or `Rexxar-CoA`.

Several classes are **level 1** and only have starter spells. The denser books are
Demon Hunter, Fleshwarden (Knight of Xoroth), Wildwalker, Pyromancer, Sun Cleric,
Reaper, and Witch Hunter.

Talent *trees* in the dump were mostly empty (`activeGroup` only). Use
`CharacterAdvancementData.json` + changelog for trees; use this file for “what the
client had on the spellbook.”

### `class-spellbooks/class-name-map.md`

Display name ↔ dump `class` code ↔ JSON `Class` key. Six classes were renamed
(Bloodmage = `SonOfArugal`, Templar = `Monk`, Venomancer dump `PROPHET`,
Fleshwarden = `KnightOfXoroth`, Wildwalker = `Primalist`, Spiritmage = `Runemaster`).

### `wdb/voljin-coa/` and `wdb/rexxar-coa/`

Raw client **WDB caches** from those two CoA realms (`creaturecache`, `itemcache`,
`questcache`, `gameobjectcache`, …).

**Why it helps:** creatures, items, quests, and objects the client actually saw.
Vol'jin is the larger set (~23 MB, `itemcache` is most of it). Rexxar is smaller
(~8 MB). These are the files `#cache-dump` asks for. Drop them into the community
consolidator; overlap is expected and deduped.

This is **not** a spawn table. WDB says *what* existed, not *where* it stood.

---

## What is not here (on purpose)

| Missing | Reason |
|---|---|
| `WTF\Account` / SavedVariables | Account folder names and character data |
| SilverDragon / GatherMate coords | This client did not have those addons |
| Packet logs | None captured |
| `Spell.dbc` / full client / server | Already circulating (Proton client + GitHub sources + repack) |
| 4th-spec node graphs after Feb 2026 | Server-side only; use the changelog |

---

## How to use it

- **Caches:** point the [ascension-cache-consolidator](https://github.com/hertigservices/ascension-cache-consolidator) / upload tool at `wdb/voljin-coa` and `wdb/rexxar-coa`.
- **Spells / classes:** `CharacterAdvancementData.json` + `all-spell-tooltips-merged.ndjson` + `by-class.json`.
- **Balance history:** `changelog-entries.ndjson` (grep a spell or spec name).

---

## Provenance

- Changelog: unauthenticated public HTTP API, paginated, pulled 2026-09-02.
- Tooltips + class spellbooks: in-game addon dump 2026-09-02, CoA realms.
- Content JSON: copied from the live 3.3.5a CoA client `Data/Content/`.
- WDB: `Cache/WDB` from the same client, split by realm folder name.

Issues and PRs welcome if you find a cleaner encoding or a duplicate-id map.
