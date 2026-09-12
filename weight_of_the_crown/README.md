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
| Operation Homecoming (§4, redesigned) | **Considered done.** Iceland (state 100) is a two-step overture-then-occupy chain (45-day buildup). Faroe Islands (state 337, DEN) gets its own overture-then-annex chain, gated on Iceland. Orkney & Shetland (state 938, ENG - already has `add_claim_by = IMP` and a level-8 naval base + supply hub) is a real invasion objective, not annexed by decision - activates once Canada controls it via `is_controlled_by = CAN`. The landing requires both secured, and its bonus now ramps: `wotc_idea_homecoming_landing_bonus_peak` (amphibious_invasion 0.30) for days 1-4, swapped to the lighter sustain idea (0.15) for days 5-14 via `events/wotc_canada.txt`'s wotc_canada.20/.21 pair, then removed. Every modifier key this system uses (`intel_network_gain`, `special_forces_cap`, `amphibious_invasion`, `supply_consumption_factor`) is now cross-checked against real usage elsewhere in this repo, not guessed. |
| Operation Anvil (new - NFA + USA naval invasion of France) | Fully gated: `USA = { is_in_faction_with = ENT }` for the diplomatic/staging decisions, and the launch decision requires `has_war_with = FRA`. Resolved via kaiserreich.wiki - FRA (Commune of France) occupies mainland France; NFA only holds Algeria/Tunisia/French West Africa/Corsica in this timeline. |

## Known gaps to close before this is playable

1. Real art for `gfx/interface/wotc_exile_autonomy_bar_*.dds` and the
   scripted_gui binding that drives the fill frame from
   `wotc_exile_influence`.
2. ~~Confirm the modifier keys flagged `UNVERIFIED`~~ - resolved for
   everything Operation Homecoming/Entente Cohesion touches:
   `special_forces_cap` and `amphibious_invasion` were real all along
   (confirmed via `common/ideas/CAN ideas (Canada).txt`); `intel_network_gain`
   replaced the invented `enemy_intel_network_gain_factor`; `lend_lease_tension`
   replaced the invented `send_manpower_to_ally`. Remaining unverified keys
   are now confined to §2 Entente High Command's still-stubbed decisions
   (see item 4).
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
8. ~~Fill in `wotc_anvil_occupier_tag`~~ - resolved: gated on
   `has_war_with = FRA` (Commune of France), confirmed via kaiserreich.wiki
   and this repo's own tag/localisation data.
