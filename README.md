# Data-derived positive-screen rules for lung cancer screening CT (NLST)

Code for the study "Data-Derived Positive-Screen Rules for Lung Cancer Screening with Low-Dose Computed Tomography: A
Retrospective Analysis of the National Lung Screening Trial". Readable positive-screen rules in Lung-RADS format are searched on the screens of one
NLST network (genetic programming with NSGA-II and MAP-Elites, a greedy rule learner, re-tuned Lung-RADS thresholds and
random search), selected by a fixed rule and tested against Lung-RADS v2022 on the other network.

## Data

The open NLST clinical tables of the NCI Imaging Data Commons (release v25, CC BY 4.0) are downloaded with

    python scripts/fetch_data.py        # writes data/raw/idc_v25/ and MANIFEST.json with SHA-256 checksums

No account is needed. The data are not redistributed here.

## Reproducing the results

    pip install -r requirements-lock.txt  # Python 3.12; exact versions used
    python -m pytest tests              # implementation check against Pinsky et al. (2015) and correctness tests
    bash scripts/run_jobs.sh runs/jobs_cpu.txt 8     # all 186 search runs (3 development sets x arms x seeds)
    python scripts/evaluate.py          # selection, test-network evaluation -> results/
    python scripts/protocol_analyses.py # further secondary and sensitivity analyses -> results/protocol.json
    python scripts/examples.py          # examples of discordant test screens -> results/examples.json
    python paper/make_results.py        # tables and figures of the paper
    python paper/make_figures.py
    python paper/make_supplement.py
    LUNA25_DIR=/path/to/luna25 python paper/make_fig_cases.py  # optional: CT figure; LUNA25 (Zenodo 10.5281/zenodo.14223624) is not included

The searches are seeded and deterministic; on one CPU core a genetic-programming run takes a few seconds and the
greedy rule learner under a minute. `scripts/evaluate.py` refuses to run until every run of the candidate pool exists.

## Layout

    src/nlst_rules/data.py        screens, nodules and outcome attribution from the NLST tables
    src/nlst_rules/rules.py       rule grammar, validity checks, vectorized evaluation, rendering as a table
    src/nlst_rules/gp.py          random rules, crossover and mutation (genetic programming)
    src/nlst_rules/search.py      NSGA-II, MAP-Elites and random search under a budget of distinct rule evaluations
    src/nlst_rules/baselines.py   re-tuned Lung-RADS (exhaustive) and the greedy rule learner
    src/nlst_rules/evaluate.py    selection rule, paired comparisons and confidence intervals
    rules/comparators/            Lung-RADS v1.0 and v2022 written in the rule grammar
    scripts/                      search runs, evaluation and secondary analyses
    paper/                        scripts that build the tables and figures from results/
    results/                      results reported in the paper

## License

The code is released under the MIT License (see LICENSE). The NLST tables are not part of this package and remain under their own license (CC BY 4.0).
