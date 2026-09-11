# Art needed here

`wotc_custom_gui.gfx` references two textures that don't exist yet and need
real art before the mod loads without missing-texture placeholders:

- `wotc_exile_autonomy_bar_bg.dds` - static background frame for the bar.
- `wotc_exile_autonomy_bar_fill.dds` - a 100-frame horizontal filmstrip (one
  frame per percentage point of `wotc_exile_influence`), matching the
  technique vanilla HOI4 uses for `GFX_subject_progressbar` and
  `GFX_exile_legitimacy_progressbar_long` in `interface/countrypoliticsview.gui`.

Also missing: the scripted_gui binding that actually sets the fill sprite's
`frame` from the `wotc_exile_influence` variable each UI refresh - that
belongs in a new `common/scripted_guis/wotc_scripted_gui.txt`, not here.
