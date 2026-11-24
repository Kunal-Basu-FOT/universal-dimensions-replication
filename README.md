# Universal dimensions of visual representation: LAION-fMRI replication

Code and results for Kunal Basu and Sander van Bree's re:vision replication of
Chen and Bonner, *Universal dimensions of visual representation*.
The original implementation is in `orig_codebase/`; its source commit is recorded
in `ORIG_CODEBASE.txt`, and its MIT license is retained there.

## Question and findings

We tested whether DNN dimensions that recur across models are more similar to
human visual-cortex responses, and whether that relationship generalizes to
held-out images. The full-scale run uses five LAION-fMRI participants, four
model sets, the fixed unique-image PCA fit, and 5,000 permutations. The
relationship is positive in all four model sets. Mean participant Spearman
correlations in the primary analysis are 0.565, 0.588, 0.478, and 0.541 for
the initialization, architecture, task, and untrained sets, respectively.
These values and the original-paper comparison are in
[`results/report/numbers.json`](results/report/numbers.json).

The tau split, five held-out CLIP clusters, and OOD images also show positive
participant-level relationships. The exact per-test results are in
[`results/generalization/report_summary.csv`](results/generalization/report_summary.csv)
and the participant-level tables beside it. These results support the
replication of the association; they do not by themselves identify what a
universal dimension represents.

## Figures

- [Universality and brain similarity](results/figures/figure2_universality_brain_similarity.png)
- [Depth-resolved relationship](results/figures/figure3_depth_resolved_relationship.png)
- [Representational similarity analysis](results/figures/figure5_rsa_original_style.png)
- [Generalization tests](results/figures/generalization_tests.png)
- [Generalization reliability](results/figures/generalization_reliability.png)

The corresponding CSVs and compact null distributions are included under
`results/`. Figures are outputs of the full-scale run, not the earlier local
50-component reference run.

## Verify the published results

Use Python 3.10 on Linux:

```bash
python -m pip install -r requirements.txt
python scripts/check_results.py
```

The quick check verifies the included image IDs and splits, model inventory,
reference summaries, and 504 saved permutation p-values. It does not refit
models. On the packaged results it reports: `Passed: image IDs and splits,
model inventory, reference summaries, and 504 saved permutation p-values.`

## Reproduce the analysis

- `src/` and `scripts/`: input preparation and primary analysis.
- `generalization/`: exact split assignments, reliability estimates, and permutation analysis.
- `supplementary/`: participant statistics, rank-tolerance sensitivity, and report summaries.
- `metadata/`: ordered image IDs and model/layer inventory.
- `results/`: reference tables, figures, and compact generalization null distributions.

Large image, beta, feature, and PCA files are not included. Obtain the
[LAION-fMRI dataset](https://laion-fmri.hebartlab.com/), including the images and
single-trial betas, then run from this directory:

```bash
bash scripts/prepare_inputs.sh /path/to/LAION-fMRI
bash scripts/run_analysis.sh
```

Preparation writes to `data/`; analysis writes to `outputs/`. If using existing
prepared data, set its locations in `config/analysis.json` and skip preparation.
The scripts default to two workers and two numerical threads per worker;
`WORKERS` and `THREADS` can be set in the shell. Full reproduction requires
substantial computation and disk space. Use a fresh output directory when changing
analysis parameters; completed scores are reused.

The primary analysis uses the fixed unique-image PCA fit and 5,000 permutations.
Generalization tests shuffle held-out predictions, with brain similarity fixed;
CLIP-fold correlations and their nulls are averaged before calculating p-values.
The original-tolerance analysis is a supplementary sensitivity check.

The replication scripts use the upstream code only from the pinned commit in
`ORIG_CODEBASE.txt`. Its MIT license applies to that retained directory;
original-work attribution is kept with the source. No raw participant data or
image pixels are committed here.
