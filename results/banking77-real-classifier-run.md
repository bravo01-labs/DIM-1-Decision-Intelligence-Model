# Real-classifier run on Banking77 (reported)

This document records a run of the governance pattern on top of a real, fine-tuned text
classifier. It is a **reported** run: it was executed by Bravo01 Labs on a Google Colab T4 in
September 2026, and the output below is copied from that run. The executed notebook is not
part of this repository, so unlike the numeric-domain notebooks this result cannot be re-run
from here.

## Setup

- **Dataset:** [Banking77](https://huggingface.co/datasets/PolyAI/banking77), a public dataset of
  13,083 customer-service queries across 77 intents (CC-BY-4.0).
- **Classifier:** ModernBERT-base, fine-tuned for 5 epochs.
- **Gate wiring:** the classifier's top-5 predicted intents are passed to the gate as candidates.
  An illustrative policy marks three intents as security-sensitive (`compromised_card`,
  `lost_or_stolen_card`, `lost_or_stolen_phone`); routing to any of them requires the session to hold
  a `handle_security_case` authority. Near-tied top-2 eligible candidates abstain. The notebook uses a
  compact inline version of these rules rather than importing `dim_governance`.

## Results

| Check | Result |
|---|---|
| Classifier accuracy (Banking77 test split) | **92.37%**, typical of fine-tuned encoder classifiers on this dataset |
| Reproducibility (same input, same model, same policy, repeated runs) | PASS, 0.0 difference over 5 runs |
| Most-confused intent pair | `balance_not_updated_after_bank_transfer` <-> `transfer_timing` (9 mistakes) |
| Paraphrase robustness (hand-written variants) | 5/6 |
| Security-sensitive sweep (120 test queries whose true intent is security-sensitive) | gate decision differed between a session with and without authority in **115/120 (95.8%)** |

Run summary as printed:

```
=== SUMMARY ===
Reproducibility:              PASS
Most-confused pair:           balance_not_updated_after_bank_transfer <-> transfer_timing (9 mistakes)
Paraphrase robustness:        5/6
Security-sensitive coverage:  120 real cases, 115 where authority changed the outcome, 95.8% caught by top-1 alone
```

## The 5 exceptions

In 5 of the 120 security-sensitive queries the classifier's top prediction was a non-sensitive
intent, so the top-ranked candidate was already eligible and the gate returned the same result
with and without authority. Full output:

```
=== Authority exceptions: security-sensitive true labels ===
Security-sensitive test examples: 120
Exceptions: 5/120

--- Exception 1 ---
Index:              453
Text:               Is a copy of the police report necessary for completing the report process?
True label:         lost_or_stolen_card
Top-1 prediction:   pending_transfer
No authority:       ALLOW / pending_transfer
With authority:     ALLOW / pending_transfer

--- Exception 2 ---
Index:              476
Text:               I cannot find my credit card.
True label:         lost_or_stolen_card
Top-1 prediction:   card_payment_not_recognised
No authority:       ALLOW / card_payment_not_recognised
With authority:     ALLOW / card_payment_not_recognised

--- Exception 3 ---
Index:              1411
Text:               What do I do if I think my card was improperly used?
True label:         compromised_card
Top-1 prediction:   card_not_working
No authority:       ALLOW / card_not_working
With authority:     ALLOW / card_not_working

--- Exception 4 ---
Index:              1421
Text:               How do I freeze my card using the app?
True label:         compromised_card
Top-1 prediction:   card_linking
No authority:       ALLOW / card_linking
With authority:     ALLOW / card_linking

--- Exception 5 ---
Index:              1425
Text:               How do I freeze my account?
True label:         compromised_card
Top-1 prediction:   terminate_account
No authority:       ALLOW / terminate_account
With authority:     ALLOW / terminate_account
```

## How to read this

- **The 115/120 figure checks that the policy layer engages on real classifier output.** Because the
  policy is written so that sensitive intents require authority, the outcome changes whenever a
  sensitive intent is among the candidates. It is not a measure of detection performance.
- **The gate only governs what the classifier surfaces.** The 5/120 exceptions (4.2%) are
  classification errors that landed on non-sensitive intents. Some are exactly the queries a security
  policy exists for (for example, "I cannot find my credit card"). Improving them is a classifier
  problem, not a policy problem.
- **Not measured:** what the gate resolved to when authority was absent (silent fallthrough to
  another intent versus abstention). See "Open design question" in the top-level README.
- **Scope:** one dataset, one classifier, one illustrative three-intent policy. No comparison to any
  other system is claimed.
