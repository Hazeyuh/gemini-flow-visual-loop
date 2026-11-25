# Prompt Templates & Multi-Round Case Studies

This reference provides reusable 500+ word prompt structures for each stage of `Visual-Loop` alongside condensed R1→R4 defect-to-fix logs across architectural photography, deep-sea surrealism, traditional craft media, game IP synthesis, and motorsport photography.

---

## 1. Stage-by-Stage Prompt Templates

### 1.1 `Pure-Lens` Round 1 (`Scout`) — 500+ Word Physical Photography Prompt

Use when base optical and physical priors are sufficient without IP-specific reference sheets. Constrain **medium/camera, primary geometry & scale crop, foreground/midground/background planes, microscopic scale cue, Kelvin lighting & optical transport, disciplined palette, and explicit exclusions**:

```text
Compose an eerie, gallery-grade 3:4 medium-format architectural fine-art photograph capturing post-human brutalist scale and quiet northern highland desolation.

Dominating the middle and upper horizon above a bleak, mist-drenched mountain moorland is a kilometer-wide windowless horizontal concrete megastructure supported hundreds of meters above the valley floor by widely spaced cast-in-place béton brut pylons. Construct the colossal span from austere repeating geometric bays, deep pitch-black rectangular intake voids, heavy overhanging concrete baffles, and raw formwork panel grids weathered by decades of vertical rainwater channels and pale mineral efflorescence streaks. Let thick ribbons of cold pearl-gray cloud drift directly around and through the hollow structural bays beneath the span, obscuring both lateral flanks so the edifice extends continuously past the left and right edges of the frame with no finite termination.

In the lower foreground, run a cracked, deserted stone-and-asphalt causeway lined with sparse damp olive moorland grass straight from the bottom center toward the distant valley floor under the megastructure's shadow, devoid of vehicles, overhead wires, or human figures. Place a single slightly tilted weathered concrete milepost beside the road in the lower left foreground as a quiet terrestrial scale anchor against the towering concrete span looming in the mist.

Shoot on a 50mm Hasselblad medium-format film camera at f/8 under flat, featureless 7000K winter overcast light: enforce dead-straight architectural verticals, soft shadowless documentary light transport, fine organic Kodak Portra 160 film grain, natural atmospheric haze lightening distant pylons into ghostly slate silhouettes, and tack-sharp micro-detail across wet foreground paving cracks and streaked concrete seams. Restrict the color palette strictly to foggy pearl-white, cold cement gray, muted olive-charcoal moorland, wet asphalt slate, and deep intake void black. Strictly exclude flying vehicles, glowing sci-fi windows, neon strips, sunset orange skies, people, birds, glossy 3D CGI sheen, painterly brushstrokes, text, or watermarks.
```

### 1.2 `Ref-Forge` Round 1 (`Scout`) — Multi-Reference Synthesis & HUD Stripping

When fusing 2–3 staged anchor files from `~/flow-mcp-out/<slug>_refs/`, assign each file's exact role in the opening paragraph, strip source HUDs/text, and lock `full-bleed` if using a flat craft medium:

```text
Create a full-bleed, edge-to-edge 16:9 1970s European fine-art silkscreen serigraph print inspired by Moebius (Jean Giraud) and Ligne Claire architectural illustration—filling 100% of the rectangular canvas with zero white paper margins, zero museum wall, zero picture frame, and zero pedestal. Synthesize the towering saffron-gold stepped ziggurat terrace, terracotta-striped Moorish arches, and mint-turquoise sky palette from ref_01.jpg with the exact faceless crimson hooded traveler silhouette and crescent-crowned turquoise robe proportions from ref_02.jpg, while stripping away all in-game UI icons, dialogue bubbles, cursor prompts, and screen borders.

[Paragraph 2: Macro architectural composition, asymmetric elevation crop, perspective lines, and spatial depth planes...]

[Paragraph 3: Exact character silhouettes, relative scale against stone balustrades, and authentic geometric stele glyphs...]

[Paragraph 4: Physical printmaking medium—heavyweight cotton rag paper tooth, subtle halftone dot gradients across sky and desert haze, crisp dark-crimson keyline ink registration, directional magenta architectural cast shadows aligned to a single high sun angle, and strict exclusions...]
```

### 1.3 Round 2 (`Sculpt`) & Round 3 (`Forensic-Check`) — Surgical Delta Prompting

When iterating on a winning previous-round image (`asset:<title>` or local `scene-NN-vX.jpeg`), **state what stays locked, state the structural/optical replacement positively, and explicitly clear every secondary trace of deleted elements**:

```text
Evolve the flooded subterranean concrete pillar hall from the reference image into a vastly larger, asymmetrical post-human architectural documentary photograph while preserving its raw weathered board-formed concrete aggregate texture, vertical mineral seepage stains, and mirror-still black flooded floor.

1. Scale & Asymmetric Framing: Multiply interior column height fivefold so the twenty-meter-thick chamfered piers ascend over two hundred meters until the coffered ceiling dissolves completely into cold slate-gray mist, shifting the camera to an austere off-center diagonal axis across five receding tiers of piers and high cantilevered concrete sky-bridges.
2. Ghost-Free Object Removal: Completely remove the central hooded figure, its dark reflection on the water, and all symmetrical hanging overhead cables—leaving clean, undisturbed mirror-black water across the central axis with zero residual dark patches or wake lines.
3. Physical Light & Surface Contact: Replace the theatrical central overhead spotlight with flat, diffused 6800K overcast daylight leaking softly from an unseen lateral chasm far to the left, casting vertically aligned pillar reflections across the black water; add a two-meter-high dark wet capillary water-stain band around the base of every concrete pier where stone meets water, plus one microscopic rusted maintenance ladder bolted to a distant pier.

Maintain a 28mm large-format lens at f/8 with plumb vertical lines, organic 65mm analog film grain, uncrushed shadow detail, zero human figures, zero text, and zero UI.
```

