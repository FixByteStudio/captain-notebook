# Cuisine mapping v2: invalidation review and applied updates

Reviewed all 944 rows and applied the project-scope recommendations from this audit. “Invalid” means excluded from the project’s cuisine/dish fields, not necessarily an incorrect OSM tag. OSM permits some styles and venue formats as `cuisine=*`; this project keeps cuisine identity and dish labels separate ([OSM cuisine guidance](https://wiki.openstreetmap.org/wiki/Key:cuisine)).

## Rows marked invalid

For these rows, `canonical_value` is blank and both `status` and `category_type` are `invalid`.

| Previous canonical value | Raw values | Reason |
|---|---|---|
| `braised` | `braise`, `braised` | Cooking method. |
| `breakfast` | `breakfast`, `petit_dejeuner` | Meal occasion. |
| `brunch` | `brunch` | Meal occasion. |
| `crepe` | `creperie`, `créperie` | Outside cuisine/dish scope. |
| `fast_food` | `fast_food`, `restauration_rapide`, `fastfood` | Service/venue format. |
| `fine_dining` | `semi-gastronomique`, `semi_gastronomic` | Service/price format. |
| `grilled_food` | `grill` | Generic cooking method/style. |
| `healthy` | `healthy`, `health_food` | Health positioning. |
| `homemade` | `maison`, `fait-maison`, `fait_maison`, `homemade`, `plats_faits-maison` | Preparation/marketing claim. |
| `italian` | `italian ← (ou cuisine=sardes` | Outside cuisine/dish scope. |
| `local` | `locale`, `produits_locaux`, `terroir` | Generic provenance descriptor. |
| `lunch` | `lunch`, `déjeuner` | Meal occasion. |
| `modern` | `modern`, `moderne` | Generic culinary/marketing style. |
| `modernist` | `modernist` | Generic culinary style. |
| `organic` | `organic`, `bio` | Production attribute. |
| `paleo` | `paleo` | Diet pattern. |
| `pierrade` | `pierrade` | Cooking/tabletop service method. |
| `plancha` | `plancha` | Cooking method. |
| `rotisserie` | `rotisserie`, `rôtisserie` | Cooking method/venue format; specific dishes such as poulet_roti remain dishes. |
| `seasonal` | `seasonal`, `saison`, `de_saison` | Menu availability attribute. |
| `slow_food` | `slow_food` | Food philosophy/marketing label. |
| `snack` | `petite_restauration` | Generic service/food occasion (only the cuisine-category mapping was invalidated). |
| `street_food` | `cuisine_de_rue`, `street-food`, `streetfood` | Service/eating format. |
| `vegetarian` | `végétale`, `végétarienne`, `cuisine_végétale` | Dietary attribute. |
| `wood_fired` | `cuisine_feu_de_bois` | Cooking/fuel method. |

**Invalidated in this update:** 48 rows. The three buffet/à volonté variants were already invalid from the prior decision and remain invalid; they are included in the current invalid-row total below.

## Previously invalidated buffet labels

| Raw value | Current canonical value | Status | Category |
|---|---|---|---|
| `buffet` | blank | invalid | invalid |
| `a_volonté` | blank | invalid | invalid |
| `à_volonté` | blank | invalid | invalid |

## Kept in the Arab cuisine group by request

| Raw value | Canonical value | Category |
|---|---|---|
| `oriental` | `arab` | cuisine |
| `halal` | `arab` | cuisine |

Both use the existing `arab` origin entry in `cuisine_region_mapping.csv`.

## Other corrections applied

| Raw value(s) | Updated mapping |
|---|---|
| `japonais` | `japanese` cuisine |
| `réunionnais`, `réunionnaise`, `réunionnaises` | `reunionese` cuisine |
| `burger_&_rôtisserie` | `burger` dish |
| `creperie`, `créperie` | invalid; venue-type text, not food label |
| `italian ← (ou cuisine=sardes` | invalid; editorial note is not a usable raw label |
| Mapped canonical values with uppercase letters | normalized to lowercase for consistent grouping |

`regional` and `international` remain mapped. Existing invalid rows were preserved.

## Current row counts

- Mapped cuisine rows: 391
- Mapped dish rows: 353
- Invalid rows: 200

## Kaggle update required

The mapping CSV is ignored by Git (`*.csv`), so GitHub notebook deployment does not upload this change. Upload the revised CSV as a new version of the attached Kaggle mapping dataset, then rerun the cleaner and France prospect export before rerunning the Tours analysis.
