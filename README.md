# El Paso Brain Drain Analysis

Does El Paso lose more of its young adults than comparable Texas metros — and if so, how, and to where? This analysis compares El Paso County against five other major Texas counties (Travis/Austin, Bexar/San Antonio, Dallas, Harris/Houston, Tarrant/Fort Worth) using two U.S. Census Bureau data products.

## Bottom line

El Paso's 18-24 population is the only one among six major Texas counties that **shrank** between 2013 and 2023 (-6.7%), while every other county grew, some by double digits. The cause isn't that El Paso's young people leave at an unusually high rate — its overall out-migration rate is mid-pack. The cause is that **far fewer young people move in to replace them** than in any peer county: El Paso's inflow-to-outflow ratio for 18-24-year-olds is the lowest of all six counties in every year tested (roughly 1 arrival for every 2 departures, vs. closer to 1-for-1 or better everywhere else). El Paso's own 25-30 cohort, by contrast, grew right in line with its peers (+11.3%) — this is a story about the front end of the pipeline (who moves in), not a wholesale exodus.

Two mechanisms now have direct data support, not just plausible narrative: **UTEP doesn't import 18-year-olds** (98% commuter, 83% from El Paso County itself), which plausibly explains the weak 18-24 pull; and **federal/military employment is doing disproportionate work in propping up the 25-30 pull** — El Paso's 25-30 in-migrants are 5.2x more likely to be federal employees, 12.2x more likely to be active-duty military, and 2.2x more likely to work in protective-service occupations than the average of the other five counties' 25-30 in-migrants.

## Data sources

1. **ACS 1-Year Public Use Microdata Sample (PUMS)** — used to build cohort population counts (18-24, 25-29/30) by county and year, 2012-2023 (2020 not released due to COVID data quality issues).
2. **ACS 5-Year Migration Flows API** (`api.census.gov/data/{year}/acs/flows`) — used for actual mover counts and destinations. This is a Census-published, pre-aggregated product; no PUMA-level crosswalk is needed to use it, unlike PUMS.

## Key methodology decisions (and why)

**Why the Migration Flows API instead of raw PUMS migration variables.** The original approach attempted to compute out-migration directly from PUMS microdata using the `MIGPUMA` variable (which identifies where a respondent lived a year ago). This turned out to be impractical to source reliably: Migration PUMAs are a distinct, coarser geography from residence PUMAs, and the authoritative crosswalk file lives only as an Excel workbook on IPUMS that could not be reliably parsed or navigated. The Migration Flows API sidesteps this entirely — Census has already done the crosswalk internally and publishes county-level mover counts and destinations directly.

