# ENEMY_BALANCE - Elite Hex Warden

Prepared: 2026-03-08
Status: implemented draft (pending simulation validation)
Reference docs:
- `BALANCE_CORE.md`
- `ENEMY_BALANCE_TEMPLATE.md`

## 1. Identity
- Enemy id: `hex_warden`
- Display name: `Проклятый гекс-страж`
- Role: `controller / attrition`
- Encounter band: `5 elite`

## 2. Target Envelope
- Difficulty reference: Normal
- Target TTK: `4.2 turns`
- Target Threat (total HP loss): `~20.5 HP`
- Target Threat per turn: `~4.9 HP`
- Burst cap: `1.75x avg turn threat`

Baseline assumptions used for envelope:
- Baseline hero max HP (Normal): `~114`
- Encounter 5 row from `BALANCE_CORE`.

## 3. Base Stat Draft
- Base HP: `126`
- Runtime HP at encounter 5 elite (avg roll, Normal):
  - `126 * 1.56 * 1.25 = ~246`
- Base damage model: uses shared pipeline (`createEnemy` + `computeEnemyBaseDamage`).
- Elite multiplier impact:
  - HP: `x1.25`
  - Damage scale: `x1.14`
- Boss multiplier impact: `N/A`

## 4. Move Cycle Draft
Cycle length: `4 actions`

| Move key | Type (attack/buff/debuff) | Damage/Hits | Status or Buff effect | Estimated Threat | Estimated EHP delta | Notes |
|---|---|---|---|---:|---:|---|
| `hex_jab` | attack | `1x 5-7` | 35% chance `Burn 1` | 4.6 | 0.0 | chip + attrition opener |
| `rune_sunder` | attack/debuff | `1x 4-6` | `Vulnerable 1` | 5.2 | 0.0 | setup turn, low raw damage |
| `warding_veil` | buff | no direct hit | `+10 block`, `+2 tempAttack next hit` | 2.8 | +10.0 | pressure via delayed burst |
| `soul_tithe` | attack/sustain | `2x 3-5` | heal self `+4` if at least one hit lands | 6.1 | +4.0 | controlled burst turn |

Cycle totals:
- Avg Threat/turn: `~4.7` (within target corridor `4.9 +/- 10%`)
- Peak Threat turn: `~6.1`
- Peak/Avg ratio: `~1.30` (under burst cap `1.75`)
- Avg EHP delta/turn from sustain tools: `~3.5`

## 5. Affix Audit
Envelope checks vs encounter-5 targets from `BALANCE_CORE`.

| Affix | Threat delta | EHP delta | In envelope? (Y/N) | If not, clamp plan |
|---|---:|---:|---|---|
| `enraged` | +18% threat | 0 | Y | none |
| `armored` | 0 | +18 flat EHP | Y | none |
| `regenerating` | 0 | +6 EHP / enemy turn | Y (borderline long fights) | cap regen to 4 for this enemy if sim fails |
| `venomous` | +1.4 to +3.2 threat (depends on landed hits) | 0 | Y | reduce proc chance from 35% to 25% if heroic spike appears |
| `swift` | +25% first damaging turn burst | 0 | Y | if P90 burst fails, limit first-strike boost to 1.18 for this enemy |

## 6. Counterplay Check
- Can player answer this enemy with all 4 heroes: `Expected yes`.
- Is intent readable 1 turn ahead for dangerous moves: `Yes` (critical turn is `soul_tithe` after setup).
- Is there at least one low-threat window in cycle: `Yes` (`warding_veil`).
- Does one relic/event combination trivialize fight: `Potential risk` with high burst + execute stacks; monitor in sim.

## 7. Simulation Results
Seed pack: `SEED_PACK_A` (planned, 60 fixed seeds)
Runs: `planned 720` (60 seeds x 4 heroes x 3 difficulties)

Current stage: implemented in `game.js`; forecast bands still require real sim harness validation.

| Difficulty | Win rate vs this encounter band | Median HP after fight | Median turns to kill | P90 burst taken |
|---|---:|---:|---:|---:|
| Story | expected `76-84%` | expected `70-82` | expected `3.8-4.3` | expected `<=10` |
| Normal | expected `48-58%` | expected `54-66` | expected `4.0-4.6` | expected `<=13` |
| Heroic | expected `20-32%` | expected `36-50` | expected `4.4-5.1` | expected `<=17` |

## 8. Decision
- Status: `ADJUST` (pending implementation and sim data)
- Required tweaks before merge:
  - Validate vulnerable uptime does not over-penalize low-block heroes.
  - Validate regen + soul_tithe does not create excessive EHP on heroic.
- Final constants to implement (initial):
  - `baseHp: 126`
  - move cycle: `hex_jab -> rune_sunder -> warding_veil -> soul_tithe`
  - `hex_jab: [5,7], burn chance 0.35`
  - `rune_sunder: [4,6], vulnerable 1`
  - `warding_veil: +10 block, +2 tempAttack`
  - `soul_tithe: 2 hits [3,5], conditional self-heal 4`
- Notes for `DEV_NOTES.md`:
  - Added first elite enemy blueprint under budget framework.
  - Next step: run seed-pack validation and tune constants from real run data.
