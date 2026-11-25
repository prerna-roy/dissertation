# Prompt-engineering a Large Language Model for Data Extraction from New South Wales Coronial Findings and Recommendations

Code, prompts, outputs and evaluation results for the Master of Health Data Science dissertation of Prerna Roy (z5650460), Centre for Big Data Research in Health, UNSW Sydney, November 2026.

The study extracted 28 variables from 50 published coronial findings of the NSW Coroners Court with an OpenAI reasoning model (`gpt-5.6-luna`), and compared zero-shot, few-shot and chain-of-thought prompting. Prompts were developed on 5 reports, the best prompt of each technique was compared on 5 internal validation reports, and the selected prompt (`Z7_v2`) was run once on 40 external validation reports. The methods and results are described in the dissertation.

## Repository structure

```
code/
  report_selection_final.ipynb        Seeded random dates, eligibility criteria, allocation of reports to the three datasets, worked-example dates, replacement of the withdrawn report
  check_report_links_final.ipynb      Checks that every report link still opens the expected PDF
  prompting_pipeline_final.ipynb      Builds the prompts, calls the API and writes the audit logs
  evaluation_pipeline_final.ipynb     Scores the audit logs against the reference standard and ranks the prompt versions
data/
  report_links.jsonl                  Report number, dataset, file number and URL of every report
  report_dataset_final_v15_clean.xlsx Reference standard (annotated values of the 28 variables) and data dictionary
  random_date_draws_20260927.jsonl              Dates drawn for the study sample (seed 2804, draws 1 to 22)
  random_date_replacement_draws_20261024.jsonl  Replacement and reserve dates for the withdrawn report (seed 2804, draws 23 to 27)
  random_date_fewshot_draws_20261018.jsonl      Dates drawn for the two worked examples (seed 1904)
  report_dataset_assignment_20261012.jsonl      Random allocation of the reports to the three datasets (seed 2804)
  link_check_20261024_1227.csv                  Result of the link check before the study runs
prompts/
  README.md, zero_shot.md, few_shot.md, chain_of_thought.md   Full text of the 20 eligible prompt versions
results/
  prompt_versions.jsonl               Full text of every prompt version
  parameter_check.csv                 Request settings accepted by the model
  final_report_tables_clean.xlsx      Calculation of every value in the tables and figures of the dissertation
  prompt_development/                 audit_log.jsonl, audit_log_first_run.jsonl, prompt_tracker.xlsx, human_review_pd.xlsx
  internal_validation/                audit_log.jsonl, prompt_tracker.xlsx, human_review_iv.xlsx
  external_validation/                audit_log.jsonl, prompt_tracker.xlsx, human_review_ev.xlsx, human_review_ev_disagreements.xlsx
evaluation/
  prompt_development/evaluation_results.xlsx
  internal_validation/evaluation_results.xlsx
  external_validation/evaluation_results.xlsx
```

## Outputs

- **Audit logs** (`results/<stage>/audit_log.jsonl`) hold one record per API call: the exact system and user messages, the report URL, the request settings, the model's answer and the token counts. `results/prompt_development/audit_log_first_run.jsonl` is the first prompt development run (191 calls), which includes the 40 calls on the withdrawn report 15 and the first versions of FS1 and FS2 run with uncorrected worked examples. These calls are kept as a record and are not scored. Together, the audit logs hold all 1,233 calls of the study.
- **Prompt trackers and human review sheets** (`results/<stage>/`) are spreadsheet views of the audit logs for reading the outputs. `human_review_ev_disagreements.xlsx` holds the review of the 49 complete disagreements on the external validation reports.
- **Evaluation workbooks** (`evaluation/<stage>/evaluation_results.xlsx`) hold every score (`scores_long`), the summary tables, the ranking of the prompt versions and the agreement between repeats. The `README` sheet of each workbook records the settings used.

## Reference standard

The reference standard was refined during the study. Each stage was scored against the version that was current at the time:

| Version | Used for | Change from the previous version |
|---|---|---|
| v11 | Prompt development and internal validation | Final version of the prompt development and internal validation reports, after the review of the reference standard described in Appendix F.2 of the dissertation |
| v14 | External validation | Review of the 40 external validation reports before scoring (v12 to v14): 120 cells in 37 external validation reports; no prompt development or internal validation report changed |
| v15_clean | This repository | v14 with the withdrawn report 15 removed; formatting only otherwise |

The values of the prompt development and internal validation reports in `report_dataset_final_v15_clean.xlsx` are identical to v11, and those of the external validation reports are identical to v14, so this file reproduces every score of the study. The `README` sheets of the evaluation workbooks name the version used when each stage was scored.

## Reproducing the analysis

1. Create a Python 3.11 environment and install the packages: `pip install -r requirements.txt`.
2. Copy `.env.example` to `.env` and add an OpenAI API key (needed for the prompting pipeline and the parameter check).
3. Run the notebooks from the `code/` folder; the file paths have been updated to be relative to it.
   - `report_selection_final.ipynb` reproduces the date draws and the allocation (see the note in the notebook on the withdrawn report).
   - `check_report_links_final.ipynb` checks the report links.
   - `prompting_pipeline_final.ipynb` runs the prompts. `ALLOWED_DATASETS` and `DATASET` (Section 3) select the dataset; `TEST_MODE = True` runs a test report only.
   - `evaluation_pipeline_final.ipynb` scores a stage. `STAGE` (Section 12) selects the stage.

The reports are read from the NSW Coroners Court website by URL and are not stored in this repository. A report withdrawn from the website can no longer be run. The model's outputs vary between calls, so a new run will not reproduce the audit logs exactly; the evaluation of the stored audit logs is reproducible.

The test runs used to check the pipeline (`results_test/`) are not included, so the demonstration run of the evaluation notebook (Section 11) requires a new test run first.
