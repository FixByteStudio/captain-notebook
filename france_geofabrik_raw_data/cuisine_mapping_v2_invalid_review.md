# Cuisine mapping v2: invalidation review

Reviewed all 944 rows in the mapping file, including mapped cuisine rows, mapped dish rows, and existing invalid rows. This log recommends out-of-scope cuisine rows for review; it does not bulk-change them. Here “invalid” means outside this project’s cuisine/dish fields, not necessarily an incorrect OSM tag. OSM’s `cuisine=*` guidance includes some style/place labels, while this project uses cuisine for food identity and keeps dishes separately ([OSM guidance](https://wiki.openstreetmap.org/wiki/Key:cuisine)).

## Mapped cuisine rows recommended for invalidation

For each listed row, the proposed change is: blank `canonical_value`, set `status="invalid"`, and set `category_type="invalid"`. These groups describe meal occasions, venue/service formats, dietary or production attributes, vague descriptors, or cooking methods.

| Current canonical value | Raw values | Reason |
|---|---|---|
| `braised` | `braise`, `braised` | Cooking method, not cuisine or a named dish. |
| `breakfast` | `breakfast`, `petit_dejeuner` | Meal occasion, not cuisine or a specific dish. |
| `brunch` | `brunch` | Meal occasion, not cuisine or a specific dish. |
| `fast_food` | `fast_food`, `restauration_rapide`, `fastfood` | Service/venue format; venue type is represented separately. |
| `fine_dining` | `semi-gastronomique`, `semi_gastronomic` | Service/price format, not type of food or a dish. |
| `grilled_food` | `grill` | Generic cooking method/food style, not cuisine or a named dish. |
| `halal` | `halal` | Dietary/religious preparation standard, not cuisine origin or a dish. |
| `healthy` | `healthy`, `health_food` | Health positioning, not cuisine or a specific dish. |
| `homemade` | `maison`, `fait-maison`, `fait_maison`, `homemade`, `plats_faits-maison` | Preparation/marketing claim, not cuisine or dish identity. |
| `local` | `locale`, `produits_locaux`, `terroir` | Generic provenance/marketing descriptor, not canonical cuisine or dish. |
| `lunch` | `lunch`, `déjeuner` | Meal occasion, not cuisine or a specific dish. |
| `modern` | `modern`, `moderne` | Generic culinary/marketing style, not cuisine origin or named dish. |
| `modernist` | `modernist` | Generic culinary style, not cuisine origin or named dish. |
| `organic` | `organic`, `bio` | Production attribute, not cuisine or dish identity. |
| `paleo` | `paleo` | Diet pattern, not cuisine or a specific dish. |
| `pierrade` | `pierrade` | Cooking/tabletop service method, not cuisine or a specific dish. |
| `plancha` | `plancha` | Cooking method, not cuisine or a named dish. |
| `rotisserie` | `rotisserie`, `rôtisserie` | Cooking method/venue format; specific foods such as poulet_roti remain dishes. |
| `seasonal` | `seasonal`, `saison`, `de_saison` | Menu availability attribute, not cuisine or dish. |
| `slow_food` | `slow_food` | Food philosophy/marketing label, not cuisine or dish. |
| `snack` | `petite_restauration` | Generic service/food occasion, not cuisine or a specific dish. |
| `street_food` | `cuisine_de_rue`, `street-food`, `streetfood` | Service/eating format, not cuisine or a named dish. |
| `vegetarian` | `végétale`, `végétarienne`, `cuisine_végétale` | Dietary attribute, not cuisine or a specific dish. |
| `wood_fired` | `cuisine_feu_de_bois` | Cooking/fuel method, not cuisine or a dish. |

**Recommended for review:** 46 cuisine rows across 24 canonical groups.

## Already invalidated by the explicit buffet decision

| Raw value | Canonical value | Status | Category |
|---|---|---|---|
| `buffet` | `` | `invalid` | `invalid` |
| `a_volonté` | `` | `invalid` | `invalid` |
| `à_volonté` | `` | `invalid` | `invalid` |

## Mapping corrections or decisions to review separately

| Raw value(s) | Current mapping | Suggested review |
|---|---|---|
| `japonais` | `Asian` cuisine | Likely map to `japanese`; correction, not invalidation. |
| `oriental` | `Arabic` cuisine | Broad and ambiguous; retain only if this project’s established mapping is intentional. |
| `réunionnais`, `réunionnaise`, `réunionnaises` | `Caribbean` cuisine | Consider `reunionese`; correction, not invalidation. |
| `italian ← (ou cuisine=sardes` | `Italian` cuisine | Contains an editorial note and likely is not a real raw value; remove or invalidate this alias. |
| `regional` and related labels | `regional` cuisine | Kept because the project intentionally resolves regional cuisine to French origin for origin deduplication. |
| `international` and aliases | `international` cuisine | Broad but still a cuisine label; keep unless broad categories are excluded by policy. |
| `burger_&_rôtisserie` | `burger` cuisine | Likely reclassify as dish; don’t invalidate the burger concept. |
| `creperie`, `créperie` | `crepe` cuisine | These describe a venue; decide whether to retain as a crepe dish proxy or invalidate as venue-type text. |

## Dish rows not included in the invalidation recommendations

The review did not recommend invalidating dish-category rows such as `poulet_roti`, `snack`, or `en-cas`. Some are broad menu-item categories and may need a separate dish-scope policy, but they were not mixed into cuisine invalidation candidates.

## Scope notes

- Existing invalid rows were preserved.
- The current working CSV now has the three buffet-related entries marked invalid.
- The CSV is ignored by Git (`*.csv`), so upload the revised mapping as a new Kaggle dataset version before rerunning the cleaner and prospect export.
