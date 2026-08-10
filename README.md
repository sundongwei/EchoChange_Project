# EchoChange

Official project page for **EchoChange: A Diffusion Language Model with Dual-Pass Remasking for Factual Remote Sensing Disaster Change Captioning**.

## Paper

- **arXiv:** https://arxiv.org/abs/2608.01856
- **Authors:** Dongwei Sun\*, Bowen Yao\*, Yujie Zhang, Pei Liu, Jing Yao, Xiangyong Cao
- \* Equal contribution.

## Abstract

Bi-temporal remote-sensing disaster change captioning requires identifying sparse and spatially localized changes across large pre- and post-event scenes and converting them into factual descriptions. Existing methods use autoregressive decoding, where an early misinterpretation of the changed object becomes an irreversible premise for subsequent text, amplifying errors.

EchoChange is a multimodal discrete diffusion language model that reframes change captioning as iterative masked-token denoising rather than left-to-right generation. This allows the model to reconsider uncertain content and correct imperfect intermediate predictions by repeatedly revising captions conditioned on image pairs. The paper also introduces draft-aware dual-pass training, a progressive masking curriculum, and confidence-guided remasking, which align training with iterative inference. Experiments on the RSCC benchmark show EchoChange substantially outperforms both general-purpose and remote-sensing-specific baselines across lexical and semantic metrics.

## Contents

- `site/` - Interactive research showcase (web app)
- `Open EchoChange.html` - Self-contained offline showcase (open directly in a browser)
- `cite.bib` - BibTeX citation entry

## Viewing the Showcase

### Online

Visit the GitHub Pages site: **[sundongwei.github.io/EchoChange_Project](https://sundongwei.github.io/EchoChange_Project/)**

Source code: **[github.com/sundongwei/EchoChange_Project](https://github.com/sundongwei/EchoChange_Project)**

### Offline

1. Download and extract the latest release ZIP.
2. Double-click `Open EchoChange.html`.
3. Navigate with the section header, bottom controls, arrow keys, mouse wheel, or touch gestures.

## Citation

```bibtex
@article{sun2026echochange,
  title     = {EchoChange: A Diffusion Language Model with Dual Pass Remasking for Factual Remote Sensing Disaster Change Captioning},
  author    = {Sun, Dongwei and Yao, Bowen and Zhang, Yujie and Liu, Pei and Yao, Jing and Cao, Xiangyong},
  journal   = {arXiv preprint arXiv:2608.01856},
  year      = {2026}
}
```
