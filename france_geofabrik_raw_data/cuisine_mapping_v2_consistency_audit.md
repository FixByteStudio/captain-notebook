# Cuisine mapping v2 consistency audit

**Audit date:** 2026-10-04  
**Files reviewed:** `cuisine_mapping_v2.csv` (944 rows) and `cuisine_region_mapping.csv` (189 rows).  
**Scope:** mapping integrity, cuisine/dish classification, composite raw labels, and cuisine-origin joins. This report flags issues; it does not change either CSV.

## Summary

- **No structural key errors found:** all raw mapping keys are nonblank and unique after Unicode NFKC, trim, and casefold; no raw entries contain semicolons; status/category pairs are consistent; mapped canonical labels are nonblank and lowercase.
- **2 canonical labels occur in both cuisine and dish fields:** `pizza` and `barbecue`.
- **3 canonical cuisine labels have no origin row:** `barbecue`, `macrobiotic`, and `pizza`.
- **11 cuisine-origin rows do not match a canonical cuisine label exactly**; some are stale, some are synonyms, and some reflect lost country-level distinctions.
- **25 mapped composite raw phrases** containing commas, `&`, or `+` resolve to a single cuisine or dish canonical label.
- **25 dish canonical labels** are broad ingredients/products/beverages rather than clearly named dishes; these need a consistent definition of the `dish` field.

## High-priority inconsistencies

### 1. Cuisine/dish category conflicts

| Canonical label | Cuisine raw values | Dish raw values | Issue to resolve |
|---|---|---|---|
| `barbecue` | `barbecue` | `babecue`, `bbq` | A cooking style is mapped as cuisine while its spelling variants are dishes. Choose one category; if barbecue is outside cuisine identity, remove its cuisine mapping. |
| `pizza` | `pizzeria` | `distributeur_de_pizza`, `pizza`, `pizza à emporter`, `pizza, burger, galettes`, `pizza, burger, spécialités de montagne`, `pizza, poulet grillé`, `pizza,regional`, `pizza_le_mercredi`, `pizza_socca`, `pizzas` | `pizzeria` is a venue type; pizza itself is a dish. Keep pizza in one output category. |

The region map has `tex_mex` as an origin cuisine, but `tex-mex` is currently mapped to `category_type=dish`. This prevents it from receiving cuisine-origin fields. Decide whether Tex-Mex is a cuisine label or whether the origin row is obsolete.

### 2. Cuisine labels missing origin mappings

| Canonical cuisine | Current mapping evidence | Issue |
|---|---|---|
| `barbecue` | `barbecue` | Also duplicated in the dish category; settle category before adding origin. |
| `macrobiotic` | `macrobiotic` | Diet/food philosophy label; decide whether it belongs in cuisine at all. |
| `pizza` | `pizzeria` | Already represented in the dish category; `pizzeria` is a venue label. |

### 3. Origin lookup rows that cannot match current canonical cuisine output

The origin join uses the `cuisine` key, while the cleaner emits `canonical_value`. These origin keys currently have no exact normalized canonical-cuisine match:

| Origin key | Matching raw mapping, if any | Origin country / region | Finding |
|---|---|---|---|
| `arabic` | arabic → arab [mapped/cuisine] | Arab world / Middle East | Alias is already collapsed to canonical `arab`; duplicate origin row is unused but has the same Arab-world origin. |
| `ardeche` | no exact raw-value row | France / Western Europe | The source phrase `cuisine_ardéchoise` is collapsed to `french`; decide whether that deliberate roll-up makes this origin row obsolete. |
| `berber` | berber → north_african [mapped/cuisine] | Maghreb / North Africa | Raw `berber` is collapsed to `north_african`; this loses the specific Berber label. |
| `libyan` | libyan → north_african [mapped/cuisine] | Libya / North Africa | Raw `libyan` is collapsed to `north_african`; this loses country-level origin. |
| `maghrebi` | maghrebi → north_african [mapped/cuisine] | Maghreb / North Africa | Raw `maghrebi` is collapsed to `north_african`; this is a broad regional alias. |
| `mascarene` | mascarene → north_african [mapped/cuisine] | Mascarene Islands / East Africa | Raw `mascarene` is currently collapsed to `north_african`, which conflicts with the origin row’s Mascarene Islands / East Africa geography. |
| `mauritian` | mauritian → north_african [mapped/cuisine] | Mauritius / East Africa | Raw `mauritian` is currently collapsed to `north_african`, which conflicts with the origin row’s Mauritius / East Africa geography. |
| `mountain` | mountain → (blank) [invalid/invalid] | Mountain / Global | Raw `mountain` is invalid, so this origin row is unreachable. |
| `perigord` | no exact raw-value row | France / Western Europe | No exact source mapping; the word appears inside a longer composite phrase mapped to `french`. |
| `tex_mex` | no exact raw-value row | United States / Southern United States | Raw `tex-mex` is classified as a dish, but this origin row treats `tex_mex` as a cuisine. |
| `tunisian` | tunisian → north_african [mapped/cuisine] | Tunisia / North Africa | Raw Tunisian variants are collapsed to `north_african`; this loses country-level origin. |

