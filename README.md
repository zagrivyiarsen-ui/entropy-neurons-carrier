# Causal but Not Carrier

Do canonical entropy neurons carry the variance of the output entropy they
causally influence? Across 13 models in 7 architecture families they
influence it everywhere and carry it only in the GPT-2 lineage.

**[paper.pdf](paper.pdf)**

## Status

Solo, unpublished, no lab and no supervisor. The paper is finished; the
repository is not. Code is released as it was written, has not been cleaned
up or re-run end to end since the results were produced. Every number in the
paper comes from the runs recorded under `results/`.

## What it finds

- Output entropy is linearly decodable from the final-MLP input in every
  model that has entropy neurons (held-out R² = 0.55–0.74).
- The canonical entropy neurons causally move it wherever they exist, but
  linearly carry its variance only in the GPT-2 lineage.
- Positive controls rule out the deflationary reading: a
  correlation-with-entropy probe recovers entropy in exactly the families
  where the entropy-neuron probe is at chance.
- Exactly one of nine further architectures (GPT-Neo-125M) has a low-rank,
  entropy-neuron-orthogonal causal carrier — and it does not survive scaling
  to GPT-Neo-1.3B, an identical architecture and corpus.

## One correction worth flagging

An earlier version of this analysis reported R² ≈ 0.40 for Pythia-1.4B and
read it as carrying that emerges with scale. It did not survive disjoint
selection and evaluation pools with leak-free bootstrapping: it fell to
0.105. The worst-seed rule in the paper exists because of that correction,
and both the deprecated and the corrected statistic are reported (Table 5).

## Setup

Models and data are public (TransformerLens / HuggingFace,
`NeelNanda/pile-10k`). Single Tesla T4, ≈25 GPU-hours. Hyperparameters and
environment are in Table 3 of the paper; data hash `da5f6242b06fa229`.

Arsenii Zahrivyi — zahrivyi@uni-wuppertal.de, University of Wuppertal
