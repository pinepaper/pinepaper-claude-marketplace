# PinePaper design KG — what you can author

> **Generated** from `js/ontology/Vocabulary.js` + `assets/client-llm/generators.json`
> by `scripts/build-design-kg.js`. Do not edit by hand — re-run `npm run build:design-kg`.

This is the agent-facing projection of the PinePaper ontology (the `pp:`
design knowledge graph). Use it to ground what you BUILD — pick the right
item type, relation, generator, or animation from the real vocabulary instead
of guessing. The names below are the **exact engine strings** every drive path
accepts:

- **MCP:** the listed `pinepaper_*` tool, e.g. `create_item { itemType: "star" }`,
  `add_relation { relation: "orbits" }`, `execute_generator { generator: "drawWaves" }`.
- **Console / `window.PinePaper`:** `app.create("star", {…})`, `app.animate(item, { animationType: "pulse" })`,
  `app.addRelation(fromId, toId, "orbits", {…})`, `app.executeGenerator("drawWaves")`.
- **Standalone (no live editor):** emit scene JSON / widget HTML / Paper.js using these
  same type + relation names so the result round-trips into the editor and exports.

The machine-readable peer is `dist/ontology/index.ttl` (full Turtle/JSON-LD); this
file is the distilled, context-sized view.

## Item types (22)

Create with `pinepaper_create_item { itemType: "<name>" }` (or `app.create("<name>", {…})`)
unless another tool is noted.

| itemType | creates with | what it is |
|----------|--------------|------------|
| `arc` | `pinepaper_create_item` | Arc segment — open path subset with curveType: arc |
| `areaChart` | `pinepaper_create_chart` | Area chart — filled region under a line, showing volume and trends |
| `barChart` | `pinepaper_create_chart` | Bar chart — rectangular marks on Cartesian axes for categorical comparison |
| `circle` | `pinepaper_create_item` | Circle shape (geometrically: ellipse where rx=ry) |
| `closed-path` | `pinepaper_create_item` | Closed path (region/boundary) — encloses area, no start/end distinction. Functionally equivalent to a shape defined by its boundary equation. |
| `compound-path` / `compoundpath` | `—` | Multiple sub-paths as single item (SVG imports, boolean ops) |
| `connector` | `pinepaper_connect` | Diagram connector |
| `diagram-shape` | `pinepaper_create_diagram_shape` | Flowchart/UML shape |
| `ellipse` | `pinepaper_create_item` | Ellipse shape |
| `group` | `—` | Item group/container |
| `image` / `raster` | `—` | Raster/bitmap image |
| `line` | `pinepaper_create_item` | Line segment — 2-point open path with curveType: linear |
| `lineChart` | `pinepaper_create_chart` | Line chart — connected marks showing trends over a continuous axis |
| `open-path` | `pinepaper_create_item` | Open path (trajectory/stroke) — has start and end points, does not enclose area. Defined by curveType: the mathematical function governing its segments. |
| `path` | `pinepaper_create_item` | Vector path — semantically incomplete without open/closed distinction. Defined by its curveType (mathematical function family). Refined to OpenPath or ClosedPath during graph extraction. |
| `polygon` | `pinepaper_create_item` | N-sided regular polygon |
| `rectangle` | `pinepaper_create_item` | Rectangle shape |
| `scatterPlot` | `pinepaper_create_chart` | Scatter plot — point marks encoding two quantitative variables as position |
| `star` | `pinepaper_create_item` | Star shape (geometrically: concave polygon with alternating radii) |
| `text` | `pinepaper_create_item` | Text element |
| `triangle` | `pinepaper_create_item` | Triangle shape (geometrically: 3-sided polygon) |
| `unclassified` | `—` | Item type not expressible in current vocabulary. Enables vocabulary gap discovery — count and inspect unclassified items to identify missing types. |

## Relations (66) — the behavior graph

