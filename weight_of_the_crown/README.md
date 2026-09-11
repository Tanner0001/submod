# The Weight of the Crown

A Kaiserreich submod scaffold for the Dominion of Canada, based on the GDD in
this repo's conversation history. This is a first-pass scaffold, not a
finished mod - see the `TODO` and `UNVERIFIED` comments scattered through the
script files before considering any of it play-tested.

## Status

| System | State |
| --- | --- |
| Exiles vs. Autonomy (§1, simplified to a read-only bar) | Variables, tiered ideas, hysteresis logic done. GUI is a stub - needs real art and the scripted_gui frame binding. |
| Entente High Command (§2) | Shared Arsenal is functional (faked stockpile transfer). Objective Mandates and Joint Invasions are stubbed pending a verified `add_ai_strategy` type. |
| 49th Parallel Escalation (§3) | Tension tracking via `on_state_control_changed` + decay pulse is functional. Sabotage now applies a real dynamic state modifier (HOI4's Operations system can't target unoccupied hostile states, so this is the verified substitute). Intervention now issues a real `create_wargoal` - the wargoal `type` is still a placeholder pending your call on what it should be. |
| Operation Homecoming (§4) | Phase gating via flags is functional. Phase 2/3 state-targeted bonuses are stubbed at country scope. |
| Operation Anvil (new - NFA + USA naval invasion of France) | Decision chain and gating on `USA = { is_in_faction_with = ENT }` is functional. The occupier/target-region condition is an explicit placeholder (`wotc_anvil_occupier_tag`, always-yes) - not guessed, see `common/decisions/wotc_operation_anvil.txt` header. |

## Known gaps to close before this is playable

1. Real art for `gfx/interface/wotc_exile_autonomy_bar_*.dds` and the
   scripted_gui binding that drives the fill frame from
   `wotc_exile_influence`.
2. Confirm the modifier keys flagged `UNVERIFIED` in
   `common/ideas/wotc_ideas.txt` (`special_forces_cap`,
   `send_manpower_to_ally`, `amphibious_invasion`,
   `enemy_intel_network_gain_factor`) against the actual game data - they
   were not found anywhere in this repo to cross-check.
3. ~~Confirm which 2ACW faction tag(s) should count as "hostile"~~ - resolved:
   WCA (Workers Congress of America) and APG (American Peoples Government).
   USA and ACC are not hostile.
4. Verify a real `add_ai_strategy` type exists for zone/garrison priority
   before shipping Objective Mandates - none was found in this repo to copy.
5. `descriptor.mod`'s `path=` needs to point at your local mod folder.
6. Pick a real `create_wargoal` `type` for
   `wotc_intervention_war_justification` (currently `annex_everything` as a
   placeholder - probably too extreme for a proxy intervention; check what
   type KR's other partial-war/liberation wargoals use).
7. Tune `wotc_49th_parallel_sabotage_modifier`'s numbers
   (`common/dynamic_modifiers/wotc_dynamic_modifiers.txt`) - the values are
   plausible-but-arbitrary, copied in structure (not magnitude) from
   `CAN_nee_resistance`.
8. Fill in `wotc_anvil_occupier_tag` in
   `common/decisions/wotc_operation_anvil.txt` - needs the real tag (and
   possibly specific state/region ids) for whoever currently occupies the
   mainland French territory NFA is meant to liberate. Not guessable from
   this repo (Alsace-Lorraine belongs to GER permanently per the history
   files, which reads as a fixed WWI loss, not this scenario).
