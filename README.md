# Fashion Beach — MVP 1

**Play now:** [https://sasaif.github.io/fashion-beach/](https://sasaif.github.io/fashion-beach/)

Add `?fps=1` to the URL to show an on-screen FPS meter (useful on iPhone).

This public repository hosts the verified MVP 1 build (git tag `mvp-1`, commit `81d25cc`). GitHub Pages serves the production static files from `release/fashion-beach-mvp-dist.zip`. The complete source is in `packaged/fashion-beach-mvp-source.zip.b64` (see `packaged/README.md` to unpack). No game code was changed for this deploy: the Vite build already uses relative paths (`base: './'`).

---

A cute, mobile-first 3D fashion game (inspired by YoWorld): customize your avatar's face, put together an outfit, and stroll around a small beach with a virtual joystick. Single player, offline, no account. Progress is saved in the browser.

## Run locally
Requirements: Node.js 20+ and npm.
```bash
cd app
npm ci                 # install exact dependency versions (≈350 MB node_modules)
npm run dev            # dev server with hot reload → http://localhost:5173
npm run build          # production build → app/dist (≈9 MB, of which the avatar .vrm is 6.4 MB)
npm run preview        # serves app/dist on http://localhost:4173 and on your LAN
node scripts/serve-dist.mjs 8080 dist   # same, with gzip like a real host (≈4 MB transferred)
```
**Play on a phone from your PC:** run `npm run preview` (or the gzip server) and open `http://<your-PC-LAN-IP>:4173` on the phone (same Wi-Fi). The build needs an HTTP server: double-clicking `index.html` (file://) does not work because browsers block ES modules and model loading from file://.

The build is static with relative paths (`base: './'`, hash router `#/menu`, `#/play`…), so `dist/` works from any folder or sub-path.

## Deploy (free static hosting)
The game is just the contents of `app/dist/` (or `release/fashion-beach-mvp-dist.zip`). Any of these works:
- **Netlify Drop**: open https://app.netlify.com/drop, sign in (free), drag the `dist` folder → you get a `https://<name>.netlify.app` URL. (Anonymous drops are temporary unless you sign in and claim the site.)
- **Vercel**: `npm i -g vercel` then, in `app/dist`, run `vercel deploy --prod` (free Hobby account). Or import the repo with Root Directory `app`, Build Command `npm run build`, Output Directory `dist`.
- **GitHub Pages**: push the repo, then either (a) commit the contents of `app/dist` to a `gh-pages` branch and enable Pages on that branch, or (b) add a workflow that runs `npm ci && npm run build` in `app/` and publishes `app/dist` with `actions/upload-pages-artifact` + `actions/deploy-pages`. Relative paths mean the `/<repo>/` sub-path works without config.
- **itch.io** (HTML5 game): zip the contents of `dist/` (index.html at the root of the zip), upload it as "HTML", check "This file will be played in the browser" and "Mobile friendly".
Tip: most hosts gzip/brotli automatically (the avatar goes 6.4 → 3.0 MB, JS 1.4 → 0.39 MB).

## URL flags (testing only; nothing test-related is visible without them)
`?fps=1` on-screen FPS / draw calls / triangles meter (use it to measure on an iPhone) · `?quality=low|high` force the quality · `?e2e` exposes `window.__game` for the automated tests · `?test` deterministic test scene · `?scene=test&testpanel` legacy dev panel · `?noui` · `?nosave`.

## Controls
| Where | Gesture | Action |
|---|---|---|
| Beach, left half | drag your thumb | analog joystick: small push = walk, full push = run (relative to the camera) |
| Beach, right half | drag | rotate the camera (free yaw, clamped pitch) |
| Beach | two-finger pinch / mouse wheel | zoom |
| Beach | tap the shop | the avatar walks to the door, then the shop (wardrobe) opens; near the door an "Enter the shop" button appears |
| Beach HUD | 🏠 Menu · 👗 Wardrobe · 👋 Wave | |
| Avatar screens | drag on the avatar | turn her around |
| Desktop keyboard | WASD / ZQSD / arrows, Shift = run, E = wave | |
The first time you open the beach, a one-time "How to play" card appears. Main menu: **✨ High quality / 🍃 Eco mode** toggle (eco = no real-time shadows, no outlines, pixel ratio 1; picked automatically when the browser reports ≤ 3 GB RAM).
The UI text is in French.

## Flow
Main menu → Face (noses, mouths) → Wardrobe (tops, jackets, bottoms, shoes) → Inventory (owned / locked; locked items can only be tried on) → Play (beach); from the beach, Wardrobe/shop and back to the same spot. Everything is auto-saved in `localStorage` (`fashionbeach.save`, versioned and repaired on load) — outfit, face, inventory — plus quality and "tutorial seen"; reload restores it all, on the same screen.

## Architecture (`app/src`)
| Folder | Role |
|---|---|
| `avatar/` | VRM loading, skeleton contract (`skeleton.ts`), `PlayerAvatar` (per-frame avatar loop), clothing (`clothing/ClothingManager`: rebind by bone name, body-zone hiding), face (`face/FaceCompositor`: nose/mouth layers composited onto the skin texture) |
| `animation/` | retargeted Quaternius clips, idle/walk/run/wave state machine, stride matching (playback rate = ground speed → no foot sliding), procedural wave and turn lean |
| `movement/` | `PlayerController` (acceleration, turning, collisions), unified input (`moveIntent`: keyboard / joystick), `playState` (orbit camera, auto-walk) |
| `camera/` | `FollowCamera`: showcase camera for the menu screens + third-person beach camera that avoids walls |
| `world/` | 100 % procedural beach (`beach/`: geometry, props, layout, water/sky shaders), 2D collision world (`collision.ts`) |
| `game/` | inventory, wardrobe (draft / preview / saved), versioned save |
| `catalog/` | item data (`data/clothing.json`, `data/face.json`) |
| `ui/` | hash router (with a "hub": menu or beach), React screens, beach HUD + touch controls, toasts, FPS meter |
| `core/` | shared runtime, tiny store, quality setting, loading progress |
Stack: React 19, @react-three/fiber 9, three 0.187, @pixiv/three-vrm 3.5 (MToon toon shading), Vite 8. The loading screen lives in `index.html` (shows before any JS) with a progress bar driven by the bytes of the avatar/animation files. JS is split into chunks: three, vrm, r3f, react, app.

## Skeleton & clothing rules (for every new item)
- **The skeleton is a contract**: VRM 1.0 humanoid of `avatar_base_f01.vrm` (VRoid `J_Bip_*` bones). Never rename, delete or re-orient a bone or change the rest pose; `validateSkeleton` checks it at load.
- **One GLB per garment**, skinned only to existing bones (≤ 4 influences per vertex), exported in the base rest pose; rebound by **bone name** at runtime, so it follows every animation.
- **Layers**: base 0 < bottoms/shoes 1 < tops 2 < jackets 3. Each item declares `hidesBodyZones` (chest, belly, hips, thighs, calves, ankles, feet, upperArms, forearms); those zones of the body and of lower layers are hidden, so no skin pokes through.
- Toon MToon materials (same shading as the avatar), world-space outline; thumbnails generated by `scripts/make-thumbs.mjs`.
- Face items = RGBA PNG layer placed on a UV anchor (`faceAnchors.ts`); tied to the base avatar's UV layout.
- Licenses: `docs/LICENSE_AUDIT.md`.

## Tests (headless Chrome, real CDP touch events)
- `node app/scripts/step7-flow.mjs 390x844|844x390|360x740` (production build served by `npm run preview`): full end-to-end flow.
- `node app/scripts/step7-perf.mjs` (gzip server on :4180): throttled load time, headless FPS, draw calls, simulated iPhone notch.
- `node app/scripts/step7-gameplay.mjs`: ~60 s touch-driven gameplay video.
- Regressions: `BASE='http://localhost:4173/?e2e' node app/scripts/step5-flow.mjs` and `step6-flow.mjs`.
Reports: `docs/STEP3.md` … `docs/STEP7.md`, feature list `docs/FEATURES.md`.

## Known limits
- Single player, offline; no multiplayer, chat, currency or purchases (out of MVP 1 scope). Locked items can only be tried on.
- One base avatar (female); 4 owned garments + a few locked demo items; limited face options.
- The shop has no interior (its door opens the wardrobe screen); 2D collisions (no jumping); benches are decorative.
- Only tested in headless Chrome with a software GPU: **real FPS must be measured on a phone** (`?fps=1`). Avatar is 6.4 MB (3 MB gzipped): about 6 s to playable on 4G, ~30 s on Fast 3G (measured with throttling).
- No sound. UI in French only.

## Suggested next steps
1. Test on real iPhones/Android phones with `?fps=1`; auto-switch to eco mode on low FPS.
2. Compress the avatar (WebP/KTX2 textures, meshopt) to get under 3 MB.
3. More clothes/hair, then currency + real shop, rewards.
4. Shop interior, sitting on benches, emotes.
5. Multiplayer beach room, then chat.
6. Ambient sound, offline PWA (service worker).
