
# Operator commands

Testing tools typed into the **GameServer console window**, then Enter. They are not
behaviour of the real server.

- A line runs for **every connected client** that has logged in to a character.
  No client connected → the log says `no client is connected`.
- Words are separated by spaces. Numbers must be whole; IDs and levels must be above 0,
  amounts 0 or more. Anything else prints the usage line.
- Every change is saved to the database and pushed to the client right away, so the
  game shows it without a relog.

## Command list

| Command | What it does | Example |
|---|---|---|
| `level` | Shows the character's level and the highest level (100). | `level` |
| `level <number>` | Sets the level (1–100). Sends the new skill cost, any features the new level unlocks, and the level to the client and its fight. Experience starts empty. Lowering the level leaves the higher features open until the client loads again. | `level 60` |
| `coins` | Lists every currency the account holds and how much. | `coins` |
| `coin <name> <amount>` | Sets (not adds) the balance of a currency, 0 – 2147483647. Name is not case sensitive. | `coin Aember 100000` |
| `pets` | Lists the pets the account owns, with their stars. | `pets` |
| `pet <pet ID>` | Gives the account a pet (0 stars). Refused if already owned or unknown. | `pet 1130` |
| `fashions` | Lists the fashion items the account owns, with their type. | `fashions` |
| `fashion <item ID>` | Gives the account a fashion item. Refused if already owned, unknown or withdrawn. | `fashion 101` |
| `kills` | Lists the character's active kill quests: quest ID, monster ID and count. | `kills` |
| `kill <quest ID>` | Completes the kill conditions of that active quest, sending what the real server sent when it saw the kill (quest update, next quests, portals). The server has no fight yet, so this is the only way past a kill quest. | `kill 1004006` |

Wallet, pets and fashion belong to the **account** (shared by its characters). Level
and quests belong to the **character**.

## Currencies (`coin <name> <amount>`)

312 names, from the client table `config_money`. The ones that matter now:

| Name | Used for |
|---|---|
| `Aember` | Main shop currency (256 goods) |
| `Aember_Broken` | Shop (198 goods) |
| `Empire_Perfect` | Shop (52 goods) |
| `Coin_Servent` | Pet shop (41 goods) |
| `Hero_Emblem` | Shop (17 goods) |
| `Empire_Gold_Coin` | Shop (6 goods) |
| `Activity_Coin1` | Shop (3 goods) |
| `Draw_Servent` | Gacha pools 2 and 1001 (pets): 1 per draw, 10 per ten |
| `Draw_Servent_Battle` | Gacha pool 6003 (battle pets) |
| `Draw_Fashion` | Gacha pool 1016 (fashion), needs level 7 |
| `Draw_Fashion_Rarity` | Gacha pool 60009 (fashion), needs level 7 |
| `Draw_Sundries` | Gacha pool 9001 |
| `Promotion_<pet ID>` | That pet's own star-up token, e.g. `Promotion_1130` |
| `Promotion_<kind>_<1-3>` | Star-up token shared by a pet kind: `Bingleng`, `Chixu`, `Diaoluo`, `Fashu`, `Fushi`, `Gongji`, `Huoyan`, `Lingyao`, `Shandian`, `Shengcun`, `Zhaohuan` |

Quick start for testing everything:

```
coin Aember 1000000
coin Draw_Servent 1000
coin Draw_Fashion 1000
coin Coin_Servent 100000
```

<details>
<summary>All other currency names</summary>

`Advlevel_401`, `Advlevel_402`, `BossChallenge_Coin`, `Coin_801` … `Coin_828`,
`Draw_Fashion_Discount`, `Draw_Fashion_Event`, `Draw_Fashion_NewYear`,
`Draw_Servent_Discount`, `Draw_Servent_Event`, `Draw_Servent_Flash`,
`EndgameChallenge_Coin`, `MysticRespec_Coin`, `PetDecompose_Coin_Epic`,
`PetDecompose_Coin_Normal`, `PetDecompose_Coin_Rare`, `PetDecompose_V2_Coin_Epic`,
`PetDecompose_V2_Coin_Normal`, `PetDecompose_V2_Coin_Rare`, `recipe_001` … `recipe_003`,
`Refund_Point`, `Revival_Coin`, `TeamTDChallenge_Coin`, `SpAreaBossPoint_430100` …
`SpAreaBossPoint_430133` (not every number in that range exists).

The full list is `sql/updates/GameServer/0017_currencies.sql`.
</details>

## Pet IDs (`pet <pet ID>`)

186 pets from `config_servant`. An ID reads as **`<family><variant><tier>0`**: the last
digit is always 0, the digit before it is the tier, the rest is the pet.

| Tier digit | Rarity value | Example |
|---|---|---|
| `1` | 2 | `1110` |
| `2` | 3 | `1120` |
| `3` | 100 (highest) | `1130` |

**Category** decides which slot takes the pet:

Each range below is three pets, tiers 1–3 (e.g. `1110-1130` = `1110`, `1120`, `1130`).

- **Battle pets** (category 1, 114 pets; slots 1–3 open at level 1 / 36 / 62):
  `1110-1130`, `1210-1230`, `1310-1330`, `2110-2130`, `2210-2230`, `2310-2330`,
  `3110-3130`, `3210-3230`, `3310-3330`, `4110-4130`, `4210-4230`, `4310-4330`,
  `5110-5130`, `5210-5230`, `5310-5330`, `5410-5430`, `7110-7130`, `7210-7230`,
  `7310-7330`, `8410-8430`, `8510-8530`, `9210-9230`, `9310-9330`, `10310-10330`,
  `10410-10430`, `11310-11330`, `11410-11430`, `12110-12130`, `13210-13230`,
  `14210-14230`, `15210-15230`, `16210-16230`, `16310-16330`, `17210-17230`,
  `18210-18230`, `19210-19230`, `20210-20230`, `21210-21230`.
