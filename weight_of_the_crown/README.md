# The Weight of the Crown

A Kaiserreich submod scaffold for the Dominion of Canada, based on the GDD in
this repo's conversation history. This is a first-pass scaffold, not a
finished mod - see the `TODO` and `UNVERIFIED` comments scattered through the
script files before considering any of it play-tested.

## Status

| System | State |
| --- | --- |
| Exiles vs. Autonomy (§1, simplified to a read-only bar) | Variables, tiered ideas, hysteresis logic done. GUI is a stub - needs real art and the scripted_gui frame binding. |
| Entente High Command (§2) | Shared Arsenal is functional (faked stockpile transfer). Objective Mandate now uses a real, verified `add_ai_strategy` type (`prepare_for_war`, biasing an ally toward war prep against ENG) instead of an invented zone/garrison type - none exists anywhere in this repo, confirmed by checking every real usage. Joint Invasion Prep still can't target a specific state: HOI4 decisions don't support free-form state-picking (there's no `state_target_trigger` - checked, doesn't exist), only hardcoded per-decision state ids, so it needs a real design choice (which states) before it can be more than the current country-wide stand-in bonus. |
| 49th Parallel Escalation (§3) | **Considered done.** Tension tracking via `on_state_control_changed` + decay pulse is functional. Sabotage applies a real dynamic state modifier (HOI4's Operations system can't target unoccupied hostile states, so this is the verified substitute). Border Guard Deployment now grants `CAN_refugee_crisis_help`, a real existing KR idea (confirmed in `common/ideas/CAN ideas (Canada).txt`) that already does exactly what the GDD wanted (`monthly_population = 1.0`). Intervention issues a real `create_wargoal` with `type = topple_government` (confirmed real, used by several KR ideological-intervention focuses) instead of the too-extreme `annex_everything` placeholder. |
| Operation Homecoming (§4, redesigned) | **Considered done.** Iceland (state 100) is a two-step overture-then-occupy chain (45-day buildup). Faroe Islands (state 337, DEN) gets its own overture-then-annex chain, gated on Iceland. Orkney & Shetland (state 938, ENG - already has `add_claim_by = IMP` and a level-8 naval base + supply hub) is a real invasion objective, not annexed by decision - activates once Canada controls it via `is_controlled_by = CAN`. The landing requires both secured, and its bonus now ramps: `wotc_idea_homecoming_landing_bonus_peak` (amphibious_invasion 0.30) for days 1-4, swapped to the lighter sustain idea (0.15) for days 5-14 via `events/wotc_canada.txt`'s wotc_canada.20/.21 pair, then removed. Every modifier key this system uses (`intel_network_gain`, `special_forces_cap`, `amphibious_invasion`, `supply_consumption_factor`) is now cross-checked against real usage elsewhere in this repo, not guessed. |
| Operation Anvil (new - NFA + USA naval invasion of France) | Fully gated: `USA = { is_in_faction_with = ENT }` for the diplomatic/staging decisions, and the launch decision requires `has_war_with = FRA`. Resolved via kaiserreich.wiki - FRA (Commune of France) occupies mainland France; NFA only holds Algeria/Tunisia/French West Africa/Corsica in this timeline. |
| Load confirmation | A one-time popup (`wotc_canada.30`) fires for the human player at game start, confirming the submod is active. Deliberately *not* a main-menu edit - `interface/frontendmainview.gui` is already fully customized by Kaiserreich, and safely adding to it would require a full shadow-copy that goes stale on KR updates. |
| Dominion Solidarity (Stage 1 - Canada helps fellow Dominions while they're still fighting) | Two targets, same shape (request advisors -> equipment/joint offensive), each verified against its own files rather than assumed: **RAJ** (Dominion of India, vs. HND) - starts in a separate faction with PRF as an ally (confirming PRF isn't a target), real focus paths into the Entente and into war with HND. **NAT** (Natal, vs. the rump South African government after the real, scripted Third Boer War - `SAF_natal_secedes` in `SAF effects (South Africa).txt`) - since Natal's opponent is a dynamically generated civil-war tag with no fixed equivalent of "HND," its final decision gates on `has_war = yes` rather than naming an opponent. |
| Liberation of Fallen Allies (Stage 2 - generalized, not India-specific) | Triggers off a real `on_capitulation` hook (any ENT member that capitulates gets tracked via `save_global_event_target_as`, not hardcoded to RAJ/HND). Three Canada-scoped decisions: Offer Sanctuary -> Rally the Free Legions -> Declare the Liberation, the last of which resolves the wargoal target dynamically (whoever currently controls the fallen ally's capital via `capital_scope.controller`, not who conquered them originally) and issues a real `create_wargoal`. Deliberately single-slot (only the most recent fallen ally is tracked) rather than a full array of simultaneous cases. |
| The Fate of a Homeland (extends Stage 2) | Once Canada personally defeats the tracked conqueror, a country_event (`wotc_canada.40`) fires - mirrors Kaiserreich's own precedent for exactly this kind of choice, "The Fate of America" (`american_civil_war_events.156`). Three options with real, distinct effects: **Restore Independence** (`release`, free, small stability gain), **Establish a Protectorate** (`release_puppet`, PP cost + small stability hit), **Absorb the Conquerors** (`puppet` the defeated conqueror itself rather than restoring the original ally - higher PP cost, bigger stability hit, and an opinion penalty with every other Entente member via the real `KR_disapproves_of_govt` modifier). This is the part that actually restores the fallen country - it doesn't ride on the vanilla peace-conference "liberate" action, since we need the specific three-way choice. |

## Load-time bugs fixed from real in-game error.log (2026-09-12)

Everything in the sections above was written against repo conventions but
never actually run in the engine until now. First launch produced a real
error.log with ~20 distinct errors; every one is now fixed:

- **Decision categories were never registered.** `wotc_49th_parallel`,
  `wotc_entente_command`, `wotc_operation_homecoming`, and
  `wotc_operation_anvil` all threw "Unknown category" - decision categories
  need a separate registration in `common/decisions/categories/` (now
  `wotc_decision_cats.txt`), confirmed against this repo's own
  `annexation_decisions` example. This was the single biggest cause of
  cascading errors across four files.
- **Decision groups can't carry `icon`/`visible` directly** - only
  individual decisions can. The category-level gating conditions moved into
  the new categories file's `available` blocks instead.
- **Scripted triggers can't live inside a decisions file.**
  `wotc_anvil_occupier_tag` was defined in `wotc_operation_anvil.txt` and
  broke that whole file's parsing - moved to
  `common/scripted_triggers/wotc_triggers.txt`.
- **Bare variable comparisons are invalid.** Every `wotc_exile_influence < X`
  / `wotc_border_tension > X` needed `check_variable = { ... }` around it
  (confirmed via this repo's own `CAN_quebec_resistance_level_3`) - fixed
  throughout `wotc_triggers.txt` and `wotc_effects.txt`.
- **`id = X` is not a real state-matching trigger.** The 49th Parallel
  border-state list now tags each state with `set_state_flag =
  wotc_49th_parallel_state` on `on_startup` and checks `has_state_flag`
  instead.
- **`random_events` isn't a real effect** - replaced with `random_list`
  (confirmed via `ALB focus (Albania).txt`), whose weights map to effect
  blocks, not bare event ids.
- **`save_temp_scope_as`/`temp_scope:` aren't real** - replaced with
  `save_event_target_as`/`event_target:` (confirmed via
  `00_on_actions_global.txt`).
- **`interface/wotc_custom_gui.gui`'s `size` field used `x=/y=`** -
  `containerWindowType`'s size takes `width=/height=` (confirmed via
  `interface/browser.gui` - the same file uses `x=/y=` correctly for a
  *different* element type, which is what misled the first pass).
- **The localisation file was missing its UTF-8 BOM**, which HOI4 requires
  and warns about loudly - fixed.

Brace-balance checked across every `.txt`/`.gui` file in this submod after
all fixes; all matched.

## Known gaps to close before this is playable

Everything below is a genuine open item - not a placeholder pretending to be
one. Resolved history has been trimmed; ask if you want it back.

1. **GUI art + binding.** Real art for
   `gfx/interface/wotc_exile_autonomy_bar_*.dds`, and the scripted_gui
   binding that drives the fill frame from `wotc_exile_influence`. I can't
   produce game art myself.
2. **Joint Invasion Prep's target states.** HOI4 decisions can't let a
   player pick an arbitrary state (checked - no such feature exists,
   confirmed against the vanilla decision system and every decision in this
   repo). It needs concrete named target states (the GDD's own suggestion
   was Algiers/Malta) hardcoded per-decision, which is a design choice, not
   a syntax gap. Currently a country-wide stand-in bonus instead.
3. `descriptor.mod`'s `path=` needs to point at your local mod folder -
   one line, whenever you're ready to load it.
4. Tune `wotc_49th_parallel_sabotage_modifier`'s numbers
   (`common/dynamic_modifiers/wotc_dynamic_modifiers.txt`) - the values are
   plausible-but-arbitrary, copied in structure (not magnitude) from
   `CAN_nee_resistance`.
5. Upstate New York's state id for the 49th Parallel border-state list -
   checked this repo thoroughly (state 358 is NYC specifically, not the
   border region) and couldn't resolve it; needs the base game's own state
   files or an in-game check.