### 1.4 Round 4 (`Polish`) — Locked-Geometry 2K Exhibition Pass

Once Round 3 passes all five `Forensic-Check` criteria, lock the geometry completely and refine only micro-surface textures, optical transitions, and grain alongside `download_quality: "upscaled"`:

```text
Museum-grade gallery exhibition polish of the reference 70mm IMAX twilight landscape, strictly preserving its exact locked composition: the monumental stepped basalt portal monolith with glowing geometric glyphs, microscopic traveler silhouette at the stair base, distant obsidian obelisks, tilted ringed gas giant, upper moon, and tiny distant three-ship wedge.

Apply one targeted astronomical optical refinement: soften the shadowed left hemisphere of the ringed gas giant so twilight atmospheric Rayleigh scattering blends its night side naturally into the dusk-violet sky rather than rendering darker than the surrounding atmosphere, keeping the sunlit right crescent and icy rings crisp. Elevate micro-surface fidelity everywhere else: sharpen ancient chiseled basalt pores, carved glyph edges, and wet turquoise-silver specular highlights across the terraced stone steps; refine mirror-calm shallow tidal reflections and concentric micro-ripples around submerged volcanic rocks with authentic 70mm photochemical film grain and smooth highlight roll-off. Zero new objects, zero composition shifts, zero text, zero UI.
```

---

## 2. Real Multi-Round Defect-to-Fix Case Studies

| Genre & Medium | Round 1 (`Scout`) Defect | Round 2 (`Sculpt`) Structural Fix & Residual Issue | Round 3 (`Forensic-Check`) Optical/Lore Fix | Round 4 (`Polish` 2K) Final Pass |
| --- | --- | --- | --- | --- |
| **Brutalist Megastructure** (`9:16` 65mm film) | Flat horizontal roofline capped the wall at ~60 m dam height; oversized service arch & railings shrank scale | Rebuilt into multi-tiered kilometer-tall stepped monoliths vanishing into upper mist; twin doors & bollard ramp still felt busy | Replaced twin doors and railings with a single wet concrete apron leading into a dark vertical chasm + one microscopic rusted post | Sharpened wet aggregate seams, mineral efflorescence, and distant amber pinpoint lamp (**9.6/10**) |
| **Deep-Sea Megalophobia** (`3:4` painterly cinema) | `v1` centered disc looked like a small UFO; `v2` asymmetric dome worked, but a central bubble column clipped straight across depth planes and a stone lantern looked out of place | Locked `v2` asymmetric overhang, deleted clipping bubbles and lantern, added tiny diver with upward flashlight; beam lacked a ceiling hit-spot and pebbles looked arranged | Tilted diver into weightless upward hover, projected a soft circular beam spot onto the overhead basalt dome, scattered natural dark reef rock | Resolved ancient concentric basalt relief inside the lit ceiling spot + fine sand stirred at diver's boots + Tyndall beam dust (**9.9/10**) |
| **Lacquer & Kintsugi Screen** (*Outer Wilds*, `3:4`) | Accurate hollow planet shell + black hole, but rendered as a 3D folding screen standing on a museum floor under spotlights | Cropped to 100% `full-bleed` flat black urushi lacquer panel using in-game reference; Hanging City arches drooped like melted stalactites | Rebuilt multi-tier sandstone arches with warm lanterns, faceted purple gravity crystals, blue-violet gravitational lensing ring, and volcanic lava arcs | Deepened glossy black urushi depth, raised 24K kintsugi gold relief, and refined tiny astronaut jet plume (**9.9/10**) |
| **Medieval Tapestry + Risograph** (*Outer Wilds*, `16:9`) | Real-world gravity overrode lore: the zero-G sand column poured downward out the bottom of the frame like a waterfall | Cleared downward waterfall, restored indigo starfield below, routed sand horizontally between the twin planets; column looked stiff like a wooden beam | Softened column into swirling woven-gold sand eddies, moved tiny striped wooden ship into clean upper silhouette, unified tapestry border | Cleaned stray sand wisps under Ember Twin and restored geometric High Energy Lab dome inside the canyon (**9.9/10**) |
| **1970s Sci-Fi Matte Painting** (*No Man's Sky*, `16:9`) | Flat wall-hugging camera lacked kilometer-deep canyon parallax; starship contrail split into an unnatural two-way fork | Shifted to dynamic 3/4 cantilevered industrial canyon with needle-nosed fighter; R1 left a looping blue "inpainting ghost" contrail + repeated white circle decals | Explicitly purged the looping ghost contrail leaving one clean pair of pale-blue trails, stripped repeated decals, organized lower-right trusses into twin gantries | 2K upscaled John Berkey / Chris Foss brushwork, riveted hull seams, solar rim highlights, and cyan docking haze (**9.9/10**) |
| **Motorsport Night Rain** (*GT3*, `16:9` Pure-Lens) | Established Nürburgring spray and crowd mood, but brake thermal glow and wet tarmac reflections lacked physical crispness | Strengthened diagonal rain streaks, grandstand camera flashes, and red tail-light mirror trails | Ignited orange-red glowing brake discs and three-color wet asphalt reflections (yellow headlights, red taillights, green curbing) | Tightened exposure contrast, rain droplet micro-grain, and rear diffuser roostertail spray *(note: full-ring brake glow through spokes accepted as a minor model prior)* |