Wire behavior between items with `pinepaper_add_relation { from, to, relation: "<name>", params }`
(or `app.addRelation(fromId, toId, "<name>", params)`). Relations are the canonical
behavior surface — prefer them over hand-rolled motion. Only relate items that exist.

| relation | direction | what it does |
|----------|-----------|--------------|
| `attached_to` | source locked to target + offset | Rigid parent-child transform — source moves with target instantly via fixed offset. Zero-lag variant of follows. Use for labels, attachments, child objects. |
| `blend_reacts_to` | source blend mode reacts to proximity of target | Source blend mode changes when target enters proximity / state. Use for collision-triggered visuals, reactive composition. |
| `blend_transition` | self-relation: source cycles blend modes over time | Source cycles through blend modes on a timed loop. Use for animated mood shifts, rhythmic visual changes. _(self-relation: omit target)_ |
| `bone_attached` | source item attached to target bone | Source canvas item rides a skeleton bone — inherits its transform. Use for character props, weapons, accessories on a rig. |
| `bone_skinned` | self-relation: source path skinned to skeleton (target=null) | Source path is skinned to a skeleton — each vertex deforms by linear blend of nearby bones' transforms. Contrast with attached_to (rigid follow) and bone_attached (inherits one bone's transform): this is per-vertex deformation enabling cloth, capes, soft-tissue. Stored as one self-edge per skinned path; the per-vertex weights live on the path's segments. _(self-relation: omit target)_ |
| `bounds_to` | source clamped within target bounds | Source's position is clamped within target's bounds. Use for keeping characters inside a frame or viewport-bounded motion. |
| `camera_animates` | viewport animates via keyframes (target=null) | Camera viewport interpolates between keyframed positions. Use for choreographed pans / zooms, fly-throughs, scripted shots. |
| `camera_follows` | viewport follows target item | Camera viewport tracks target's position with smooth pursuit. Use for cinematic follow shots, subject-lock cameras. |
| `circumscribes` | source draws bounding shape around target (note: source=drawn shape, target=bounded item) | Source's bounds scale to fully enclose target. Use for halo highlights, selection rings, labels framing content. |
| `concentric_with` | source center = target center | Source's center is held on the target's center (shared center / concentric). |
| `construction_reveal` | self-relation: source reveals at step time (target=null) | Self-relation: source fades in (opacity 0→1) starting at params.revealAt over params.fadeIn seconds, driven by the timeline (playbackTime). Used by pp:ConstructionSequence to play a construction back one step at a time. _(self-relation: omit target)_ |
| `deform_breathe` | self-relation: breathe deformation on source | Item rhythmically scales in and out. Use for living / idle motion, organic presence. _(self-relation: omit target)_ |
| `deform_bulge` | self-relation: bulge deformation on source | Item bows outward from center. Use for inflation, swelling, expansion. _(self-relation: omit target)_ |
| `deform_fold` | self-relation: fold deformation on source | Item creases along a fold line and lays flat. Use for paper-fold animations, panel reveals. _(self-relation: omit target)_ |
| `deform_inflate` | self-relation: inflate deformation on source | Item swells uniformly outward. Use for balloon, pre-pop state, growth. _(self-relation: omit target)_ |
| `deform_melt` | self-relation: melt deformation on source | Item droops downward as if liquefying. Use for dissolution, dali-esque scenes, decay. _(self-relation: omit target)_ |
| `deform_pinch` | self-relation: pinch deformation on source | Item draws toward a central point. Use for vacuum-up, focus pull, gravity well. _(self-relation: omit target)_ |
| `deform_ripple` | self-relation: ripple deformation on source | Item's surface ripples with concentric waves. Use for water disturbance, energy pulse on shape. _(self-relation: omit target)_ |
| `deform_shear` | self-relation: shear deformation on source | Item slants progressively along one axis. Use for italic-like lean, motion shear, gravity drag. _(self-relation: omit target)_ |
| `deform_squash` | self-relation: squash deformation on source | Item flattens vertically and stretches horizontally (area-preserving). Use for bounce-landing impact, weight. _(self-relation: omit target)_ |
| `deform_squeeze` | self-relation: squeeze deformation on source | Item pinches inward symmetrically. Use for cartoon expression, compression visuals. _(self-relation: omit target)_ |
| `deform_twist` | self-relation: twist deformation on source | Item rotates progressively along an axis. Use for whirlpool, candy-cane, screw motion. _(self-relation: omit target)_ |
| `deform_wave` | self-relation: wave deformation on source | Item undulates with a traveling sine wave. Use for flag-waving, fabric, liquid surface. _(self-relation: omit target)_ |
| `deform_wobble` | self-relation: wobble deformation on source | Item jiggles like jelly. Use for elastic motion, cartoon overshoot, gelatin shake. _(self-relation: omit target)_ |
| `driven_by` | source property driven by target property | Source property linearly maps from a target property. Use for parameter linking, slaved values, reactive controls. |
| `effect_blast` | self-relation: blast burst on source | Outward radial burst of particles emits once. Use for impacts, explosions, energy release. _(self-relation: omit target)_ |
| `effect_bubbles` | self-relation: rising bubbles on source | Rising bubbles emerge from the item. Use for underwater, liquid, light-hearted scenes. _(self-relation: omit target)_ |
| `effect_confetti` | self-relation: confetti burst on source | Colored streamers fall from above. Use for celebrations, accomplishments, party scenes. _(self-relation: omit target)_ |
| `effect_dust` | self-relation: ambient dust on source | Slowly drifting dust motes fill the area. Use for old / abandoned moods, sunbeam visualizations. _(self-relation: omit target)_ |
| `effect_electric` | self-relation: electric bolts on source | Crackling electric arcs jump around the item. Use for energy, danger, sci-fi power. _(self-relation: omit target)_ |
| `effect_fire` | self-relation: fire particles on source | Animated flame emerges from the item. Use for heat, burning, energy. _(self-relation: omit target)_ |
| `effect_fireflies` | self-relation: firefly particles on source | Slow-blinking glowing particles drift around the item. Use for magical night scenes, romantic ambiance. _(self-relation: omit target)_ |
| `effect_gem_smoke` | self-relation: volumetric curling smoke wreath on source | Volumetric curling smoke wreath encircles the item silhouette. Use for ornate emphasis, ritual or magical contexts. _(self-relation: omit target)_ |
| `effect_glow` | self-relation: pulsing glow aura on source | Soft luminous halo surrounds the item. Use for highlighting, importance, magical quality. _(self-relation: omit target)_ |
| `effect_heatmap` | self-relation: animated thermal halo turbulence on source | Animated thermal-color noise fills the item's silhouette. Use for temperature visualization, dramatic glow, abstract energy. _(self-relation: omit target)_ |
| `effect_liquid_metal` | self-relation: chrome-flow reflective banded shader on source | Reflective chrome-flow shader stylizes the item with banded highlights. Use for metallic logos, sci-fi aesthetic, polish. _(self-relation: omit target)_ |
| `effect_rain` | self-relation: rain particles on source | Falling raindrops cover the canvas area. Use for weather scenes, melancholy mood. _(self-relation: omit target)_ |
| `effect_ripple` | self-relation: ripple rings on source | Concentric expanding rings emanate from the item. Use for water-drop, shockwave hint, attention pulse. _(self-relation: omit target)_ |
| `effect_shockwave` | self-relation: shockwave rings on source | Single explosive ring expands outward from the item once. Use for impact moments, dramatic emphasis. _(self-relation: omit target)_ |
| `effect_smoke` | self-relation: smoke particles on source | Rising smoke trail emanates from the item. Use for damage, weight, atmosphere. _(self-relation: omit target)_ |
| `effect_snow` | self-relation: snow particles on source | Falling snowflakes drift across the canvas area. Use for winter scenes, peaceful slow motion. _(self-relation: omit target)_ |
| `effect_sparkle` | self-relation: sparkle particles on source | Twinkling particles cascade from the item. Use for celebration, magic, attention-draw moments. _(self-relation: omit target)_ |
| `effect_trail` | self-relation: motion trail on source | Particle trail follows the item as it moves. Use for motion blur, speed lines, comet tails. _(self-relation: omit target)_ |
| `expresses` | self-relation: source plays facial expression (target=null) | Source plays a named expression preset (smile, blink, surprise) driving its part_of children. Use for facial animation, character emotion. _(self-relation: omit target)_ |
| `follows` | source pursues target position | Smooth pursuit with lag — source asymptotically approaches target. Use for trailing, delay, easing motion. Contrast: attached_to is rigid (zero lag). |
| `group_morphs_to` | source group migrates into target group configuration | Source paper.Group's children migrate into target Group's children's positions, paired by index. Path.Line children deform via segment endpoints; other items translate. Excess children fade. Generic across any two groups. |
| `grows_from` | source scales from zero at target origin | Source scales up from zero starting at target's position. Use for spawn-from-point effects, ripple-into-being entrances. |
| `ik_target` | source item drives IK chain on target skeleton | Target item is the end-effector goal for an IK chain on source skeleton. Use for hand-reaches-cup, foot-lock, gaze-to-target. |
| `indicates` | source pulses to highlight target | Temporary emphasis effect. mathematically: pulseScale on source triggered by target reference. |
| `is_centroid_of` | source = centroid(target, ...params.others) | Source is held at the centroid (average position) of the target and params.others (more anchor ids). |
| `is_circumcenter_of` | source = circumcenter(target, other1, other2) | Source is held at the circumcenter of the triangle target, params.other1, params.other2 (inactive when the three are collinear). |
| `is_midpoint_of` | source = midpoint(target, params.other) | Source is held at the midpoint of the target and params.other (a second anchor). Live: drag either anchor and the source follows. |
| `lies_on_line` | source = lerp(target, params.other, t) | Source is constrained to the line through the target and params.other, at fraction params.t along it (0=target, 1=other). |
| `maintains_distance` | source held at distance from target | Source stays at a fixed distance from target as either moves. Use for tethering, leash dynamics, fixed-spacing groups. |
| `mirrors` | source reflects target across axis | Source's transform mirrors target across an axis. Use for reflections, symmetry, mirrored character poses. |
| `morphs_to` | source shape morphs into target shape | Source's path interpolates into target's path over time. Use for shape morphing, geometry transitions. |
| `moves_along_path` | self-relation: source travels along stored path (target=null) | Item position is driven along a user-supplied path stored as params. Self-relation; named easing curves (linear / easeIn / easeOut / easeInOut / sine / bounce / pingpong). _(self-relation: omit target)_ |
| `orbits` | source orbits target | Source revolves around target at a fixed radius. Use for celestial mechanics, satellites, rotating-around-X relationships. Spatial params (radius) accept canvas-relative units (e.g. '30vmin' = 30% of min(canvasW,canvasH)) so the scene adapts to any size/aspect. |
| `parallax` | source depth-shifts relative to target scroll | Source moves at a depth-scaled fraction of target's motion. Use for background layers, parallax scrolling, depth illusion. |
| `part_of` | source is named part of target — follows target position with named role metadata | Source is a named sub-element of target (e.g. eye_left part_of face). Use for compound items and named sub-element addressing. |
| `points_at` | source rotates to face target | Source rotates to always face target. Use for compass needles, gun turrets, gaze direction, arrows tracking a target. |
| `staggered_with` | source staggers after target (index-ordered) | Stored as pairwise edges with index param to reconstruct group ordering. Conceptually 1→N but decomposed into binary pairs. |
| `time_expression` | self-relation: expression on source (target=null) | Source property evaluates a math expression of time each frame. Use for custom oscillations, formula-driven motion. _(self-relation: omit target)_ |
| `unknown` | unspecified | Relation type not expressible in current vocabulary. Enables vocabulary gap discovery — count and inspect unknown relations to identify missing edge types. |
| `wave_through` | source receives wave from target (phase-ordered) | Stored as pairwise edges with index param for phase offset. Conceptually 1→N but decomposed into binary pairs. |
| `wiggle` | self-relation: noise on source (target=null) | Source position / rotation jitters via noise-driven offset. Use for hand-drawn liveliness, idle motion, organic shake. _(self-relation: omit target)_ |

