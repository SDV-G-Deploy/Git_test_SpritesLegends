# ENEMY_BALANCE_TEMPLATE - SpriteLegends

Use this template before implementing a new enemy in `game.js`.

## 1. Identity
- Enemy id:
- Display name:
- Role (`striker` / `tank` / `controller` / `attrition`):
- Encounter band (1-4 regular / 5 elite / 6 boss):

## 2. Target Envelope
- Difficulty reference: Normal
- Target TTK:
- Target Threat (total HP loss):
- Target Threat per turn:
- Burst cap:

## 3. Base Stat Draft
- Base HP:
- Base damage scale:
- Elite multiplier impact:
- Boss multiplier impact (if relevant):

## 4. Move Cycle Draft
Cycle length: ___ actions

| Move key | Type (attack/buff/debuff) | Damage/Hits | Status or Buff effect | Estimated Threat | Estimated EHP delta | Notes |
|---|---|---|---|---:|---:|---|
| | | | | | | |
| | | | | | | |
| | | | | | | |

Cycle totals:
- Avg Threat/turn:
- Peak Threat turn:
- Avg EHP delta/turn:

## 5. Affix Audit
For each affix, estimate post-affix envelope:

| Affix | Threat delta | EHP delta | In envelope? (Y/N) | If not, clamp plan |
|---|---:|---:|---|---|
| enraged | | | | |
| armored | | | | |
| regenerating | | | | |
| venomous | | | | |
| swift | | | | |

## 6. Counterplay Check
- Can player answer this enemy with all 4 heroes?
- Is intent readable 1 turn ahead for dangerous moves?
- Is there at least one low-threat window in cycle?
- Does one relic/event combination make this enemy trivial?

## 7. Simulation Results
Seed pack:
Runs:

| Difficulty | Win rate vs this encounter band | Median HP after fight | Median turns to kill | P90 burst taken |
|---|---:|---:|---:|---:|
| Story | | | | |
| Normal | | | | |
| Heroic | | | | |

## 8. Decision
- Status: `PASS` / `ADJUST` / `REJECT`
- Required tweaks:
- Final constants to implement:
- Notes for `DEV_NOTES.md`:
