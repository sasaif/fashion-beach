# Feature list — MVP 1

"Tested" = verified automatically on the **production build** in headless Chrome with real CDP touch events (unless noted), see the evidence column. Tests run with a software GPU, so they prove behaviour, not phone performance.

## Implemented and tested
| Feature | Evidence |
|---|---|
| Loading screen with progress bar (before JS), fades out when ready; error message + reload button if loading fails / no WebGL | `docs/step7/<size>/01_splash.png`, step7-flow |
| Main menu (Wardrobe, Face, Inventory, Play, Wave, quality toggle); avatar turntable by drag | step7-flow, step5-flow (regression) |
| Face customization: noses + mouths, preview, try-on of locked items, equip, reset, save | step7-flow (`05_visage_change.png`), step5-flow |
| Wardrobe: tops / jackets / bottoms / shoes, preview, equip, remove, cancel, unsaved-changes dialog, save | step7-flow, step5-flow |
| Inventory: owned / locked filters, detail, "try on" shortcut | step7-flow, step5-flow |
| Local save (versioned, repaired if corrupted), restored after reload incl. current screen | step7-flow "reload: outfit, face, quality, screen restored", step5-flow corrupted-save cases |
| Beach world (procedural): sand, animated toon ocean, palms, umbrellas, boardwalks, pier, shop, plaza with bonfire/benches/stage | `docs/step6/views/`, `docs/step7/gameplay_60s_390x844.mp4` |
| Analog joystick: walk vs run by stick distance, camera-relative direction, stop on release | step7-flow, step6-flow |
| Camera: drag to rotate (pitch clamped), pinch zoom, wall avoidance | step7-flow, step6-flow |
| Collisions: shop walls, palms, benches, bonfire, ocean limit, world edge, slide along walls; gait slows when blocked (no running in place) | step6-flow (6 collision checks), step7-flow (shop wall) |
| Animations matched to ground speed (no foot sliding), wave | step6-flow (timescale check), step3 regression (`step3-interactive.mjs`) |
| Shop: tap → auto-walk → wardrobe opens; proximity "Enter the shop" button; back to the beach in front of the door with the new outfit | step7-flow, step6-flow |
| HUD: Menu, Wardrobe, Wave — every visible button exercised | step6-flow "visible HUD buttons are exactly the tested ones" |
| First-launch "How to play" card (once) | step7-flow |
| Smooth screen transitions (fade / slide-up sheets) | visual (screenshots/video) |
| Orientation change mid-game (portrait ↔ landscape) | step7-flow "orientation change" |
| Portrait 390×844, 360×740 and landscape 844×390 layouts | step7-flow at the 3 sizes |
| iPhone safe areas (`viewport-fit=cover`, `env(safe-area-inset-*)`), checked with a simulated notch in portrait and landscape | `docs/step7/perf/notch_*.png`, `perf.json` → `safeArea` |
| No page scroll / pinch-zoom / double-tap zoom / pull-to-refresh (viewport `user-scalable=no`, `touch-action`, `overscroll-behavior`, iOS gesture events blocked) | `perf.json` → `noPageScroll`; iOS-specific gesture blocking not testable in Chrome |
| Quality toggle High / Eco (shadows, outlines, pixel ratio), persisted | step7-flow (draw calls 55 → 21) |
| `?fps=1` on-screen FPS meter | `docs/step7/perf/fps_meter_*.png` |
| Keyboard controls on desktop | step6-flow, step3-interactive |
| Production build: relative paths, code-split chunks, eval assets excluded, web app manifest | `docs/STEP7.md` |

## Not implemented (out of MVP 1 scope, by decision)
- Multiplayer, chat, other players, friends.
- Virtual currency, buying items, real shop inventory, rewards (locked items can only be tried on).
- More clothing / hair / avatars (only the existing 4 owned garments + locked demo items, one female base avatar).
- Shop interior, sitting on benches, emotes besides wave, apartments, trading.
- Sound / music.
- Offline mode (service worker); English UI (UI text is French).
- Measured performance on a real phone (needs the device: use `?fps=1`).
