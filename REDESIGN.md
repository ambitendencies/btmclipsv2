# benclips redesign and validation

## Run

From this folder, run `python3 -m http.server 4173 --bind 127.0.0.1`, then open [the experience](http://127.0.0.1:4173/canvas-C33FF489-E385-466C-A6A3-31230FB77BEB.html). No package installation or build step is required. Browser modules and fonts require an internet connection.

## Scope and audit

Only `canvas-C33FF489-E385-466C-A6A3-31230FB77BEB.html` was changed. This document is new. The two supplied WebP backgrounds and `mockup.png` are unchanged. No dependencies were installed, no system settings changed, and no commit was made.

The original was a single browser-module application with Three.js 0.164.1, OrbitControls, RoundedBoxGeometry, RoomEnvironment, runtime Tailwind, Tone.js 14.8.49, and Google Fonts. There were no external GLTF models, package scripts, or existing tests. All models and artwork were procedural.

Preserved systems: player assembly, screen canvas, category illustrations, registry metadata and synth patterns, PCB, receiver, orange slider, keyring, raycasting, carousel, and two-stage insertion/ejection paths. The original global phase setter allowed invalid transitions; oversized framing cropped the collection, labels were disconnected from the models, and duplicated scenic geometry obscured the editorial content.

## Architecture and decisions

- HTML/CSS owns editorial content, navigation, live status, accessible controls, labels, and the reader dialog. Runtime Tailwind was replaced with scoped CSS.
- React 18.3.1 and React Three Fiber 8.17.10 use pinned import-map URLs. Fiber components own the existing player, collection, and lighting objects through `primitive`; `useFrame` owns animation and rendering. Reusing the audited model builders avoids an unnecessary full reconstruction. The existing camera is supplied to Fiber and responds to viewport changes. See [Fiber custom roots](https://r3f.docs.pmnd.rs/api/canvas#custom-canvas).
- `IMG_7237.WEBP` supplies desktop/landscape scenery. `IMG_7235.WEBP` supplies portrait layouts up to 900px. Cover sizing avoids side bands; a small portrait overscan crops the caption baked into the bottom of the mobile source.
- Duplicate desk, game cases, plant, figurine, poster, and other scenic meshes were removed. Lighting, receiver, dock, and player remain genuine 3D.
- Hollow extruded shell rims replace solid transmitting boxes, which washed out the artwork and could not refract the separate CSS photograph correctly. Physical materials, shell seams, ribs, fasteners, transmission, and environment lighting retain depth without covering the display.
- World-space label projection aligns HTML metadata with the dock. Portrait navigation and keyboard focus move the same carousel as the 3D cartridges.
- `CARTRIDGE_REGISTRY` holds category, artwork renderer/alt text, metadata, theme colors, and experience identifier. `EXPERIENCES` holds mode, title, entries, and optional declarative action configuration. Actions share renderers for memory sequences, bit switches, and visual steps.
- One screen interface displays artwork, audio progress, and field notes. An accessible reader mirrors its content and interactive state at comfortable text sizes. All eight categories have three authored entries. These are thematic editorial vignettes, not claims about the owner's biography.
- Audio is optional and loaded after user intent. Existing synth patterns and foley are retained. Track labels now identify the category and experience rather than suggesting commercial recordings are included. Progress uses the audio transport clock; pause/resume preserves position, and the touch-screen progress bar supports seeking.
- Reduced motion removes camera parallax, focus push, pulsing light, and insertion arcs, and shortens mechanical travel. Hidden documents stop rendering. The canvas permits pinch zoom; pointer cancellation returns held cartridges safely.

## Guarded interaction flow

| State | Permitted next states |
| --- | --- |
| idle | selected, aligning, dragging |
| selected | idle, selected, aligning, dragging |
| dragging | aligning, selected, active, ejecting |
| aligning | dragging, selected, active, inserting, ejecting |
| inserting | booting |
| booting | active |
| active | active, dragging, ejecting |
| ejecting | selected, aligning |

Assembly begins idle and selects cartridge 001 when ready. Tap insertion lifts and aligns before sliding into the receiver. A successful drag follows the same insertion path. Occupied-slot swaps eject the old cartridge before inserting the queued one. Mechanical phases reject additional input; outside drops and cancellation return to the dock. The orange physical slider, accessible Eject button, instruction control, and Escape key use the same ejection routine.

## Milestone reports

Evidence is saved outside the source folder. Related milestones share verified screenshots; these are grouped validation checkpoints, not eight separate implementation branches.

| Milestone | Files changed | Completed and tested | Performance / limitation | Next step | Desktop / mobile evidence |
| --- | --- | --- | --- | --- | --- |
| 1. Audit | None | Inspected assets, modules, procedural models, materials, audio, UI and animation; rendered both baselines | Cropped collection, text/model overlap, per-frame screen uploads; no benchmark yet | Establish layered scene | [Desktop](/Users/ben/.codex/visualizations/2026/11/01/01a19953-072f-7770-a3f0-fd916435d6d3/benclips/before-desktop.png) / [Mobile](/Users/ben/.codex/visualizations/2026/11/01/01a19953-072f-7770-a3f0-fd916435d6d3/benclips/before-mobile.png) |
| 2. Scene/layout | HTML | Fiber integration, supplied backgrounds, responsive framing and projected labels; resize regression identified and fixed | Removed duplicate scenic geometry; CDN modules still required | Refine reference composition | [Desktop][checkpoint-desktop] / [Mobile][checkpoint-mobile] |
| 3. Visual match | HTML | Editorial hierarchy, warm palette, horizontal dock, reference navigation and image treatment; desktop/mobile inspection | Existing artwork is intentionally more graphic than the reference | Refine materials | [Desktop][checkpoint-desktop] / [Mobile][checkpoint-mobile] |
| 4. Geometry/materials | HTML | Hollow transmitting rims, seam lines, PCB, screw slots, thinner tray, metallic keyring | Reduced shell subdivision; photograph cannot receive real 3D reflections | Guard mechanics | [Desktop][checkpoint-desktop] / [Mobile][checkpoint-mobile] |
| 5. State machine | HTML | Guards, alignment, insertion, boot, ejection, swap queue, cancelled/outside drops; rapid-input test | Rejects overlapping mechanical operations; native-device feel remains to be reviewed | Complete Nostalgia | [Desktop][checkpoint-desktop] / [Mobile][checkpoint-mobile] |
| 6. End-to-end experience | HTML | Nostalgia load, boot, three notes, pagination, optional synth audio, pause/resume, eject | Audio loads on demand; no commercial recordings or user-supplied personal media | Expand shared registry | [Desktop][nostalgia-desktop] / [Mobile][nostalgia-mobile] |
| 7. All categories | HTML | All eight loaded and read; Gaming code solved; Technology produced binary 1010 = decimal 10; growth, palette and sky controls responded | Shared UI/action renderers; authored thematic content | Responsive/performance polish | [Desktop][collection-desktop] / [Mobile][collection-mobile] |
| 8. Optimization/QA | HTML, REDESIGN.md | Six viewports, touch at DPR 2, keyboard, focus trap/restoration, reduced motion, sound and transport checks | 563 → 265 draw calls/frame; physical low-end phones and Safari/Firefox untested | Real-device review and owner-specific content | [Desktop][final-desktop] / [Mobile][final-mobile] |

## Validation results

Environment: local HTTP server, Chromium through Playwright. Browser plugin was not available; the supplied Playwright tools were used. No browser dependencies were installed. JavaScript module syntax also passed `node --check` after extracting the module to a temporary file.

| Check | Result / evidence |
| --- | --- |
| Page identity | Correct filename route and `benclips™ — A Little Piece of Everything` title |
| Nonblank page | Player rendered and eight real cartridge controls present |
| Framework overlay | None |
| Console | No current errors or warnings, including after sound activation |
| Screenshot/reference comparison | Native 1536×1024 reference size and 1440×900 desktop; both supplied backgrounds and final screenshots visually inspected |
| Responsive layout | 1536×1024, 1440×900, 768×1024, 390×844, 320×568, 844×390; no horizontal document overflow |
| Basic insertion | Tap → aligning → inserting → booting → active |
| Physical control | Clicking/tapping orange 3D slider → ejection → selected |
| Dragging | Dock → outside drop returns; dock → aligned receiver loads; occupied receiver swaps correctly |
| Cancellation | Simulated pointer cancellation returns cartridge to dock and restores controls |
| Native touch emulation | Separate mobile context, DPR 2: native tap loaded; physical slider tap ejected; dispatched touch drag inserted successfully |
| Rapid input | Twenty repeated cartridge/eject attempts during insertion left exactly Nostalgia loaded |
| Eight experiences | Every category loaded, opened, and displayed content; configured interactions exercised |
| Keyboard | Arrow keys select/center, Enter loads, Escape closes/ejects |
| Dialog | Shift-Tab wraps within reader; Escape restores Field notes focus; page behind dialog is inert |
| Reduced motion | Media emulation enabled; keyboard-loaded Life reached active successfully |
| Audio | Sound opt-in, playing, pause, resume and mute verified; transport seconds resumed from paused position |

### Performance observations

Measured on Chromium using ANGLE Metal on an Apple M1 Max at 1440×900, DPR 1. Instrumentation counted native WebGL draw calls over 1.5-second steady-state samples. Repeated PCB components and cartridge contact pads now use instancing, cartridge faces with the same material are batched, and paired shell seams share draw calls.

- Before batching: approximately 563 draw calls per frame.
- After batching: approximately 265 draw calls per frame, a 53% reduction.
- Both samples followed roughly 165 animation callbacks per second on this host; this is hardware-specific and is not a mobile frame-rate guarantee.
- DPR is capped at 1.5. At emulated device DPR 2 and 390×844, the observed drawing buffer was 585×1266.
- Shadow map is 1024². The screen texture refreshes at 10 Hz during audio playback, instead of every rendered frame; boot and explicit interactions update immediately.
- The supplied background files total about 146 KB; no new raster assets were added. The HTML is about 120 KB. Cold-network/CDN performance was not benchmarked.

## Reference fidelity ledger

| Comparison | Reference / rendered evidence | Decision or repair |
| --- | --- | --- |
| Composition | Left editorial block, center-right collectible, horizontal eight-cartridge dock | Kept all; repaired initial cropping and label drift |
| Typography | Large editorial serif, small tracked navigation, compact captions | Matched hierarchy and line breaks; condensed small-screen layout avoids overlap |
| Palette | Warm studio, ivory text, orange physical control | Supplied photography and warm lighting replace procedural scenic approximations |
| Player | Clear shell, exposed board, black screen, metal ring | Preserved real meshes; replaced washed-out solid glass geometry with hollow rims |
| Artwork | Detailed pixel/painted miniature scenes in mockup | Intentionally retained the original procedural illustrations; simpler and more graphic than the mockup |
| Dock | Brushed collectible tray and individual labels | Eight visible on desktop; navigable horizontal collection on portrait devices |
| Mobile | No full mobile composition was supplied | Adapted the hierarchy around the supplied portrait background, with short-height and landscape variants |
| Copy | Brand, headline, subtitle, navigation and physical instructions | Preserved. Added accessible player controls, live status, and authored archive content as functional necessities |
| Sound/track preview | Reference shows sound on and a named commercial song | Defaults to opt-in sound; truthful synth-study labels replace unavailable recordings |
| Reader | Reference's small display cannot provide comfortable long-form text | Accessible dialog mirrors the same content and state shown on the actual 3D screen |

The final result was compared against the supplied concept, including copy, layout, typography, palette, asset treatment, spacing, icons, and responsive behavior. It follows the reference's composition and interaction language; it is not a pixel-identical reproduction. The intentional deviations above remain.

## Remaining limitations and recommended next steps

1. Review on physical iOS/Android devices and Safari/Firefox, especially touch feel, thermal load, WebGL transmission and synthesized audio. Browser emulation does not replace hardware testing.
2. Replace thematic vignettes with owner-authored memories and licensed/personal media where desired; the declarative registry and screen modes provide the integration points.
3. If exact miniature artwork fidelity is required, commission/export eight matching artwork assets. Existing procedural illustrations remain intentional, functional artwork rather than unavailable-media placeholders.
4. For offline distribution or deployment with a strict content security policy, vendor the pinned modules/fonts. This revision deliberately keeps the original no-build workflow and adds no package installation.

[checkpoint-desktop]: /Users/ben/.codex/visualizations/2026/11/01/01a19953-072f-7770-a3f0-fd916435d6d3/benclips/milestone-desktop.png
[checkpoint-mobile]: /Users/ben/.codex/visualizations/2026/11/01/01a19953-072f-7770-a3f0-fd916435d6d3/benclips/milestone-mobile.png
[nostalgia-desktop]: /Users/ben/.codex/visualizations/2026/11/01/01a19953-072f-7770-a3f0-fd916435d6d3/benclips/nostalgia-desktop.png
[nostalgia-mobile]: /Users/ben/.codex/visualizations/2026/11/01/01a19953-072f-7770-a3f0-fd916435d6d3/benclips/nostalgia-mobile.png
[collection-desktop]: /Users/ben/.codex/visualizations/2026/11/01/01a19953-072f-7770-a3f0-fd916435d6d3/benclips/collection-desktop.png
[collection-mobile]: /Users/ben/.codex/visualizations/2026/11/01/01a19953-072f-7770-a3f0-fd916435d6d3/benclips/collection-mobile.png
[final-desktop]: /Users/ben/.codex/visualizations/2026/11/01/01a19953-072f-7770-a3f0-fd916435d6d3/benclips/final-desktop.png
[final-mobile]: /Users/ben/.codex/visualizations/2026/11/01/01a19953-072f-7770-a3f0-fd916435d6d3/benclips/final-mobile.png

## Follow-up: native window sizing and optical materials

The reported brown margins came from the QA browser's persistent 1440×900 emulated viewport inside a larger native window. A new preview context with `viewport: null` follows the real window. Native resizing to 1800×1100 and 1250×900 produced matching background/canvas/content dimensions of 1800×1013 and 1250×813 respectively. No artificial fixed-size compensation was added to the application.

Plastic now uses thinner beveled rims, increased physical transmission, thin-film iridescence and restrained spectral edge glints. A rounded-distance shader replaces the thick selection border with a narrow white core and colored falloff. An additive reflection on the tray adds local light spill. Existing insertion/ejection passed after these changes; mobile tap loading also passed without page errors. The earlier draw-call benchmark predates the added glint and spill passes.

## Follow-up: internal cartridge light and assembly angles

Selection illumination is now a cartridge-local surface inside the shell, clipped to a narrow rounded rim. Its depth places it behind the opaque connector and gold contacts. It follows the cartridge through dragging and insertion. Dock pitch increased from 20 to 22 world units and docked scale decreased from .76 to .68; the tray and framing were adjusted to retain all eight cartridges on desktop. Subtle player and dock tilts reveal shell depth. Receiver staging/seating positions now derive from the player's world transform, and cartridge rotation/scale interpolate into that same transform. Screw heads have concentric machining texture, anisotropic metallic shading and polished bevels.

Validated desktop load, occupied-slot swap, eject and mobile tap loading; no page errors. Desktop and mobile screenshots were inspected for spacing, ring framing, inner illumination and insertion orientation. No full new performance benchmark was taken for these additional material details.
