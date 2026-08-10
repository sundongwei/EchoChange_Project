# EchoChange: Public-Model Correction Baselines

## 1. Models

This evaluation compares two publicly available vision-language models under the same correction protocol:

- Qwen2-VL-7B
- InternVL3-8B

The experiment measures error detection, factual correction, non-target preservation, behavior on clean inputs, and masked-span recovery.

## 2. Evaluation scale

| Split | Samples per model | Purpose |
|---|---:|---|
| Clean control | 892 | Retention of already correct captions and over-correction |
| Single error | 2,291 | Correction of one injected factual error |
| Multi error | 553 | Joint correction of two injected factual errors |
| Recovery test | 892 | Reconstruction of a masked factual span |
| **Total per model** | **4,628** | Complete evaluation scale |

The correction splits contain 3,397 injected error points in total.

## 3. Metric definitions

### 3.1 Correction

- **Strict correction hit:** every injected error in a sample is restored to the exact original value, and the injected wrong value is absent.
- **Semantic correction hit:** the same criterion after a limited synonym normalization.
- **Error precision / recall / F1:** error-point-level measures that count successful target corrections, missed target corrections, and additional edits.

### 3.2 Preservation

- **Non-target preservation:** after replacing target error locations with a shared placeholder, all remaining normalized semantic tokens match the reference.
- **CCSR:** the sample simultaneously satisfies semantic correction hit and non-target preservation.
- **Clean Keep:** a clean input is retained exactly after normalization.
- **Over-correction:** a clean input is edited without necessity.

### 3.3 Detection

Detection evaluates only whether the model chooses to edit. It does not determine whether the edit is correct. A generated caption different from the input is labeled `EDIT`; an identical caption is labeled `KEEP`.

## 4. Aggregate results

| Metric | Qwen2-VL-7B | InternVL3-8B | Preferred direction |
|---|---:|---:|---|
| Strict correction hit | 9.46% | **28.02%** | Higher |
| Semantic correction hit | 9.70% | **28.59%** | Higher |
| Non-target preservation | **29.18%** | 9.56% | Higher |
| CCSR | 2.46% | **5.91%** | Higher |
| Clean Keep | **28.81%** | 7.17% | Higher |
| Over-correction | **71.19%** | 92.83% | Lower |
| Error precision | 2.80% | **7.12%** | Higher |
| Error recall | 11.45% | **32.91%** | Higher |
| Error F1 | 4.49% | **11.70%** | Higher |
| Detection precision | 74.90% | **77.28%** | Higher |
| Detection recall | 66.63% | **99.05%** | Higher |
| Detection F1 | 70.52% | **86.82%** | Higher |

## 5. Single-error and multi-error behavior

| Metric | Qwen single | Qwen multi | InternVL single | InternVL multi |
|---|---:|---:|---:|---:|
| Strict correction hit | 11.57% | 0.72% | **31.91%** | **11.93%** |
| Semantic correction hit | 11.83% | 0.90% | **32.61%** | **11.93%** |
| Non-target preservation | **29.20%** | **29.11%** | 9.25% | 10.85% |
| CCSR | 3.06% | 0.00% | **6.77%** | **2.35%** |
| Error F1 | 3.94% | 5.65% | **9.86%** | **15.51%** |
| Detection F1 | 68.29% | 48.28% | **84.18%** | **56.97%** |

Both models degrade on samples containing two simultaneous factual errors. InternVL3-8B maintains higher correction recall, while Qwen2-VL-7B remains substantially more conservative outside the target positions.

## 6. Main observations

1. **Detection and correction are distinct.** InternVL3-8B detects nearly all erroneous inputs, but its aggregate semantic correction hit remains 28.59%.
2. **Aggressive editing reduces preservation.** InternVL3-8B corrects more target errors but preserves non-target content in only 9.56% of error samples.
3. **Qwen2-VL-7B is more conservative but misses more errors.** It leads on non-target preservation and Clean Keep, while its correction recall is substantially lower.
4. **Neither model resolves the correction–preservation trade-off.** CCSR remains below 6% for both baselines.

## 7. Limitations

- Results are specific to this injected-error protocol and normalization procedure.
- Exact preservation is intentionally strict and may penalize harmless paraphrases.
- Detection metrics measure the decision to edit, not the factual quality of the resulting edit.
- No confidence intervals or significance tests are reported in this snapshot.
- Public baselines differ in architecture and pretraining; this comparison characterizes behavior rather than isolating a single causal factor.
