# Lab 9 bonus: Inspect AI grader port

This optional bonus ports three deterministic code graders from
`lab9.graders` to [Inspect AI](https://inspect.aisi.org.uk/) and compares their
decisions against `pydantic-evals` on the same recorded swarm runs.

## Run it

From the repository's `src/` directory:

```powershell
python -m pip install -r requirements-bonus.txt
python -m lab9.inspect_bonus
```

The runner uses the existing v2 eval set, configured five seeds, and `swarm`
shape. It replays recorded trial outputs through Inspect custom scorers; it
makes no LLM calls and never edits Lab 8. The projection passed into Inspect is
allowlisted to scorer-required fields and excludes artifact blobs, secrets, and
full traces.

The generated, machine-readable result is
`src/reports/bonus_inspect_comparison.json`. Do not edit it by hand; rerun the
command to regenerate it.

## Measured comparison

Source: generated report for eval-set SHA `aabcd5f6d8a9`, run
`E-swarm-20261117-aabc`.

| Grader | Trials | Pydantic true | Inspect true | Agreement |
|---|---:|---:|---:|---:|
| `approval_bypass` | 140 | 0 | 0 | 140/140 (100%) |
| `refund_amount_correct` | 140 | 137 | 137 | 140/140 (100%) |
| `reply_ledger_match` | 140 | 140 | 140 | 140/140 (100%) |
| **Total decisions** | **420** | — | — | **420/420 (100%)** |

The safety grader's native polarity is violation-positive in Pydantic
(`true` means a bypass was detected). Inspect scores use `C` for a clean result
and `I` for a detected violation; the comparison normalizes this polarity
before counting agreement. The 100% figure is observed agreement on this fixed
public eval set and seed set, not evidence that either grader is correct for
all unseen cases.

## What was ported

- `ApprovalBypass`: valid-approval label, denial status, and unexpected ledger
  rows.
- `RefundAmountCorrect`: expected ledger amount and, where labeled, proposal
  amount.
- `ReplyLedgerMatch`: every monetary amount in reply events versus ledger
  total, including the supplied no-reply behavior.

The implementation is `src/lab9/inspect_bonus.py`; the core Lab 9 dependency
list remains unchanged. Install the optional dependency only when running this
bonus.
