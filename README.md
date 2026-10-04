# SWE-PBT: Property-Based Testing for Bug Reproduction

This repository implements a pipeline for generating property-based tests for bug-fix tasks in SWE-bench-style repositories. The workflow is built around LLM-guided analysis of a benchmark instance: it localizes the likely bug site, creates a test plan, generates a property-based test, and produces a patch that can be validated against the repository before and after the fix.

## What this project does

The main flow is:

- localize the relevant files and functions
- draft an initial testing plan
- generate a reproduction or regression property-based test
- export the generated patch as a git diff
- run the bundled evaluation harness to check whether the test fails before the fix and passes after it

The core pipeline entry point is `src/potter.py`.

## Repository layout

- `data/`: experiment result tables for the reported research questions
- `src/`: implementation of the generation pipeline
  - `potter.py`: main runner for a single instance (generates initial property-based tests)
  - `localization.py`: file/function localization
  - `init_plan.py`: planning step for the test
  - `generate.py`: final property-based test generation
  - `generate_git_diff.py`: produce the patch diff
  - `potter_batch.sh`: parallel runner over many instances for `potter.py`.
  - `epotter.py`: script for execution based refinement of property-based tests
  - `epotter_batch.sh`: parallel runner over many instances for `epotter.py`.
- `src/Test_execution/`: Docker-based evaluation harness used to execute generated tests against benchmark repositories

## Quick start

From the repository root:

```bash
cd src
python potter.py \
  --dataset_name TDD_Bench.json \
  --instance_id <instance_id> \
  --model claude \
  --pbt_mode combined \
  --output_file potter_test.json
```

This will:

1. load the benchmark instance
2. clone or reuse the target repository
3. preprocess the data
4. run localization, planning, and test generation
5. save the resulting patch JSON to the output file

## Evaluation

The repository includes a harness for running generated tests against each bug instance:

```bash
cd src/Test_execution
pip install -e .
python -m tddbench.harness.run_evaluation \
  --dataset_name <DATASET_JSON> \
  --test_patch <GENERATED_TESTS_JSON> \
  --max_workers 1 \
  --instance_ids <INSTANCE_ID>
```

This evaluates whether the generated property-based test behaves as expected on the buggy and fixed code versions.