**Two real crosswalk bugs were caught and fixed along the way** (relevant if you're reusing `brain_drain_trend_fixed.py`, which still uses residence PUMA codes for population counts, not migration): an earlier draft's "El Paso" PUMA codes (05301-05304) were actually Travis County/Austin's codes, and "Tarrant" (02101-02107) was Ellis + Johnson County — both were silently returning *plausible-looking, non-zero, wrong* data in every year tested, not just recent ones. All six counties' codes are now verified directly against Census's own `NAME` field for both the 2010 and 2020 PUMA vintages.

**Coverage limits of the Migration Flows data (real Census product limits, not bugs):**
- County-to-county detail exists for 5-year vintages ending 2010 through 2020 only. Census discontinued full county-to-county publication after the 2016-2020 release.
- A replacement "state-to-county" product exists for the 2018-2022 vintage onward, but — confirmed empirically in this analysis — it only publishes **inbound** flows (who moved into the county from state X), not outbound. It cannot be used to extend the out-migration analysis past 2020.
- Age-cohort detail (the `AGE=` characteristic) exists for exactly two vintages: 2006-2010 and 2011-2015. No other year has an age breakdown in this dataset, for any county.
- The 2019-2023 state-to-county vintage has not been published yet as of this analysis (July 2026); only 2018-2022 is available.

Given these limits, the cohort-specific in/out comparison relies on the 2010 and 2015 vintages, and the population-shrinkage finding relies on the annual PUMS trend (2012-2023).

## Key numbers

**Population change, 18-24 cohort, 2013-2023:**

| County | 2013 | 2023 | % change |
|---|--:|--:|--:|
| **El Paso** | 99,494 | 92,847 | **-6.7%** |
| Travis (Austin) | 117,682 | 120,312 | +2.2% |
| Dallas | 244,435 | 253,821 | +3.8% |
| Bexar (San Antonio) | 199,950 | 210,385 | +5.2% |
| Harris (Houston) | 428,939 | 453,585 | +5.7% |
| Tarrant (Fort Worth) | 182,581 | 209,531 | +14.8% |

El Paso's 25-30 cohort, for comparison, grew +11.3% over the same period — in line with peers. The shrinkage is specific to the youngest cohort.

**In-migration ÷ out-migration ratio, 18-24 cohort (2015 vintage, the more recent of the two cohort-tagged years):**

| County | Moved in | Moved out | In/out ratio |
|---|--:|--:|--:|
| **El Paso** | 3,508 | 6,841 | **0.51** |
| Dallas | 17,377 | 26,400 | 0.66 |
| Harris (Houston) | 24,681 | 37,646 | 0.66 |
| Tarrant (Fort Worth) | 13,345 | 19,429 | 0.69 |
| Bexar (San Antonio) | 14,859 | 19,060 | 0.78 |
| Travis (Austin) | 16,363 | 18,056 | 0.91 |

Same pattern holds in the 2010 vintage and in the 25-29 cohort — El Paso has the lowest ratio of all six counties in every comparison run.

**All-ages out-migration rate, 2016-2020 vintage (using 2020 Census county population as the denominator):**

| County | Out-migrants | Population | Rate |
|---|--:|--:|--:|
| Travis (Austin) | 90,679 | 1,290,188 | 7.03% |
| Dallas | 152,795 | 2,613,539 | 5.85% |
| **El Paso** | 45,817 | 865,657 | **5.29%** |
| Tarrant (Fort Worth) | 100,712 | 2,110,640 | 4.77% |
| Bexar (San Antonio) | 92,341 | 2,009,324 | 4.60% |
| Harris (Houston) | 189,998 | 4,731,145 | 4.02% |

El Paso is squarely mid-pack here — the "brain drain" signal is not a matter of unusually high flight, it's a matter of unusually weak replacement among the young specifically.

**Top destinations for El Paso out-migrants (2010-2020, all ages, all years combined):**

| Rank | Destination | Total movers |
|--:|---|--:|
| 1 | Doña Ana County, NM (Las Cruces) | 23,079 |
| 2 | Bexar County, TX (San Antonio) | 15,325 |
| 3 | Harris County, TX (Houston) | 9,283 |
| 4 | Maricopa County, AZ (Phoenix) | 8,391 |
| 5 | Travis County, TX (Austin) | 8,111 |
| 6 | Tarrant County, TX (Fort Worth) | 6,132 |

The dominance of Doña Ana County reflects the tightly integrated El Paso-Las Cruces borderplex rather than a long-distance move in most cases — worth keeping in mind when interpreting "brain drain" as people leaving the region entirely versus a single cross-metro commute.

## Why the pattern might exist: hypotheses tested against data

Six hypotheses for El Paso's 25-30 pull were tested directly using ACS 1-Year PUMS microdata (recent movers into each county, identified via a non-blank `MIGSP`, crossed against `POBP`, `NATIVITY`, `CIT`, `COW`, `MIL`, `OCCP`, `INDP`, `MAR`/`MARHYP`, and `PINCP`). Three came back with a clear, distinctive signal for El Paso; three did not — both results are worth reporting.

**Confirmed: government and military employment.** Compared to the average of the other five counties' 25-30 in-migrants, El Paso's 25-30 in-migrants are:
- **5.2x** more likely to be federal government employees (11.1% vs. a 2.2% peer average)
- **12.2x** more likely to be active-duty military (6.6% vs. 0.5%)
- **2.2x** more likely to work in protective-service occupations specifically — police, border/security-adjacent roles (3.5% vs. 1.6%)

This elevation shows up in El Paso's 18-24 movers too (9.4% federal, 6.9% active-duty), just slightly less than in 25-30 — so government/military presence isn't a mechanism unique to the older cohort, it's a structural feature of El Paso's economy at every age tested. It complements, rather than fully explains, the 18-24 shortfall (that's more the UTEP story below), while it goes a long way toward explaining why the 25-30 cohort holds up as well as it does.

Worth noting: El Paso's 25-30 movers also have the **lowest mean personal income of all six counties** ($24,338 vs. a $34,638 peer average, roughly 30% lower). This is consistent with government/military jobs providing stability and a clear reason to relocate rather than a high-paying career leap — and likely also reflects El Paso's lower regional wage levels generally, not something distinctive about who specifically moves there.

**Modest support: boomerang migration.** El Paso's 25-30 in-migrants are 41.3% Texas-born, versus a 37.6% average among the other five counties (a 1.1x ratio) — a real but modest signal, and Bexar County actually edges out El Paso on this specific measure (44.6%). Every county's Texas-born share among movers drops between the 18-24 and 25-30 cohorts, so this isn't a pattern unique to El Paso; El Paso is simply somewhat above the middle of the pack on it.

**Not supported: cross-border/binational household formation.** The hypothesis that El Paso's proximity to Mexico specifically drives its 25-30 inflow did not hold up: El Paso's 25-30 movers are 14.2% born in Mexico (Dallas is actually higher, at 14.8%) and only 16.3% foreign-born overall — the **lowest** foreign-born share of all six counties except Bexar. Whatever is pulling people to El Paso at 25-30, it isn't disproportionately a cross-border/immigration story relative to peer metros.

**Not supported: healthcare-sector recruitment.** El Paso's 25-30 movers are 9.2% healthcare-industry workers, squarely in line with the peer range (7.7%-10.4%). No distinctive signal here.

**Not supported: recent marriage / joining a partner.** El Paso's 25-30 movers are recently married at 11.6%, again in line with peers (10.5%-12.4%). No distinctive signal.

## Why the 18-24 pull is weak: a testable hypothesis

The 18-24 shortfall lines up with a concrete, independently-documented fact about El Paso's largest university. **UTEP (University of Texas at El Paso) is 98% commuter, and 83% of its students are from El Paso County itself** — only 2.37% of students come from out of state (College Factual / CollegeSimply, 2025). Every other county in this comparison has at least one university system that actively recruits and enrolls students from outside its own metro (UT-Austin, Texas A&M/UT-Arlington for Dallas-Fort Worth, UTSA, University of Houston). Freshman enrollment is a major driver of 18-24 in-migration everywhere else — El Paso's largest school mostly doesn't provide that pull, since it's predominantly educating people who already live there rather than importing them.

This doesn't explain the whole gap on its own (entry-level job market composition and general lifestyle/amenity draw for a footloose 18-24 crowd likely play a role too), but it's the piece of this analysis grounded in an external, checkable fact rather than inference from the migration numbers alone.

Sources: [College Factual, UTEP Diversity & Demographics](https://www.collegefactual.com/colleges/the-university-of-texas-at-el-paso/student-life/diversity/); [CollegeSimply, UTEP Diversity & Student Demographics](https://www.collegesimply.com/colleges/texas/the-university-of-texas-at-el-paso/students/)

## Files in this analysis

- `texas_cities_brain_drain_trend.csv` — cohort population by county/year (PUMS)
- `texas_out_migration_totals.csv` — total movers by county/year/cohort (Migration Flows)
- `texas_out_migration_destinations.csv` — full destination-level detail (Migration Flows)
- `texas_boomerang_and_government_pull.csv` — recent-mover composition by county/year/cohort: nativity, citizenship, employer type, occupation, industry, marital status, income (PUMS)
- `brain_drain_trend_fixed.py`, `texas_out_migration.py`, `texas_boomerang_and_government_pull.py` — the scripts that produced the above

## Caveats worth keeping in mind

- The mover-composition analysis (federal employment, military, boomerang migration, etc.) identifies "recent movers" using `MIGSP` (state lived in one year ago), which gives state-level origin only, not county-level. It can tell you someone moved from Arizona into El Paso, not which Arizona county. That's sufficient for the hypotheses tested here, but don't extend it to county-level origin claims.
- The `OCCP` (3700-3960 for protective service) and `INDP` (7970-8290 for healthcare) code ranges come from Census's own documentation, but these codes shift slightly between vintage years (a few codes get added, split, or retired). If a specific year's healthcare or protective-service percentage looks like an outlier, check that year's code list before treating it as a real trend break.
- The in/out ratio comparison is based on only two data points (2010, 2015 vintages) — a real, structural limit of the Census product, not a sampling choice made here. Treat the direction of the finding as solid (it replicates across both years and both cohorts tested) but don't over-read precision into any single ratio.
- "25-30" is reported here as "25-29" — Census's age bins in the Migration Flows product don't have a bucket ending at 30 (25-29 and 30-34 are the two adjacent bins), so 25-29 was used as the closer match rather than approximating with a broader 25-34 band.
- Population figures for years 2012-2021 vs. 2022-2023 rely on two different, independently-verified PUMA crosswalks (2010 and 2020 vintages) — see the trend script's inline documentation for details on how each was verified.
