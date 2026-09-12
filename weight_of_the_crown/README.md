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
