---
layout: post
read_time: true
show_date: true
title: "Daily Engineering Tech Brief — 2026-11-27"
date: 2026-11-27
img: posts/20260114/quantum-computing.jpg
tags: [daily brief, quantum computing, VQE, HI-VQE, uncertainty quantification, active learning, Kriging, surrogate model, FEM, modal analysis, physics-informed]
categories: [daily brief]
author: Hoechang Ryu
description: "Quantum chemistry, UQ/reliability, AI/ML for engineering and CAE: verified updates, ratings, and research relevance."
mathjax: yes
toc: yes
---

**Edition note.** This is the baseline edition: no earlier briefing was found in the wiki or on the blog, so every item is **new**. All arXiv items are **preprints, not peer reviewed**. Facts come from arXiv abstract pages or the cited press release; full texts were not read, and "Interpretation" paragraphs are mine.

## Since last week
| Item | Status |
|---|---|
| SSPT for CS-VQE (arXiv:2611.22108) | new (v2 on 2026-11-23) |
| First-quantised chemistry (arXiv:2611.24885) | new |
| IBM/ORNL/AMD QEC platform | new |
| Inner optimizers in AK reliability (arXiv:2611.03117) | new |
| Modal Rational neural operators (arXiv:2611.07059) | new |
| Energy-norm FE surrogates (arXiv:2611.14574) | new |

## 1. Quantum computing and quantum chemistry

