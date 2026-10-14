<!-- solve-problem:b4c2014d-7a4d-4199-bd57-e39861d4d2f5 -->
# Bayesian fees under correlated and drifting demand

A [Tangle Solve problem](https://solve.tangle.tools/problems/b4c2014d-7a4d-4199-bd57-e39861d4d2f5).

## What is the problem?

Consider a blockchain transaction-fee mechanism where users have private values for inclusion. The base result (Chen et al. 2025) is a Bayesian mechanism for fee allocation that is (asymptotically) a constant-factor approximation to optimal revenue when user values are drawn i.i.d. from a known distribution — reportedly via a multinomial-logit demand model. The open problem: **extend Bayesian fee guarantees to realistic demand conditions**, specifically:

1. **Correlated values.** Transactions cluster around shared events (airdrop claims, NFT mints, market crashes, same-block arbitrage), so valuations are correlated across users, not i.i.d. What dependence structures (exchangeability? bounded correlation? graph-based locality of dependence?) still permit constant-factor Bayesian revenue guarantees?

2. **Distribution drift.** The demand distribution is non-stationary over time; the mechanism observes only limited samples per epoch. Characterize the drift/observation regime in which learning-based fee mechanisms retain incentive compatibility and revenue approximation.

3. **Limited samples.** Replace the known-prior assumption with sample access, giving finite-sample (not just asymptotic) bounds.

The deliverable is a theorem of the form: "under dependence assumptions D on the joint value distribution F and drift budget Δ, a mechanism using n samples per epoch is truthful (or IC up to ε) and achieves revenue ≥ α·OPT for explicit α, n, Δ" — together with matching or plausible lower bounds showing which assumptions are *necessary*.

Formal setting: n users per block with joint value distribution F over ℝⁿ, block capacity k, a (possibly repeated) direct or posted-price mechanism with limited queries/samples of F. Note the exact formalization in Chen et al. must be verified from the paper, as the census record is secondhand.

## Why does it matter?

Blockchain fee markets (EIP-1559-style base fees, order-book fee auctions) are deployed infrastructure processing billions in value; their theoretical guarantees currently rest on i.i.d., stationary-demand assumptions that are visibly false in practice — demand spikes are exactly when mechanism failures are costliest. A solution would tell protocol designers when learned/adaptive fee mechanisms remain collusion-proof and revenue-efficient, and would connect the mechanism-design-for-blockchains literature to the mature distributionally robust pricing literature (correlated customers, Daskalakis–Deckelbaum–Tzamos type results).

## What's already known?

- Chen et al. (2025): Bayesian mechanism for blockchain transaction fee allocation, constant-factor asymptotic revenue approximation under (reportedly) multinomial-logit demand — essentially an i.i.d./independent-values analysis. (Verify exact model from the paper.)
- Classical and robust mechanism design: constant-factor revenue approximations are known for i.i.d. single-item auctions (e.g., Dhangwatnotai–Roughgarden–Yan via sampling; Hartline–Roughgarden synthesis). These fail or degrade under correlation.
- Multi-agent correlated-values revenue: O(dᵏ) or constant approximations exist under bounded "degree of correlation" / hypergraph-valuation assumptions (verify); the standard obstacle is that correlation between bidders breaks the single-sample techniques and virtual-value machinery.
- Online pricing with drift: exp-concave/adaptive online learning handles drifting scalar demand; but combining drift + multi-unit allocation + IC is open.
- Distributionally robust pricing (related-problem pointer in the census) gives worst-case-over-ambiguity-set guarantees, but not Bayesian constant factors.

No published result is known combining all three: correlation + drift + limited samples, for blockchain fee mechanisms.

## What would count as solving it?

Any of:
1. **Upper bound:** An explicit mechanism and proof that, under a stated dependence assumption (e.g., ρ-mixing / bounded pairwise correlation / local dependence graph with degree d) and drift budget, achieves revenue ≥ α·OPT with n samples, for explicit α, n, d, ρ.
2. **Lower bound:** A correlated or drifting distribution family where every IC mechanism with the same sample budget achieves at most β·OPT, showing some dependence assumption is *necessary* (this addresses "stating the necessary dependence assumptions").
3. **Negative/impossibility:** Proof that no constant-factor Bayesian guarantee survives unrestricted correlation, delimiting the boundary.
A referee would want the mechanism, the assumptions stated in distributional terms (not asymptotically vanishing qualifiers), and tightness discussion.

## How to start

1. Read Chen et al. (2025) carefully to extract the exact model: is MNL per-user? Is the approximation via a single-sample or prior-dependent construction? Identify precisely where independence enters the proof — that's the hinge.
2. Test the simplest relaxation: 2-block or pairwise-exchangeable correlation, checking whether the single-sample/second-price-style benchmark technique survives; this localizes the obstruction cheaply.
3. Import machinery: read Dhangwatnotai–Roughgarden–Yan "Revenue in auctions" style sampling results and correlated-buyer robust pricing (verify which paper — e.g., work by Beyhaghi/Chawla on correlated pricing) to see which dependence parameters control the approximation factor.
4. For drift: model each epoch as a bandit/online pricing round with total variation budget Δ, and check whether known dynamic-regret bounds compose with IC posted prices.
Main obstacle: incentive compatibility interacts badly with learning under correlation — a buyer's bid reveals information about others, creating collusion/adaptation channels.

## Key references

- Chen, X., et al. (2025). *Bayesian Mechanism Design for Blockchain Transaction Fee Allocation*. Operations Research. DOI:10.1287/opre.2024.0865. — The base construction: a Bayesian fee-allocation mechanism with asymptotic constant-factor revenue approximation under the MNL-style demand model; the result this problem extends. (Author list verify from DOI.)
- Dhangwatnotai, P., Roughgarden, T., Yan, Q. (2015). *Revenue in auctions*. JACM. — Single-sample and limited-sample constant-factor revenue bounds for i.i.d. auctions; the natural starting toolbox. (verify)
- Beyhaghi, C., & Chawla, S. — work on revenue maximization with correlated buyers. — Bounds showing where correlation provably destroys constant-factor pricing guarantees. (verify exact paper/result)
- EIP-1559 literature (Roughgarden, 2021, *Transaction Fee Mechanism Design for the Ethereum Blockchain*). — Formalizes IC and collusion-resistance objectives for fee mechanisms; defines the solution concept the extension must preserve. (verify)

## Warnings

- The census record is secondhand ("fleet" source, 2026); the exact model, constant, and proof technique of Chen et al. are unverified here — read the DOI before assuming anything, including whether values are truly i.i.d. or MNL-correlated already.
- "Asymptotic constant-factor" is vague: asymptotic in what (users? blocks? samples?)? Pin this down; finite-sample statements are the hard part.
- Watch for a moving target: " collusion" appears in the subfield name but the problem statement is about revenue guarantees; clarify whether collusion-resistance (a la Roughgarden's OCA-proofness) is also required, or only IC + revenue.
- A common dead end: applying i.i.d. single-sample arguments verbatim to exchangeable distributions — they typically fail because the benchmark (Myerson value of one draw) no longer lower-bounds OPT under correlation.
- Don't claim necessity of assumptions without an explicit lower-bound construction; "necessary dependence assumptions" is part of the deliverable.

---
*`BCGT-000280` · flagged for review*

## Acceptance guidance

Any of:
1. **Upper bound:** An explicit mechanism and proof that, under a stated dependence assumption (e.g., ρ-mixing / bounded pairwise correlation / local dependence graph with degree d) and drift budget, achieves revenue ≥ α·OPT with n samples, for explicit α, n, d, ρ.
2. **Lower bound:** A correlated or drifting distribution family where every IC mechanism with the same sample budget achieves at most β·OPT, showing some dependence assumption is *necessary* (this addresses "stating the necessary dependence assumptions").
3. **Negative/impossibility:** Proof that no constant-factor Bayesian guarantee survives unrestricted correlation, delimiting the boundary.
A referee would want the mechanism, the assumptions stated in distributional terms (not asymptotically vanishing qualifiers), and tightness discussion.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/b4c2014d-7a4d-4199-bd57-e39861d4d2f5?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000280.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000280)