### 4. Over-broad North African canonical group

The mapping currently assigns 20 raw cuisine labels to `north_african`. Several are specific country or cultural cuisines (including Moroccan, Tunisian, and Libyan labels), so this canonical value suppresses country-level origin detail. More seriously, `mascarene`, `mauritian`, and Mauritius spellings are also assigned to North Africa even though the origin lookup associates them with the Mascarene Islands / Mauritius in East Africa. Review this group and keep country/cultural labels distinct where the region analysis needs that detail.

**Raw aliases currently in this group:**

`berber`, `berbère`, `libyan`, `maghreb`, `maghrebi`, `maroc`, `marocaine`, `mascarene`, `mauritian`, `mauritius`, `mauritus`, `moroccan`, `morrocan`, `north-african`, `north_african`, `tunesian`, `tunisian`, `tunisie`, `tunisien`, `tunisienne`

## Multi-concept raw values collapsed to one output label

Per project rules, commas and ampersands are retained in raw tokens and are not split. The issue is that these complete phrases often list several cuisines or foods but the mapping assigns only one canonical label. Keep the raw phrases intact; decide whether each should be invalid, represented as a specific combined label, or manually assigned to multiple canonical outputs.

| Category | Raw value | Current canonical |
|---|---|---|
| dish | `burger_&_rôtisserie` | `burger` |
| cuisine | `french,_food_fusion,_haute_cuisine,_périgord,_gastronomie` | `french` |
| cuisine | `italian,_roumain,_traditionnel` | `italian` |
| cuisine | `regional,_organic` | `regional` |
| cuisine | `bistro,regional` | `regional` |
| dish | `burger,salad` | `burger` |
| cuisine | `chinese, vietnamese` | `chinese` |
| dish | `crêpe, galette, ice cream, beverages, drinks` | `crepe` |
| dish | `crêpe, galette, ice cream, beveragws, drinks` | `crepe` |
| dish | `crêpe, galette, meat, fish, salad, ice cream, beverages, drinks` | `crepe` |
| dish | `crêpes_galettes_&_cachapas_focaccia` | `crepe` |
| cuisine | `french, italian` | `french` |
| cuisine | `french, meat` | `french` |
| cuisine | `french, regional` | `french` |
| dish | `glace, burger` | `ice_cream` |
| dish | `kebab, hamburger, pizza` | `kebab` |
| dish | `pizza, burger, galettes` | `pizza` |
| dish | `pizza, burger, spécialités de montagne` | `pizza` |
| dish | `pizza, poulet grillé` | `pizza` |
| dish | `pizza,regional` | `pizza` |
| dish | `poke, sushi, bobun, ramen` | `poke` |
| dish | `poulet_fermier,_andouille_maison` | `chicken` |
| dish | `salad,french` | `salad` |
| dish | `sandwich,salad,dish_of_the_day` | `sandwich` |
| dish | `tapas,regional` | `tapas` |

## Invalid raw-key hygiene

The following entries are already `invalid`, so they do not leak into `cuisine_standard` or `dish`. They still make the mapping table harder to maintain and look like fragments or collection artifacts. Remove them from the lookup or add a short audit reason if raw-key history must be retained.

