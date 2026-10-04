# Cuisine mapping v2 consistency audit

**Audit date:** 2026-10-04  
**Files reviewed:** `cuisine_mapping_v2.csv` (944 rows) and `cuisine_region_mapping.csv` (187 rows).
**Scope:** mapping integrity, cuisine/dish classification, composite raw labels, and cuisine-origin joins. The mapping and origin CSVs were corrected locally based on the findings below. Both CSVs are ignored by Git, so publish the revised files to the Kaggle dataset separately.

## Current state

- The mapping has **389 cuisine rows, 353 dish rows, and 202 invalid rows**. Each non-invalid row has one category; invalid rows have blank canonical values and `category_type=invalid`.
- All 944 normalized raw keys are nonblank and unique. Raw values contain no semicolons. Multi-output canonical values use semicolons, which the cleaner can split after exact raw-label matching.
- The mapping emits **187 distinct canonical cuisines** and **197 distinct canonical dishes**. No canonical label is emitted in both categories.
- All 187 emitted cuisine labels have exactly one normalized key in the 187-row cuisine-origin lookup, and all origin keys are used. This is key alignment only; it does not establish that every origin assignment is geographically or culturally correct.
- `oriental` and `halal` still map to `arab`, as requested. `barbecue` is now a dish; `pizzeria` and `macrobiotic` are invalid; `tex-mex` is now the cuisine `tex_mex`.
- The previously over-broad `north_african` assignments for specific cuisines were corrected. It now contains only `north-african` and `north_african`. Country/cultural labels such as Berber, Libyan, Moroccan, Mauritian, and Tunisian remain distinct, as do `ardeche` and `perigord` where present in composite labels.

## Remaining issues to decide

### 1. Mixed cuisine-and-dish raw phrases

The lookup schema assigns one category to each complete raw value. These phrases combine concepts that belong in different output fields, so the current single-category mapping necessarily loses a component:

| Raw value | Current mapping | Lost or unresolved component |
|---|---|---|
| `french, meat` | cuisine: `french` | `meat` belongs in the dish/food-item field. |
| `salad,french` | dish: `salad` | `french` is a cuisine. |
| `tapas,regional` | dish: `tapas` | `regional` is a cuisine. |
| `pizza,regional` | dish: `pizza` | `regional` is a cuisine. |

Resolving these fully requires either a schema that permits one raw key to populate both cuisine and dish, or explicit project choices to keep only one concept. Do not change the raw tokenization rule: commas and underscores are part of these complete labels, and only semicolons separate OSM cuisine values.

### 2. Multi-concept phrases with incomplete detail

The clearly separable same-category components in composite labels have been mapped to semicolon-delimited canonical values. Some descriptors are still intentionally or implicitly omitted because the current mapping does not define a suitable canonical label. Examples include `regional,_organic` → `regional`, `bistro,regional` → `regional`, `burger_&_rôtisserie` → `burger`, `pizza, burger, spécialités de montagne` → `pizza;burger`, and `pizza,regional` → `pizza`. Review whether to add concepts such as `organic`, `bistro`, `rotisserie`, or mountain specialties, or continue treating them as out of scope.

### 3. Dish field definition

The `dish` category still includes broad offerings such as beverages (`beer`, `coffee`, `drinks`, `juice`, `tea`, `wine`), ingredients/products (`beef`, `chicken`, `fish`, `fruit`, `meat`, `rice`, `strawberry`), and menu items. Decide whether `dish` means any menu offering or only prepared dishes. If it means prepared dishes, a future schema change to `food_item`/`beverage` or an explicit exclusion policy is needed.

### 4. Origin geography is not a uniform country level

The origin lookup uses `normalized_country` for a mixture of sovereign countries and broader areas, including `Arab world` and `North Africa`. `Arab world` is an intentional project choice. Treat the column as an origin area unless the project later standardizes its geographic level. The North African origin for the two broad `north_african` aliases remains an aggregate assignment; this does not affect the distinct country/cultural cuisine mappings restored above.

### 5. Invalid-key history and uncertain dish labels

Invalid rows remain in the lookup so the audit trail is preserved. Continue reviewing unclear spellings, fragments, and broad dish values against source tags before changing their status. In particular, a label being invalid does not imply its raw source value should be deleted from the mapping file.

## Composite raw values: current treatment

The 25 mapped raw values containing commas, ampersands, or plus signs remain intact as lookup keys. Eighteen now map to multiple canonical values separated by semicolons; the remaining rows retain one or more selected concepts. The raw strings are never split on commas, underscores, or ampersands.

Mixed-category cases that cannot be represented completely with the current one-category-per-raw-key schema are listed above. For the other composites, inspect their current canonical outputs in `cuisine_mapping_v2.csv` before changing them; several contain venue/menu descriptions rather than clean lists of equivalent concepts.

## Project rules retained

- `oriental` and `halal` map to `arab`.
- `regional` remains a mapped cuisine and resolves to French origin in the origin lookup.
- `international` remains a broad cuisine label.
- Extraction splits raw OSM cuisine values only on semicolons. Matching normalization trims whitespace, applies Unicode normalization, and casefolds while preserving underscores and commas.
- Invalid labels remain available for review and are excluded according to their `status` during cleaning.

## Publishing note

The revised `cuisine_mapping_v2.csv` and `cuisine_region_mapping.csv` are local ignored files. The notebook-only GitHub Actions deploy will not upload them. Upload a new version of the attached Kaggle dataset containing both revised CSVs, then rerun the cleaner and refresh the cleaned Geofabrik and prospect exports before rerunning dependent analysis notebooks.
