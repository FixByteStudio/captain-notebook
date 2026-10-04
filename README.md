# France Food Delivery Market Analysis

This project combines official French business and population data with OpenStreetMap venue and cuisine data to explore food-delivery prospecting opportunities in France and Tours. The notebooks produce exploratory statistics and charts; venue counts and population-normalized rates describe mapped supply, not measured delivery demand.

## Notebooks and run order

1. [`france_geofabrik_raw_data/clean_geofabrik_food_venues.ipynb`](france_geofabrik_raw_data/clean_geofabrik_food_venues.ipynb) prepares the Geofabrik venue extract and writes `food_venues_cleaned.csv`.
2. [`france_food_delivery_prospects.ipynb`](france_food_delivery_prospects.ipynb) combines the cleaned Geofabrik venues with BANCO, analyzes cuisine and commune/region patterns, and writes `france_food_delivery_prospects.csv`.
3. [`tours_food_delivery_market_analysis.ipynb`](tours_food_delivery_market_analysis.ipynb) analyzes the Tours market using that prospect export.

Run the notebooks in order. All CSV inputs and outputs are excluded from this Git repository and belong in the project’s Kaggle Dataset (or in the corresponding local folders for local runs). Preserve the folder structure below so the notebooks can find their inputs:

```text
france_banco_raw_data/
  data.csv
  metadata.csv
france_geofabrik_raw_data/
  food_venues_from_pbf.csv
  cuisine_mapping_v2.csv
  cuisine_region_mapping.csv
insee_commune_population_2023/
  commune_population_2023.csv
commune_region_2026.csv
```

The Geofabrik preparation notebook creates `food_venues_cleaned.csv`. The France-wide analysis creates `france_food_delivery_prospects.csv`, which the Tours analysis then reads. Keep these generated files in your Kaggle working directory or the matching local project folders.

## Data sources

- **BANCO:** France’s *Base nationale des commerces ouverte*, distributed through [data.gouv.fr](https://www.data.gouv.fr/datasets/base-nationale-des-commerces-ouverte). The source metadata is part of the Kaggle Dataset; the repository retains [`france_banco_raw_data/license.txt`](france_banco_raw_data/license.txt). Check the publisher’s current license and terms before redistribution.
- **OpenStreetMap:** Food venues and cuisine tags come from a Geofabrik extract. OpenStreetMap data is available under the [ODbL](https://www.openstreetmap.org/copyright); retain required attribution when redistributing derived data or maps.
- **INSEE:** Commune population figures are from the 2023 population table; administrative commune and region codes use the 2026 Code officiel géographique. The compact population and commune-to-region lookups are supplied through the Kaggle Dataset.

Cuisine mapping files are supplied through the Kaggle Dataset. See the cleaning notebook for how cuisine tokens and mappings are applied.

## Environment

The notebooks use Python with `pandas` and `matplotlib`; the Geofabrik preparation notebook also uses the Python standard library. A minimal local setup is:

```bash
python -m pip install pandas matplotlib jupyter
jupyter notebook
```

Use the notebook run order above. CSV datasets and exports are excluded from Git; download or attach the project’s Kaggle Dataset and place the input files in the documented folder structure before running locally.

## Kaggle

Attach a Kaggle Dataset containing the required source files, preserving the directory names and relative paths listed above. The France prospect notebook finds inputs under `/kaggle/input/<dataset-slug>/` and writes its export to `/kaggle/working/`. Kaggle mounts datasets read-only. The Geofabrik cleaner and Tours notebook still use project-relative paths, so copy their inputs into the working directory or adjust those paths when running them separately on Kaggle.

The GitHub Actions workflow in [`.github/workflows/notebook-validation.yml`](.github/workflows/notebook-validation.yml) validates notebook structure on pushes and pull requests. The [`publish-france-notebook-to-kaggle.yml`](.github/workflows/publish-france-notebook-to-kaggle.yml) workflow publishes and runs `france_food_delivery_prospects.ipynb` on Kaggle when that notebook changes on `main`; it can also be started manually from the Actions tab. It stays inactive until the repository owner configures these repository variables and secret:

- Variables: `KAGGLE_KERNEL_ID` (`owner/kernel-slug`), `KAGGLE_KERNEL_TITLE`, and `KAGGLE_DATASET_SOURCE` (`owner/dataset-slug`). `KAGGLE_KERNEL_PRIVATE` is optional and defaults to `true`.
- Secret: `KAGGLE_API_TOKEN`, generated from the Kaggle account API settings.

The attached dataset must already exist and be accessible to the API-token owner. For a new kernel, set `KAGGLE_KERNEL_ID` to the owner/slug that matches `KAGGLE_KERNEL_TITLE`; the first push creates it. Later pushes update that kernel. The workflow sends only the notebook and generated kernel metadata; the CSV dataset remains on Kaggle and is attached as `dataset_sources`. Kaggle’s CLI `kernels push` starts a run by default. See [Kaggle kernel push documentation](https://github.com/Kaggle/kaggle-cli/blob/main/docs/kernels.md) and [kernel metadata format](https://github.com/Kaggle/kaggle-cli/blob/main/docs/kernels_metadata.md).