| Raw key | Why it looks inconsistent |
|---|---|
| `bur`, `fro`, `gastr`, `rac`, `reg`, `take`, `tra`, `en` | Truncated fragments or standalone function words rather than stable cuisine/dish labels. |
| `1` | Numeric value with no food meaning. |
| `=bing` | Malformed/search-like artifact. |
| `9h-30_18h30_tous_les_jours` | Opening-hours text, not a cuisine/dish token. |
| `italian ← (ou cuisine=sardes` | Editorial note embedded in a raw key. |
| `café,_courses`, `savory_waffles artisanal_ice_cream`, `tapas,_moules_frites,_inspiration_hollandaise` | Free-text/composite phrases containing unrelated descriptors; already invalid, but confirm whether the raw forms are genuine OSM values. |
| `ecai`, `out_fry`, `pabini`, `boa`, `buffer` | Unclear spellings or fragments; keep invalid unless verified against source tags. |

## Dish field scope inconsistencies

These canonical values currently use `category_type=dish` but often describe a beverage, ingredient, or product rather than a prepared dish. This may be acceptable if `dish` means “menu item or food/drink offering”; if it means a prepared dish, they should move to a broader `food_item`/`beverage` category or be excluded.

| Canonical dish label | Raw aliases |
|---|---|
| `beef` | `beef` |
| `beer` | `beer`, `bieres`, `bière`, `bières` |
| `chicken` | `chicken`, `poulet`, `poulet_fermier,_andouille_maison` |
| `cocktail` | `cocktail`, `cocktails` |
| `coffee` | `coffee` |
| `cream` | `cream` |
| `drinks` | `boisson`, `boissons`, `drinks` |
| `duck` | `canard`, `duck` |
| `fish` | `fish`, `poisson` |
| `fruit` | `fruit` |
| `juice` | `juice` |
| `lobster` | `lobster` |
| `meat` | `meat`, `viande`, `viandes` |
| `mussel` | `moule`, `moulerie`, `moules`, `musles`, `mussel`, `mussels` |
| `pork` | `pork` |
| `potato` | `patate`, `pommes_de_terre`, `potato` |
| `poultry` | `poultry` |
| `rice` | `rice`, `riz` |
| `sausage` | `sausage` |
| `shellfish` | `crustasés`, `shellfish` |
| `soda` | `soda` |
| `strawberry` | `fraise` |
| `tea` | `tea`, `thés` |
| `truffle` | `truffes` |
| `wine` | `vin`, `vins`, `wine` |

## Origin-map normalization collisions

`normalized_cuisine` intentionally consolidates multiple origin keys: `french`, `regional`, and `lyonnais` resolve to French; `arab` and `arabic` resolve to Arab; `iranian` and `persian` resolve to Persian. Check geography consistency within each group before deduplicating. `arab`/`arabic` and Persian/Iranian have matching origin fields. French, regional, and lyonnais share normalized country France and region Western Europe, but intentionally differ in `normalized_locality` (France, Regional French, Lyon); locality-level summaries should preserve that distinction or explicitly roll it up.

`normalized_country` also contains non-country groups such as `Arab world`, `North Africa`, and `Mountain`. `Arab world` is an explicit project choice; the broader column therefore represents origin areas as well as sovereign countries. Consider renaming it or documenting that mixed level if this appears in published analysis.

## Intentional project decisions retained

- `oriental` and `halal` map to canonical `arab`, as requested.
- `regional` remains mapped and resolves to French origin for the project’s deduplicated origin analysis.
- `international` remains a broad cuisine label.
- Multi-value extraction continues to split only on semicolons; commas, underscores, and ampersands stay intact.

## Suggested resolution order

1. Resolve `pizza` and `barbecue` category conflicts; decide whether `macrobiotic` is cuisine or out-of-scope diet/style.
2. Fix the clearly incorrect `mascarene`/`mauritian` → `north_african` assignments and decide whether to preserve Maghreb country-level labels.
3. Review the 25 comma/ampersand composite mappings for information loss.
4. Align or remove origin-map keys that are unused after canonicalization, and define whether broad ingredients/drinks belong in `dish`.

No mapping edits were made during this audit. The CSV remains ignored by Git, so any later approved changes must be uploaded as a new Kaggle dataset version before regenerating the cleaned venue and lead exports.
