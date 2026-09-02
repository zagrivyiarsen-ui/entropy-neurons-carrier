# Causal but Not Carrier

Do canonical entropy neurons carry the variance of the output entropy they
causally influence? Across 13 models in 7 architecture families they
influence it everywhere and carry it only in the GPT-2 lineage.

Entropy neurons are read as a confidence-regulation mechanism because
intervening on them moves output entropy. That inference skips a step: a
component can set a quantity under intervention while accounting for little
of its variation on natural text. This work reproduces the causal result of
Stolfo et al. (2024) and adds the carrier measurement that paper does not
make.

**[paper.pdf](paper.pdf)**

## Status

Solo, unpublished, no lab and no supervisor. This repository holds the paper.
The code that produced the results has not been cleaned up or re-run end to
end since; every number in the paper comes from the runs recorded at the
time, under data hash `da5f6242b06fa229`.

## What it finds

- Output entropy is linearly decodable from the final-MLP input in every
  model that has entropy neurons (held-out R² = 0.55–0.74).
- The canonical entropy neurons causally move it wherever they exist, but
  linearly carry its variance only in the GPT-2 lineage — established by a
  paired bootstrap against a random-neuron baseline under a worst-seed rule.
- Positive controls rule out the deflationary reading: a
  correlation-with-entropy probe recovers entropy in exactly the families
  where the entropy-neuron probe sits at the random baseline, so the absence
  is specific to these neurons rather than a limit of linear probing.
- Exactly one of nine further architectures (GPT-Neo-125M) has a low-rank,
  entropy-neuron-orthogonal, bidirectionally controllable causal carrier that
  beats a matched PCA subspace at every tested rank — and it does not survive
  scaling to GPT-Neo-1.3B, identical in architecture and pretraining corpus.

## One correction worth flagging

An earlier version of this analysis reported R² ≈ 0.40 for Pythia-1.4B and
read it as carrying that emerges with scale. It did not survive disjoint
selection and evaluation pools with leak-free bootstrapping: it fell to
0.105. The worst-seed rule in the paper exists because of that correction,
and both the deprecated and the corrected statistic are reported side by side
(Table 5) so the before-and-after is auditable.

## Setup

Models and data are public (TransformerLens / HuggingFace,
`NeelNanda/pile-10k`). Single Tesla T4, ≈25 GPU-hours. Hyperparameters and
environment are in Table 3 of the paper.

Arsenii Zahrivyi — zahrivyi@uni-wuppertal.de, University of Wuppertal
