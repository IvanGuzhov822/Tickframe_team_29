# Quality Requirement Tests

This document defines the maintained automated quality requirement tests for
Tickframe. Each QRT links a measurable quality requirement to an automated test,
coverage gate, or CI check that can be inspected in normal repository evidence.

## QRT-001: Market data update latency

**Linked quality requirement:** [QR-001](quality-requirements.md#qr-001-market-data-update-latency)

**Verification method:** Automated backend unit/integration tests and CI
coverage run.

**Test data, setup, or environment:** In-memory trade and candle fixtures under
the standard GitHub Actions `Quality` workflow environment.

**Automated command or CI check:**

```bash
coverage run --source=backend.app,ml.pattern_recognition -m unittest discover -s backend/tests
```

Relevant tests:

- [`backend/tests/test_observability.py`](../backend/tests/test_observability.py)
- [`backend/tests/test_service_history.py`](../backend/tests/test_service_history.py)

**Expected measurable result:** Latency samples are exported through the
observability snapshot and Prometheus output with deterministic p95 values, and
stable chart snapshots respect the configured delay window used for latency
control.

**Evidence link:** [Quality workflow](https://github.com/Team-29-TickFrame/Tickframe_team_29/actions/workflows/quality.yml)
and the latest passing `Backend lint, tests, coverage` job for the protected
default branch.

## QRT-002: Exchange data failure visibility

**Linked quality requirement:** [QR-002](quality-requirements.md#qr-002-exchange-data-failure-visibility)

**Verification method:** Automated backend unit tests.

**Test data, setup, or environment:** Deterministic exchange/store fixtures
under the standard GitHub Actions `Quality` workflow environment.

**Automated command or CI check:**

```bash
coverage run --source=backend.app,ml.pattern_recognition -m unittest discover -s backend/tests
```

Relevant tests:

- [`backend/tests/test_aggregation.py`](../backend/tests/test_aggregation.py)
- [`backend/tests/test_exchange_parsers.py`](../backend/tests/test_exchange_parsers.py)

**Expected measurable result:** Disconnect gaps are not hidden as normal
price-continuation data, exchange trade timestamps are normalized with latency
metadata, and the health/status path remains available for users to distinguish
fresh and stale data states.

**Evidence link:** [Quality workflow](https://github.com/Team-29-TickFrame/Tickframe_team_29/actions/workflows/quality.yml)
and the latest passing `Backend lint, tests, coverage` job for the protected
default branch.

## QRT-003: Critical module test coverage

**Linked quality requirement:** [QR-003](quality-requirements.md#qr-003-critical-module-test-coverage)

**Verification method:** Automated coverage report plus critical-module
coverage gate.

**Test data, setup, or environment:** Standard GitHub Actions `Quality`
workflow environment with backend and ML test dependencies installed.

**Automated command or CI check:**

```bash
coverage json -o coverage.json
python backend/scripts/check_critical_coverage.py coverage.json
```

Relevant implementation:

- [`backend/scripts/check_critical_coverage.py`](../backend/scripts/check_critical_coverage.py)
- [`docs/testing.md`](testing.md#critical-modules-and-coverage-status)

**Expected measurable result:** Every critical module listed in
`backend/scripts/check_critical_coverage.py` is present in `coverage.json` and
has at least 30% automated line coverage; the CI job fails if any listed module
falls below the threshold.

**Evidence link:** [Quality workflow](https://github.com/Team-29-TickFrame/Tickframe_team_29/actions/workflows/quality.yml)
and the uploaded `backend-coverage` artifact from the latest passing protected
default-branch run.
