# EchoChange: Correction and Generation Ablation Report

## 1. Scope

This report evaluates three questions:

1. Can the model determine whether an input caption contains a factual error?
2. Can it correct the target error while preserving valid non-target content?
3. How do denoising, polishing, and remasking strategy affect generation quality, repetition, and inference cost?

All values are descriptive results from the current evaluation snapshot. They should not be interpreted as statistical significance tests.

## 2. Evaluation data

| Split | Samples | Injected errors | Primary purpose |
|---|---:|---:|---|
| Clean control | 892 | 0 | Measure retention and over-correction on already valid captions |
| Single error | 2,291 | 2,291 | Measure correction of one factual error per caption |
| Multi error | 553 | 1,106 | Measure joint correction of two factual errors per caption |
| All error samples | 2,844 | 3,397 | Aggregate correction evaluation |

Injected errors cover `change_type`, `direction`, `event`, `object`, `quantity`, and `relation`. Correct values, injected values, error types, and token positions are available to the evaluator only.

## 3. Correction results

### 3.1 Clean control

| Metric | Result | Interpretation |
|---|---:|---|
| Clean Keep | 79.26% | Fraction of valid inputs retained exactly after normalization |
| Over-correction | 21.52% | Fraction of valid inputs unnecessarily edited or marked for editing |

The current model is usually conservative on clean inputs, although approximately one fifth of clean captions still trigger an unnecessary edit.

### 3.2 Single-error correction

| Metric | Result |
|---|---:|
| Strict correction hit | 65.34% |
| Semantic correction hit | 65.47% |
| Non-target preservation | 23.26% |
| Conservative correction success rate (CCSR) | 16.15% |
| Error precision | 30.47% |
| Error recall | 65.47% |
| Error F1 | 41.59% |
| Detection recall | 95.72% |

High detection recall indicates that the model usually recognizes when an edit is required. The much lower CCSR shows that identifying an erroneous caption is substantially easier than correcting only the target fact without modifying valid context.

### 3.3 Multi-error correction

| Metric | Result |
|---|---:|
| Strict correction hit | 41.23% |
| Semantic correction hit | 41.59% |
| Non-target preservation | 18.08% |
| CCSR | 9.04% |
| Error precision | 42.67% |
| Error recall | 64.20% |
| Error F1 | 51.26% |
| Detection recall | 98.92% |

Jointly correcting two errors is more difficult than correcting one. Detection remains strong, while successful conservative editing decreases to 9.04%.

### 3.4 Performance by error type

| Error type | Error count | Semantic recall |
|---|---:|---:|
| Change type | 543 | 21.55% |
| Direction | 82 | 52.44% |
| Event | 581 | 81.41% |
| Object | 1,763 | 67.56% |
| Quantity | 87 | 67.82% |
| Relation | 341 | 95.89% |

Relation and event errors are recovered most reliably in this snapshot. Change-type errors remain the weakest category.

## 4. Recovery evaluation

The recovery task replaces a factual span in a correct caption with `[MASK]`. The model receives T1, T2, and the partially masked caption, but never receives the held-out answer span during inference.

| Metric | Result |
|---|---:|
| Samples | 892 |
| Full-caption token accuracy | 9.78% |
| Unresolved-mask rate | 0.00% |
| Length MAE | 7.314 tokens |
| Unigram / bigram / trigram repetition | 10.64% / 0.74% / 0.09% |
| ROUGE-L | 31.765 |
| METEOR | 35.393 |
| ST5-SCS | 84.180 |

All masks are resolved, but exact recovery remains weak. Mask elimination should therefore not be interpreted as factual recovery.

## 5. Generation ablations

| Configuration | Forward passes | ROUGE-L | METEOR | ST5-SCS | Latency | Trigram repetition |
|---|---:|---:|---:|---:|---:|---:|
| Denoise + Polish (4D+4P) | 8 | 24.454 | 25.388 | 73.975 | 1,423 ms | 36.74% |
| Polish only (0D+20P) | 5 | 22.745 | 21.609 | 71.924 | 887 ms | 50.74% |
| Denoise only (16D+0P) | 16 | 25.196 | 28.299 | 75.488 | 2,856 ms | 27.01% |
| Random remasking (16D+4P) | 20 | **26.090** | **33.937** | **80.273** | 3,561 ms | **0.18%** |

### 5.1 Interpretation

- Polish-only inference is weakest on all three external generation metrics and exhibits the highest repetition.
- Sixteen denoising stages improve quality relative to 4D+4P, but double the forward-pass budget.
- Random remasking reports the strongest quality and repetition values, but also uses the largest compute budget.
- Because compute budgets are not matched, the improvement of random remasking cannot be attributed to position selection alone.

## 6. Limitations

- The ablation configurations do not use matched forward-pass budgets.
- Reported aggregates do not include confidence intervals or hypothesis tests.
- Exact-match correction metrics are intentionally strict and may undercount semantically valid paraphrases.
- Current recovery results show that producing a complete surface form is not equivalent to recovering the held-out fact.
- Conservative editing under multiple simultaneous errors remains an open problem.
