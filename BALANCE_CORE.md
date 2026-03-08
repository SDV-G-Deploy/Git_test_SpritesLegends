# BALANCE_CORE - SpriteLegends

Version: v2
Last updated: 2026-03-08
Scope: stage-based run balance framework with progressive bosses.

## 1. Goal
- Keep runs readable and beatable while preserving build variance.
- Make stage progression predictable: regular fights -> mini-boss -> boss checkpoints.
- Ensure first boss is intentionally more forgiving for onboarding.

## 2. Core Structure
- Stage model:
  - Each stage has 4 core fights: `3 regular + 1 mini-boss`.
  - Every 3rd stage adds an extra boss fight after mini-boss.
- Between regular fights inside a stage:
  - short heal + chance of random event (`eventChance` by difficulty).
- After each stage:
  - guaranteed `iron hub` with broad purchase options.
- After each boss:
  - guaranteed `boss hub` with one `MEGA` upgrade choice.

## 3. Target Matrix (Normal)
Reference envelope for stage fights:

| Encounter Type | Target TTK | Target HP Loss | Burst Cap |
|---|---:|---:|---:|
| Regular-1 | 2.8 | 0.08 | 1.55 |
| Regular-2 | 3.1 | 0.10 | 1.55 |
| Regular-3 | 3.4 | 0.12 | 1.60 |
| Mini-boss | 4.2 | 0.18 | 1.75 |
| Boss checkpoint | 4.8-5.6 | 0.22-0.30 | 2.00 |

## 4. Difficulty Transform

| Difficulty | EHP Mult | Threat Mult |
|---|---:|---:|
| Story | 0.88 | 0.86 |
| Normal | 1.00 | 1.00 |
| Heroic | 1.15 | 1.18 |

## 5. Boss Progression Contract
Boss index is counted by checkpoint order (every 3rd stage):

1. Boss #1: `weak little gragon`
   - Intentional onboarding boss.
   - ~15% weaker than baseline dragon profile (HP + damage profile).
2. Boss #2: `standart dragon`
   - Baseline dragon profile.
3. Boss #3 and later:
   - Random dragon variant (`elder/void/storm`) with scaling ramp.
   - Increase both HP and threat by boss index.

Rule:
- First boss should be clearable by average build on Normal with reasonable play.

## 6. Economy Rules
- Intra-stage transition:
  - +4% max HP short rest.
  - random event may trigger.
- Stage completion:
  - +12% max HP rest,
  - guaranteed iron hub with broad options,
  - S-tier options cost more HP than common options.
- Boss completion:
  - +14% max HP rest,
  - guaranteed boss hub with `MEGA` reward selection.

## 7. Move/Budget Scoring (Design-Time)
- `DirectThreat = expected landed damage`
- `BleedThreat = 4 * stacks`
- `BurnThreat = 5 * stacks`
- `VulnerableThreat = 0.30 * expected next hit`
- `EnemyBlockToEHP = +block`
- `TempAttackThreat = temp_attack * affected_hits`

Checks:
- Encounter total threat within target corridor `+/-10%`.
- Peak turn remains under burst cap.
- Affix variant remains in playable envelope.

## 8. Affix Delta Guardrails
- `enraged`: threat x1.18
- `armored`: +18 EHP
- `regenerating`: +6 EHP per enemy turn
- `venomous`: extra bleed pressure on hit
- `swift`: +25% first damaging turn burst

Guardrail:
- If specific enemy + affix exceeds envelope, clamp enemy-specific tuning first.

## 9. Release Gates
- Story win rate: `70-85%`
- Normal win rate: `45-60%`
- Heroic win rate: `18-35%`
- Boss #1 Normal clear rate should not be lower than `~50%` for baseline runs.
- No single encounter with `P90 burst > burst cap + 10%`.

## 10. Integration Points (game.js)
- Stage generation and boss progression:
  - `RUN_STRUCTURE`, `buildBossProfile`, `generateEncounters`
- Enemy scaling:
  - `createEnemy`, `handleDragonPhaseTransition`
- Between-fight flow:
  - `finishEncounter`, `showIntermissionEvent`
- Stage/boss hubs:
  - `showStageHub`, `showBossHub`

## 11. Change Discipline
For each balance iteration:
- List changed constants.
- State expected effect on first boss clear rate and stage pacing.
- Playtest at least 2 runs per difficulty.
- Update `DEV_NOTES.md` with findings and next risks.