## Generators (56) — backgrounds & generative content

Run with `pinepaper_execute_generator { generator: "<name>", colors, bgColor }`
(or `app.executeGenerator("<name>", {…})`). Use for backgrounds, textures, patterns,
particle fields, and math/scene art — not for foreground objects.

| generator | what it draws |
|-----------|---------------|
| `draw3DSurface` | Parametric 3D surface with perspective projection (Klein bottle, Möbius strip, torus). Use for math art, geometry visualization. |
| `drawBokeh` | Soft circular out-of-focus light dots. Use for photographic bokeh, romantic blur, ambient highlights. |
| `drawChoroplethMap` | choropleth map background generator — procedural "choropleth map" backdrop. |
| `drawCircuit` | Maze-like circuit-board pattern with nodes and traces. Use for tech backdrops, sci-fi panels. |
| `drawConcentricRings` | concentric rings background generator — procedural "concentric rings" backdrop. |
| `drawConstellation` | constellation background generator — procedural "constellation" backdrop. |
| `drawCornerAccents` | corner accents background generator — procedural "corner accents" backdrop. |
| `drawCosmosSpace` | cosmos space background generator — procedural "cosmos space" backdrop. |
| `drawCountryMap` | country map background generator — procedural "country map" backdrop. |
| `drawDayNightCycle` | day night cycle background generator — procedural "day night cycle" backdrop. |
| `drawFallingPetals` | falling petals background generator — procedural "falling petals" backdrop. |
| `drawFibonacci` | fibonacci background generator — procedural "fibonacci" backdrop. |
| `drawFireflies` | fireflies background generator — procedural "fireflies" backdrop. |
| `drawFloatingLeaves` | floating leaves background generator — procedural "floating leaves" backdrop. |
| `drawFlowCurves` | flow curves background generator — procedural "flow curves" backdrop. |
| `drawFlowField` | flow field background generator — procedural "flow field" backdrop. |
| `drawFluidFlow` | Curving streamlines suggesting fluid motion. Use for water / wind / lava illustration, organic backdrops. |
| `drawFormulaArt` | formula art background generator — procedural "formula art" backdrop. |
| `drawFunctionPlot` | Plots a math expression y=f(x) over an x-range. Use for math illustrations, function visualization, education. |
| `drawGeometricAbstract` | Randomized composition of overlapping geometric shapes. Use for abstract art, modern poster backdrops. |
| `drawGlobeWireframe` | globe wireframe background generator — procedural "globe wireframe" backdrop. |
| `drawGlowOrbs` | glow orbs background generator — procedural "glow orbs" backdrop. |
| `drawGoldenSpiral` | golden spiral background generator — procedural "golden spiral" backdrop. |
| `drawGradientMesh` | Smooth multi-color noise gradient. Use for atmospheric backdrops, mood lighting. |
| `drawGrid` | Uniform grid pattern. Use for blueprint backgrounds, technical aesthetics, layout reference. |
| `drawHalftone` | Grid of dots whose radius rides a tone field (noise / radial / linear) with color from an OKLCH palette. Use for print-halftone looks, soft textured backgrounds, retro pop art. |
| `drawKaleidoscope` | kaleidoscope background generator — procedural "kaleidoscope" backdrop. |
| `drawMetaballs` | metaballs background generator — procedural "metaballs" backdrop. |
| `drawNeonGrid` | neon grid background generator — procedural "neon grid" backdrop. |
| `drawNoiseTexture` | Static or animated noise texture. Use for grain overlays, paper textures, depth-cueing backgrounds. |
| `drawOrganicFlow` | Flowing curves that breathe and shift over time. Use for living abstract backgrounds, mood ambience. |
| `drawParametricCollection` | parametric collection background generator — procedural "parametric collection" backdrop. |
| `drawParametricCurve` | Plots a parametric (x(t), y(t)) curve over a t-range. Use for Lissajous figures, spirals, parametric art. |
| `drawPattern` | Tileable geometric pattern (chevrons, hexagons, polkadots). Use for textured backgrounds, brand backdrops. |
| `drawPerspectiveGrid` | perspective grid background generator — procedural "perspective grid" backdrop. |
| `drawPoissonDisk` | poisson disk background generator — procedural "poisson disk" backdrop. |
| `drawPoissonShapesAsync` | poisson shapes async background generator — procedural "poisson shapes async" backdrop. |
| `drawRibbons` | Smooth Bézier ribbons undulating across a shared Perlin noise field, colored from an OKLCH palette. Use for flowing aurora / silk / current backdrops, organic motion. |
| `drawShaderArt` | shader art background generator — procedural "shader art" backdrop. |
| `drawSimulation` | Renders a live ODE-based dynamic system (pendulum, Lorenz, spring-mass). Use for physics demos, chaos, science visuals. |
| `drawSolarSystem` | solar system background generator — procedural "solar system" backdrop. |
| `drawSpectrumAnalyzer` | Renders a signal's frequency spectrum via FFT. Use for audio visualizers, signal-processing demos, abstract data motion. |
| `drawStackedCircles` | Vertical stack of overlapping circles. Use for snowman-shapes, decorative beadwork, abstract sculpture forms. |
| `drawStarfield` | starfield background generator — procedural "starfield" backdrop. |
| `drawSunburst` | Radial rays emanating from a center point. Use for solar emblems, celebratory backgrounds, retro motifs. |
| `drawSunburstLines` | sunburst lines background generator — procedural "sunburst lines" backdrop. |
| `drawSunsetScene` | Layered horizon scene with sky-color gradient. Use for landscape backdrops, mood-setting backgrounds. |
| `drawTadpoles` | tadpoles background generator — procedural "tadpoles" backdrop. |
| `drawTessellation` | tessellation background generator — procedural "tessellation" backdrop. |
| `drawTruchet` | Seeded Truchet tiling of quarter-arc or diagonal tiles, colored from an OKLCH palette. Use for flowing maze textures, generative line art, circuit-like backdrops. |
| `drawUSAMap` | usa map background generator — procedural "usa map" backdrop. |
| `drawVoronoi` | voronoi background generator — procedural "voronoi" backdrop. |
| `drawWaves` | Layered horizontal sine waves. Use for water surfaces, audio waveforms, ocean backgrounds. |
| `drawWindField` | Flow-field of streaming particles driven by Perlin noise. Use for wind visualization, smoke trails, atmospheric motion. |
| `drawWorldMap` | world map background generator — procedural "world map" backdrop. |
| `drawYeganehMountains` | yeganeh mountains background generator — procedural "yeganeh mountains" backdrop. |

## Simple animations (8)

Set on creation (`create_item { animationType: "<name>" }`) or via `app.animate(item,
{ animationType: "<name>" })`. These are the always-available primitives; a larger
Animate.css-parity preset library (~100 entrances/exits/attention-seekers) is also
available by preset name.

| animationType | motion |
|---------------|--------|
| `pulse` | rhythmic scale in/out — heartbeat, breathing, “live” emphasis |
| `rotate` | continuous spin around the item center |
| `bounce` | vertical ease up-and-down |
| `fade` | opacity oscillation in/out |
| `wobble` | playful side-to-side rotation |
| `shake` | rapid horizontal jitter — error/alert |
| `swing` | pendulum rotation from the top |
| `jelly` | squash-and-stretch wobble |

