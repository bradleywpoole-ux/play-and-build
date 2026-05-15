# Play & Build — Design Doc Update (Session 3)

## 🔄 ARCHITECTURE PIVOT — Outfits over Layered Tops/Bottoms

### What changed
After attempting to ship the layered overlay system (Top + Bottom + Accessories as separate layers on Lily), we discovered that ChatGPT cannot reliably generate clothing items shaped to fit a specific character's body — especially sleeve length and shoulder width to match Lily's chibi proportions. Multiple regenerations of the pink hoodie produced garments that looked fine in isolation but never aligned with Lily's bare arms or fit her body naturally.

### The new approach: Full-body outfits + layered accessories
- **Outfit** is a single full-body PNG covering Lily from neck to feet (includes top, bottom, and shoes baked in). Only Lily's head and hands remain visible.
- **Accessories** (hat, glasses, jewelry, bag) remain as separate small overlays layered on top of the outfit.
- **Pet** is positioned beside Lily, not on her.

### Why this is better
- ChatGPT can produce coherent full-body chibi outfits much more reliably than it can produce body-part-specific overlay pieces.
- Each outfit looks "finished" because the garments are drawn as a coordinated set, not assembled from mismatched parts.
- Lydia still gets meaningful customization — she swaps between 10 curated outfits and adds accessories on top.
- Fewer assets total: ~10 outfits + ~5 of each accessory type ≈ 30 assets for a full wardrobe, versus dozens of tops/bottoms.
- No more arm-coverage / sleeve-length problems. The outfit covers the arms by design.

### Trade-offs accepted
- **Less mix-and-match flexibility.** Lydia can't pair "the pink hoodie" with "the blue jeans" — outfits are pre-coordinated.
- **The Lily base must match each outfit's silhouette.** If outfits are drawn for a Lily-shaped body, they only work on this Lily.
- **Each outfit needs precise alignment to Lily's body.** Expect 2-4 ChatGPT regens per outfit to nail the fit.

### Quality bar
Brad's standing rule for this project: **don't ship "kind of looks real."** Outfits must look like clothes Lily is genuinely wearing, not stickers floating on her body. Every outfit gets eyeballed on iPad before we keep it; failed generations get redone, not patched.

---

## 🎨 Updated category structure (Lily mode)

| Category | Status | Approach |
|---|---|---|
| 💇 Hair Style | ✅ WORKING | Existing — Long/Bob/Ponytail (brown) |
| 👗 **Outfit** | **NEXT** | Full-body overlay, neck-to-feet, replaces Top + Bottom |
| 🎀 Hat | Phase 4 | Small overlay on head |
| 👓 Glasses | Phase 4 | Small overlay on face |
| 💎 Jewelry | Phase 4 | Small overlay at neck |
| 🎒 Bag | Phase 4 | Overlay at side of body |
| 🐾 Pet | Phase 4 | Positioned beside Lily |

**Removed from previous design:**
- ❌ Top (replaced by Outfit)
- ❌ Bottom (replaced by Outfit)
- ❌ Accessories (split into Hat / Glasses / Jewelry / Bag)
- ❌ Hair Color (deferred to Phase 5)
- ❌ Eyes (deferred to Phase 5 or later)

---

## 👗 The 10-outfit wardrobe (locked plan)

1. **Casual day** — hoodie + jeans + sneakers
2. **Sundress** — floral/pastel dress + sandals
3. **Pajamas** — matching PJ top + pants + slippers (optional sleep mask)
4. **Sporty** — athletic top + leggings + sneakers (optional sweatband)
5. **Party dress** — sparkly/glittery dress + fancy shoes
6. **Beach day** — one-piece swimsuit + flip-flops
7. **Winter** — long coat + boots + scarf woven in
8. **Rainy day** — raincoat + rain boots
9. **School uniform** — plaid skirt + collared shirt + cardigan + Mary Janes
10. **Costume / fantasy** — TBD by Lydia (princess gown, superhero, magical girl, etc.)

Each outfit is one PNG, named `outfit_<name>.png` (e.g. `outfit_casual.png`, `outfit_sundress.png`).

---

## 🛠️ Implementation conventions for outfits

### PNG canvas requirements (must be exact)
- Dimensions: **1000 × 1500 pixels** (portrait, same as Lily base)
- Background: **fully transparent** (no white, no checkered, no color)
- The outfit must be drawn in the **same position and proportions** as Lily's body occupies in her PNG. Shoulder line, waist, knees, and ankles must align pixel-to-pixel with Lily.
- Bottom of the outfit (shoes) must reach the same Y-coordinate as the bottom of Lily's shoes in her base PNG.
- Style: chibi anime, bold black outlines, flat colors with soft cel-shading — matching Lily's existing art style.

### CSS approach
- The outfit overlay is a single `<img>` absolutely positioned over Lily, full width, full height of the character container.
- `object-fit: contain` ensures it scales without distortion.
- Because the outfit canvas matches Lily's canvas, no per-outfit positioning is required — the same CSS rule works for all outfits.

### Accessory CSS approach (Phase 4)
- Each accessory category (Hat, Glasses, Jewelry, Bag) gets its own CSS rule for positioning.
- Within a category, all items share the same CSS rule and must be drawn at consistent positions on the canvas.

### Z-order (bottom to top)
1. Lily base (with hair)
2. Outfit
3. Bag
4. Jewelry
5. Glasses
6. Hat

Pet is positioned beside Lily and is not part of this stack.

---

## ✂️ What gets deleted in the Session 3 refactor

- `top_hoodie_pink.png` and any other `top_*.png` files
- The `tops` array in `index.html`
- The `topOverlay` `<img>` element and its CSS
- The `.lily-overlay` CSS rules (will be replaced by per-category overlay rules)
- The categories `top`, `bottom`, `accessories`, `eyes`, `hairColor` from the Lily mode category list

---

## 📁 Updated repo file structure (target state)

```
play-and-build/
├── index.html
├── DESIGN.txt
├── README.md
├── lily_long_brown.png
├── lily_bob_brown.png
├── lily_ponytail_brown.png
├── outfit_casual.png           ← Phase 3 first deliverable
├── outfit_sundress.png
├── outfit_pajamas.png
├── outfit_sporty.png
├── outfit_party.png
├── outfit_beach.png
├── outfit_winter.png
├── outfit_rainy.png
├── outfit_school.png
├── outfit_costume.png
├── hat_*.png                   ← Phase 4
├── glasses_*.png
├── jewelry_*.png
├── bag_*.png
└── pet_*.png
```

---

*Pivot decided in Session 3. Architecture is locked. Onward to Outfit #1.*