### 1.1 Stabilizer-Subspace Perturbation Theory for CS-VQE
**What happened.** Preprint, online 2026-11-17, revised 2026-11-23 (v2). DOI [10.48550/arXiv.2611.22108](https://doi.org/10.48550/arXiv.2611.22108); [source](https://arxiv.org/abs/2611.22108). Authors Yao, Liu, Li.
- *Verified:* stabilizer projection in CS-VQE drops Hamiltonian couplings to configurations outside the retained subspace. SSPT treats the removed Pauli terms as perturbations on the optimized reference, using an Epstein–Nesbet-type second-order correction or a resolvent formulation. The factorized reference lets matrix elements come from Pauli expectation values on the reduced register.
- *Author-reported:* large reductions in projection error; chemical precision with fewer contextual qubits in several cases.

**Why it matters (interpretation).** The generic EN form is $E^{(2)}=\sum_k |\langle k|V|\psi_0\rangle|^2/(E_0-\langle k|H|k\rangle)$ with $V$ the removed terms (paper's exact expressions not verified). HI-VQE builds a core space and diagonalizes classically, as selected-CI does; a PT2-type correction for out-of-core coupling is the standard SCI companion, so SSPT is the closest conceptual parallel to adding a post-processing correction to an HI-VQE-gPCE subspace. Measurement cost (Pauli expectations on a reduced register) is the number to compare against HI-VQE sampling cost.

**What to watch.** Full-text benchmarks, resolvent stability near small denominators, measurement scaling, peer-reviewed version.

**Ratings.** Importance: **Medium** (incremental method, preprint). Relevance: **High** (subspace selection + classical post-processing).

### 1.2 First-quantised chemistry with a sum-of-squares representation
**What happened.** Preprint, 2026-11-18. DOI [10.48550/arXiv.2611.24885](https://doi.org/10.48550/arXiv.2611.24885); [source](https://arxiv.org/abs/2611.24885).
- *Author-reported (resource estimates, not hardware):* on six molecules (4–24 electrons, orbitals 5–14× electrons), up to 16× fewer Toffoli gates than second-quantised in the few-hundred-logical-qubit regime; advantage up to roughly 1,400 qubits; minimum qubits 1.4–2× lower.

**Why it matters (interpretation).** Fault-tolerant, phase-estimation based; it sets the long-term benchmark that hybrid selected-subspace methods like HI-VQE must be positioned against, not a near-term method.

**What to watch.** Independent compilation checks; peer review.

**Ratings.** Importance: **Medium**. Relevance: **Low** (not NISQ/VQE).

### 1.3 IBM / ORNL / AMD quantum error-correction co-design platform
**What happened.** Official press release, 2026-11-05 ([HPCwire wire copy](https://hpcwire.com/off-the-wire/ornl-ibm-and-amd-launch-new-co-design-platform-for-quantum-error-correction); canonical IBM/ORNL pages not checked). No DOI.
- *Verified (as stated):* IBM Quantum Nighthawk (120 qubits) paired with ORNL's Umbra (AMD Instinct MI355X, EPYC); planned work on concatenated and space-time codes and algorithm-aware decoders.
- *Vendor-reported / targets:* IBM's 70-logical-qubit, 438-hard-operation experiments (not results of this platform); DOE target of 100 logical qubits × 100,000 operations by late 2028.

**Why it matters (interpretation).** Infrastructure signal for classical–quantum co-design of decoding; no chemistry or UQ result.

**What to watch.** Decoder latency/throughput data from the platform.

**Ratings.** Importance: **Medium**. Relevance: **Low**.

**Not found:** no sufficiently verified new HI-VQE or quantum-gPCE source this week (latest HI-VQE item known to me, arXiv:2601.06935, is from January 2026 and not new).

## 2. UQ, reliability and design optimization

### 2.1 Inner optimizers in active-learning Kriging reliability analysis
**What happened.** Preprint, 2026-11-03 (v1), single author W.-C. Yeh. DOI [10.48550/arXiv.2611.03117](https://doi.org/10.48550/arXiv.2611.03117); [source](https://arxiv.org/abs/2611.03117).
- *Author-reported:* with surrogate, population and stopping rule fixed, five continuous optimizers did not beat candidate-pool selection (hybrid: 0 wins, 23 ties, 21 losses vs pool methods in 44 comparisons). On three series systems, multistart L-BFGS-B left a failure mode unlearned in 59/60 runs; in all 198 runs with an unlearned mode, $P_f$ was underestimated (labeled exploratory). The error-based stopping rule accepted 17 runs at 0.38–0.89 of the reference.

**Why it matters (interpretation).** AK-MCS style learning functions such as $U(x)=|\mu_G(x)|/\sigma_G(x)$ only help if the search for $\arg\min U$ reaches every failure region; a missed mode gives a confident, unconservative $P_f$ and the surrogate's own error metric cannot see it. For RBDO with several constraints, or tail measures (VaR/CVaR) computed from a surrogate, the same blind spot would bias constraints toward infeasible designs. Whether PCE/DD-GPCE active-learning loops share it is untested here.

**What to watch.** Full-text problem set, replication, coverage diagnostics for PCE-based loops.

**Ratings.** Importance: **Medium** (single-author, partly exploratory). Relevance: **High** (GP/Kriging active learning, failure probability).

**Not found:** no sufficiently verified new work this week on DD-GPCE, score-form vs Stein-form sensitivity, or VaR/CVaR sensitivity.

## 3. AI/ML for engineering

### 3.1 Physics-embedded neural operators for structural dynamics (Modal Rational)
**What happened.** Preprint, online 2026-11-06, v2 2026-11-09. DOI [10.48550/arXiv.2611.07059](https://doi.org/10.48550/arXiv.2611.07059); [source](https://arxiv.org/abs/2611.07059).
- *Author-reported:* MR maps structural features to modal quantities, then reconstructs frequency response analytically; PGMON/MOSAIC add bounded corrections to frequency, damping and residual flexibility. Two 20,000-design populations (80 m truss tower, tapered shell beam); mean displacement-history errors 0.73–0.76% (tower) and 0.029–0.116% (beam) versus band-limited modal references.

**Why it matters (interpretation).** The surrogate keeps the modal form $H(\omega)=\sum_j \phi_j\phi_j^T/(\omega_j^2-\omega^2+2i\zeta\omega_j\omega)$ explicit (generic form, mine), so resonance shifts are predicted through eigenpairs rather than learned as raw curves. Caveats stated by the authors: fixed topology, no established generalization or compute savings; the reference is a modal solution, not full FE.

**What to watch.** Comparison to PCE/GP surrogates of natural frequencies; mode-tracking under design change.

**Ratings.** Importance: **Low–Medium**. Relevance: **Medium–High** (modal/eigenvalue surrogates).

## 4. Engineering simulation, CAE and computational mechanics

### 4.1 Energy-norm training of FE stress surrogates
**What happened.** Preprint, 2026-11-11 (v1). DOI [10.48550/arXiv.2611.14574](https://doi.org/10.48550/arXiv.2611.14574); [source](https://arxiv.org/abs/2611.14574). Cao and Song.
- *Author-reported:* changing only the loss norm cut a 3D transformer's median energy-norm error 7.5× and von Mises error 6.1×; a Ritz projection (needs the stiffness matrix) beat the label-free model except on peak stress; no network transferred to a substantially finer mesh.

**Why it matters (interpretation).** For $K u=f$ and $\Pi(v)=\tfrac12 v^TKv-f^Tv$, $\Pi(\hat u)-\Pi(u)=\tfrac12(\hat u-u)^TK(\hat u-u)$. In the eigenbasis, $\|e\|_K^2=\sum_i\lambda_i c_i^2$ versus $\sum_i c_i^2$ for an L2 displacement loss, so stiff modes are underweighted by L2. Scope is linear elastostatics; extension to contact (a variational inequality) is my extrapolation, not a result.

**What to watch.** Nonlinear/contact extensions, mesh transfer, effect on downstream failure probability.

**Ratings.** Importance: **Medium**. Relevance: **High** (FEM discretization, surrogate error).

**Not found:** no sufficiently verified major vendor or standards update (Siemens Simcenter 2026-07-28 and Ansys 2026 R1 are older).

## Verification summary
| Class | Items |
|---|---|
| Verified (abstract/press-release level) | existence, dates, DOIs, stated methods |
| Author/vendor-reported | all performance numbers above |
| Interpretation | relevance to DD-GPCE, HI-VQE-gPCE, RBDO and FEM links |

Sources: [arXiv:2611.22108](https://arxiv.org/abs/2611.22108), [arXiv:2611.24885](https://arxiv.org/abs/2611.24885), [arXiv:2611.03117](https://arxiv.org/abs/2611.03117), [arXiv:2611.07059](https://arxiv.org/abs/2611.07059), [arXiv:2611.14574](https://arxiv.org/abs/2611.14574), [IBM/ORNL/AMD release](https://hpcwire.com/off-the-wire/ornl-ibm-and-amd-launch-new-co-design-platform-for-quantum-error-correction).
