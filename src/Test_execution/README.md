# Test Execution Harness

Docker-based harness for executing generated tests against SWE-bench-style instances. It runs each generated test on the pre-fix and post-fix versions of the repository and reports whether the test fails before the fix and passes after it.

## Setup

Install Docker, then install the harness from this directory:

```bash
pip install -e .
```

## Usage

```bash
python -m tddbench.harness.run_evaluation \
    --dataset_name <DATASET_JSON> \
    --test_patch <GENERATED_TESTS_JSON> \
    --max_workers 1 \
    --instance_ids <INSTANCE_ID>
```

Logs and results are written under `logs/run_evaluation/`.