- **Drop pets** (category 2, 72 pets; slots 4–6 open at level 60):
  `6110-6130`, `6210-6230`, `6310-6330`, `7410-7430`, `8110-8130`, `8210-8230`,
  `8310-8330`, `9110-9130`, `10110-10130`, `10210-10230`, `11110-11130`, `11210-11230`,
  `12210-12230`, `13110-13130`, `14110-14130`, `14310-14330`, `15110-15130`,
  `16110-16130`, `17110-17130`, `18110-18130`, `19110-19130`, `19310-19330`,
  `20110-20130`, `21110-21130`.

Source: `sql/updates/GameServer/0020_servants.sql`.

Raising stars (1–5) is done in the game's pet screen and costs that pet's
`Promotion_<pet ID>` token or a shared `Promotion_<kind>_<n>` token — set them with `coin`.

## Fashion IDs (`fashion <item ID>`)

905 items from `config_fashion`. The type is named after `rpc.FashionType`.

| Type | Name | Items | ID range |
|---|---|---|---|
| 1 | Clothes | 80 | 101 – 12013 |
| 2 | Main weapon skin | 108 | 20001 – 21060 |
| 4 | Head | 4 | 40001 – 40040 |
| 5 | Back | 64 | 50001 – 50064 |
| 6 | Skill skin | 155 | 60101 – 64314 |
| 7 | Body | 66 | 70001 – 70377 |
| 8 | Foot | 60 | 80001 – 80060 |
| 9 | Transfer (teleport effect) | 49 | 90001 – 90050 |
| 10 | Weapon effect | 12 | 30010 – 30120 |
| 12 | Head icon | 181 | 100010 – 190111 |
| 13 | Head frame | 68 | 110002 – 110069 |
| 16 | Hero character | 26 | 160001 – 160026 |
| 17 | Action | 5 | 170001 – 170011 |
| 18 | Dummy | 5 | 180001 – 180005 |
| 19 | Drop effect | 3 | 200001 – 200003 |
| 20 | Idle action | 8 | 170002 – 170013 |
| 21 | Drop sound | 2 | 210001 – 210002 |
| 25 | Kill effect | 3 | 250001 – 250003 |
| 26 | Hit effect | 1 | 260001 |
| 27 | Power god | 1 | 270001 |
| 28 | Level up | 1 | 280001 |
| 101 | Skill pendant 1 | 2 | 270002, 1010001 |
| 102 | Skill pendant 2 | 1 | 1020001 |

Ranges have gaps: not every number in a range is an item. **Cannot be owned** (withdrawn
by the game): `170005`, `170010`, `170013`. The exact list is
`sql/updates/GameServer/0021_fashion.sql`.

## Levels (`level <number>`)

Range 1–100 (`character_level_costs`). Levels that unlock features (function IDs printed
in the log after `features unlocked:`):

| Level | Function IDs unlocked |
|---|---|
| 1 | most basic features (98 IDs) |
| 2 | 101 |
| 7 | 501 |
| 8 | 116, 505, 2004 |
| 20 | 135 |
| 25 | 151, 1001 |
| 30 | 144 |
| 45 | 173 |
| 60 | 127, 180, 509, 510 (also drop pet slots 4–6) |
| 70 | 131 |
| 75 | 156, 172, 174, 517, 2018 |
| 80 | 2005 |
| 81 | 133 |
| 89 | 145 |
| 90 | 1363 |
| 92 | 162, 176 |

51 more features unlock by finishing a quest instead (`unlockKind 2` in
`sql/updates/GameServer/0007_function_unlocks.sql`). Pet slots 2 and 3 open at 36 and 62.

## Kill quests (`kill <quest ID>`)

Use `kills` first: it shows only the quests active right now, which are the only ones
`kill` accepts. The first story quests with a kill condition:

| Quest ID | Map | Monster ID | Count |
|---|---|---|---|
| 1004006 | 100006 | 1022015 | 1 |
| 1005003 | 100007 | 1040011 | 3 |
| 1005009 | 100007 | 1041025 | 1 |
| 1006003 | 100008 | 1042011 | 1 |
| 1006015 | 100008 | 1051011 | 1 |
| 1006023 | 100008 | 1051021 | 1 |
| 1008004 | 100004 | 1111011 | 1 |
| 1009001 | 100005 | 1112000 | 1 |

There are about 280 in all (`target = 'killMonster'` in `quest_conditions`,
`sql/updates/GameServer/0010_quests.sql`). If the log ends with
`NPC actions with no known command: …`, the quest's NPC actions were skipped because
the server does not know them yet.

## Messages you may see

| Log message | Meaning |
|---|---|
| `the connection has not logged in` | That client has not entered a character yet. |
| `unknown operator command; use …` | Unknown word, missing or extra argument, or a bad number. |
| `the game has no currency 'X'` | Name is not in the currency list. |
| `the game has no pet N` / `already owns pet N` | Bad pet ID / already owned. |
| `the game has no fashion N that can be owned` | Unknown or withdrawn fashion ID. |
| `the highest level is 100` | Level above the maximum. |
| `X has no active kill quest N` | Quest is not active or has no kill condition. |
