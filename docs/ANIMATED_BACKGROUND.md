# Animated / 3D theme backgrounds

Optional `"background"` block in `theme.json` (still `"format": 1`). An app that does not know the block
ignores it and shows the theme with its colours and header only, so old units keep working.
First theme that uses it: `christmas-live`.

```json
"background": {
  "type": "parallax",
  "motion": "tilt",
  "still":  { "dark": "bg-still.webp", "day": "bg-day-still.webp" },
  "layers": [
    { "dark": "bg-sky.webp",  "day": "bg-day-sky.webp",  "depth": 0.05 },
    { "dark": "bg-near.webp", "day": "bg-day-near.webp", "depth": 0.8 }
  ],
  "particles": { "kind": "snow", "count": 90, "speed": 0.6, "size": [1.5, 4.5], "color": "#FFFFFF", "drift": 0.3 },
  "twinkle": true,
  "userPhoto": { "allowed": true, "keepParticles": true, "dim": 0.25 }
}
```

| Field | Meaning |
|---|---|
| `type` | `parallax` (layered images that move at different speeds = 3D depth). `still` = one image only. |
| `motion` | What moves the layers: `tilt` (rotation-vector / gyroscope sensor), `swipe` (follows the dashboard pager), `auto` (slow sway, for units without a gyroscope), `none`. Apps should fall back `tilt` → `auto` when no sensor exists. |
| `still` | Pre-flattened image used when motion is reduced (low-end device, Driving Mode, battery saver) and as the first frame while layers load. |
| `layers` | Back to front. Each is a transparent WebP of **1600×900** (bigger than a 1280×720 screen so it can shift), with a `dark` and `day` file. `depth` 0–1: how far it moves; 0 = fixed, 1 = moves the most. Suggested max shift: `depth × 80 px` horizontally, `depth × 30 px` vertically, centre-cropped to the screen. |
| `particles` | Drawn on top of the layers. `kind`: `snow` (later: `stars`, `petals`, `candles`). `count` = flakes on a 1280×720 screen (scale by area), `speed` in screen-heights per ~4 s, `size` px range, `drift` = side-to-side sway 0–1. |
| `twinkle` | Small stars in the sky layer slowly pulse (cheap alpha animation). |
| `userPhoto` | `allowed`: the user may pick their own photo instead of the layers (photo is treated as one layer at depth 0.1). `keepParticles`: snow still falls over the photo. `dim`: 0–1 black overlay so text stays readable. |

## Rendering rules for the app

- Respect `LocalReducedMotion`: when true, draw `still` (or the user photo) with no particles and no sensor.
- Stop the sensor and animation when the launcher is not visible (`Lifecycle.State.STARTED`).
- Decode layers at screen size + margin, `RGB_565` for the sky layer on low-RAM units.
- Particles: one `Canvas` + `withFrameNanos` loop, no per-flake composables. 90 flakes is cheap.
- Sensor: `TYPE_GAME_ROTATION_VECTOR`, `SENSOR_DELAY_GAME`, low-pass filter (α≈0.1) and clamp to ±1.
