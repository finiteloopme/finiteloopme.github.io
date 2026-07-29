---
layout: post
title: "Bitcoin-Native JIT Liquidation Shielding on Babylon TBV"
published: true
comments: true
categories:
    - technical
tags:
    - bitcoin
    - defi
    - babylon
    - blockchain
---

# Turning Bitcoin's Liquidation Predators Into Borrower Defense

*A system-design writeup for review — including an honest account of what's still unproven.*

Babylon's **Trustless Bitcoin Vaults (TBV)** let you lock native Bitcoin on Bitcoin mainnet and use it as a collateral to borrow on EVM protocols like Aave V4 — with no wrapping, no bridge, and no custodian. It is one of the most important primitives in Bitcoin DeFi. But it inherits DeFi's oldest structural flaw: during a temporary flash crash, opportunistic liquidators seize your collateral the instant your Health Factor dips below 1.00 and extract a 5–10% penalty from your equity — even if the price fully recovers an hour later.

This post proposes a defensive layer built *on top of* TBV that flips that dynamic: instead of paying tens of thousands of dollars to a liquidator who destroys your position, you pay a small fee to a keeper who **saves** it — by injecting a pre-signed, just-in-time collateral top-up *before* the breach. I will walk through the full architecture, and I will be equally clear about the single hardest thing that has to be true for it to work.

> **Open research question — prediction validation.**  
> The mechanism relies on forecasting when a position is about to enter the danger zone, across both a ~15-minute macro horizon and a sub-minute micro horizon. Sub-minute directional prediction of BTC microstructure is **not** an established, out-of-the-box capability. General time-series models (TimesFM, Chronos) and vector-ANN pattern matching are promising, but this needs empirical validation on historical flash-crash data before anyone should believe a precision number.
>
> Critically: when centralized-exchange prices drop but the on-chain oracle has not updated yet, the oracle is *lagging* — which means the liquidators cannot act yet either. So the window where a forecast is both *correct* and *actionable ahead of liquidators* has to be characterized empirically, not assumed.
>
> The design's saving grace is asymmetry: a false-positive top-up is cheap (tens of dollars), while a true positive saves ~$18,600 on a $1M loan. That turns prediction accuracy from a single point of failure into a tunable, risk-managed parameter — a **high-recall / moderate-precision** filter rather than a crystal ball.

With that on the table, here is how the system works.

## 1. What a flash-crash liquidation actually costs

Consider an institutional borrower using a Babylon Trustless Bitcoin Vault:

- **Collateral:** 10.0 BTC at $100,000 = **$1,000,000**
- **Debt:** $750,000 USDC
- **Liquidation threshold:** 85%

```
Health Factor (HF) = (Collateral x Liquidation Threshold) / Debt
HF = ($1,000,000 x 0.85) / $750,000 = 1.133   -> Safe
```

Now a 12-minute flash crash drops BTC from $100,000 to **$88,000** (-12%):

```
HF_crash = ($880,000 x 0.85) / $750,000 = 0.997   -> Underwater
```

Within milliseconds, a liquidator repays 50% of the debt ($375,000) and seizes collateral **plus a 5% penalty bonus**:

```
Collateral seized (USD) = $375,000 x 1.05 = $393,750
Collateral seized (BTC) = $393,750 / $88,000 = 4.474 BTC
```

**The borrower loses $18,750 (0.213 BTC) in pure penalties** — even though BTC recovers to $98,000 an hour later. That penalty is the value this design recaptures.

## 2. The core mechanism: proactive top-ups from a pre-signed backup vault

Instead of reacting *after* HF breaches 1.00, an off-chain prediction engine forecasts when a position is entering the **Danger Zone** (`1.00 < HF <= 1.04`) and triggers a **Just-In-Time collateral top-up** from a secondary TBV — pushing the position back to a safe buffer (`HF ~ 1.08`) before liquidators can act.

```
Layer 1 - Babylon Trustless Bitcoin Vaults (TBV)
   Native BTC locked via pre-signed tx graphs -> mirrored as collateral on Aave V4
        |
        v
Layer 2 - JIT Liquidation Shielding
   Time-series prediction engine + secondary backup TBV on Bitcoin mainnet
        |
        v
Layer 3 - Sovereign protection & keeper yield
   ~$18,600 preserved per successful save | consolidated fee paid to keepers
```

## 3. How the cross-chain top-up actually works (without breaking Bitcoin)

A crucial engineering reality: **Bitcoin Script cannot read, introspect, or verify EVM state** like an Aave Health Factor. And native covenants (CTV/BIP-119, OP_VAULT/BIP-345) are *not* active on Bitcoin mainnet. Any design that claims a Bitcoin UTXO "checks the health factor" is fiction.

TBV solves cross-chain binding the real way — with **pre-signed transaction graphs + Taproot enumerated leaves + emulated covenant committees**. This proposal reuses exactly those primitives.

When opening a backup **TBV Shield Vault** (say, 1.5 BTC), the borrower pre-signs an enumerated tree of spending transactions *before* funding it:

- **Path 1 — Sovereign Self-Recall:** a timelocked (CSV/CLTV) path letting the borrower withdraw their BTC after an unbonding period if no top-up ever fires.
- **Path 2 — Pre-Signed Top-Up Release:** a pre-signed path that redeems a specific slice (e.g., 0.8 BTC) to the designated lending-protocol destination. Because the destination is fixed *at vault-creation time*, the funds physically cannot be redirected — no keeper, relayer, or third party can steal them.

```
BITCOIN MAINNET (TBV collateral)          EVM CHAIN (Aave V4)
+------------------------------+         +---------------------------+
| Primary TBV deposit (10 BTC) |-------->| Borrower position         |
|   -> mirrored to Aave V4     | collat. | $750k USDC debt / 10 BTC  |
|                              |         +---------------------------+
| Backup Shield Vault (1.5 BTC)|                     ^
|   pre-signed Taproot graph   |  releases 0.8 BTC   |
+------------------------------+  via pre-signed Path 2
              ^
              | "HF hits 1.015"
   +----------------------+
   | Prediction engine    |  monitors feeds, triggers in the Danger Zone
   +----------------------+
```

## 4. The hard part: Bitcoin's 10-minute blocks vs. a 60-second crash

Bitcoin confirms blocks every ~10 minutes. An EVM oracle can update in ~20 seconds. If you wait for the crash and *then* send a fresh Bitcoin transaction, your collateral arrives ~10 minutes too late — long after liquidation.

The design resolves this with **two-horizon lead-time synchronization**, and an explicit, honest reconciliation of *how* an EVM top-up settles back to Bitcoin.

```
15-MIN MACRO HORIZON                    10-SEC MICRO HORIZON
Predicts a sustained dip ~15 min out    Predicts a sudden flash wick
        |                                        |
        v                                        v
Submits pre-signed Path 2 tx            Keeper advances an optimistic EVM
on Bitcoin mainnet EARLY                buffer in the SAME BLOCK,
(confirms before the crash)             reconciled to Bitcoin later via BABE
```

**Macro horizon (native Bitcoin settlement):** when the model projects a downward trend 15–20 minutes ahead (while HF is still a comfortable 1.06–1.08), it submits the pre-signed Path 2 Bitcoin transaction *early*, so satoshis confirm on Bitcoin mainnet before the crash lands.

**Micro horizon (optimistic advance):** for violent 60-second wicks where waiting 10 minutes is impossible, a TBV **Application Vault Keeper** advances an EVM liquidity buffer in the same block. This is the key honest distinction:

- The **optimistic advance** is a fast, *trusted-but-slashable* action by the keeper, who takes temporary counterparty risk.
- **BABE** (BitVM-based ZK-proof verification of EVM events) is the eventual **trustless settlement backstop**, operating over a **2–3 day fraud-proof challenge window** — *not* the real-time gate.

In plain terms: **the borrower is protected in the same block; the keeper is exposed for 2–3 days.** Everything downstream (fees, risk, economics) follows from that single fact, and I have priced it accordingly rather than pretending settlement is instant.

## 5. A complete, balance-sheet walkthrough

Same crash ($100k -> $88k), now shielded. Here is the full accounting at the $88,000 bottom — every figure reconciles across collateral, the depleted backup vault, unchanged debt, and net equity.

| Metric | Unprotected liquidation | TBV JIT Shielding |
| :--- | :--- | :--- |
| Primary collateral in loan | 5.526 BTC ($486,288) | 10.800 BTC ($950,400) |
| Backup shield vault | 1.500 BTC ($132,000) | 0.700 BTC ($61,600) |
| Total BTC owned | 7.026 BTC ($618,288) | 11.500 BTC ($1,012,000) |
| Outstanding debt | $375,000 | $750,000 |
| Forced penalty paid | **$18,750** | **$0** |
| Keeper Shield Fee | $0 | $107 |
| **Borrower net equity** | **$243,288** | **$261,893** |
| Resulting Health Factor | 1.102 | 1.077 |

**Net: ~$18,605 in equity preserved per successful save**, after the $107 keeper fee and ~$38 gas. (This is a per-save figure, not an annualized expected value.)

*Notes: Unprotected primary collateral reflects 10.0 BTC minus 4.474 BTC seized by the liquidator; shielded reflects 10.0 BTC plus 0.8 BTC top-up. Backup vault falls from 1.5 to 0.7 BTC as 0.8 BTC is deployed. Debt is unchanged under shielding; the unprotected column shows a 50% forced repayment.*

## 6. Who runs the prediction engine? Two options

Because Path 2 is bound by the pre-signed graph, the borrower has full flexibility over *who* triggers the top-up.

```
              Who runs the prediction engine?
                   /                  \
                  v                    v
   Option A - Self-Sovereign     Option B - Keeper Market
   Run your own engine           Independent quants compete
   0% fees, full privacy         0.15% fee, zero maintenance
   ~250:1 payoff                 ~102:1 payoff
                  \                    /
                   v                  v
        Pre-signed top-up executed safely via TBV
```

**Option A — Self-Sovereign.** Run your own prediction engine next to your own Bitcoin node. Zero third-party fees, total privacy over your models. Best for institutional desks and technical Bitcoiners.

**Option B — Keeper Market.** Delegate to an open market of independent Application Vault Keepers. When your position enters the Danger Zone, the first keeper to execute the top-up earns a single **0.15% consolidated Keeper Shield Fee** ($107 in the example). Zero infrastructure to maintain. Best for passive borrowers.

The alignment shift is the whole point: **instead of paying ~$18,750 to a liquidator who destroys your loan, you pay ~$107 to a keeper who saves it.**

The fee is deliberately a *single consolidated fee* (not stacked bounty + bridge premium) that compensates the keeper for fronting real capital across the 2–3 day settlement window and carrying directional price risk during it. It is funded from a dedicated borrower shield deposit inside the EVM controller — not by Aave, which has no native primitive to pay such a fee.

## 7. Security and the risks keepers actually take

The interesting risks live with the keeper during that 2–3 day window. The main defenses:

- **Directional / basis risk during the lag.** If BTC keeps falling while the keeper waits to be reconciled, the collateral they receive is worth less than what they fronted. Mitigation: the 0.15% fee is upfront revenue, and keepers hedge advanced inventory on perp markets. (This hedge itself carries basis and funding-spike risk during violent wicks — a real cost that belongs in the fee calibration.)
- **The proactive-vs-oracle gate contradiction.** A naive on-chain check like `require(onChainHF <= 1.04)` would *revert* a proactive top-up, because the whole point is to act *before* the oracle updates (when on-chain HF is still ~1.13). The fix is an **Optimistic Verification Gate**: the top-up executes on a signed forecast attestation, and the hard on-chain check is applied at *reconciliation time*. If the predicted drop never materializes, it is booked as a false positive and the keeper eats the cost — the borrower is never penalized.
- **Theft of backup BTC.** Impossible by construction — destinations are hardcoded in the pre-signed graph.
- **MEV / transaction snatching.** Keepers use pre-registered, signed mandates submitted via private bundles (Flashbots/MEV-Boost), so copycats revert.

## 8. Why the asymmetry survives false positives

The economics only work if false positives are cheap. They are.

**Option A (self-sovereign):** a false positive costs ~$75 (two EVM txs + a few days of illustrative net carry on the idle buffer). Against ~$18,750 saved on a true positive, that is roughly a **250:1** per-event asymmetry.

**Option B (keeper market):** a false positive costs ~$182 ($75 + the $107 fee), for roughly **102:1**.

A necessary caveat: these are **per-event** ratios, not annualized expected values. The annual cost to a borrower depends on the *false-positive rate*, which is exactly the empirical parameter the prediction research would pin down. The point of the asymmetry is not "we predict perfectly" — it is "we can afford to be wrong a lot and still come out far ahead," which is what makes the whole approach robust to the open research question above.

## 9. What I am proposing — and where I would love scrutiny

This is a design for review, not a finished product, and it is built to sit on top of TBV as it matures toward mainnet. I would genuinely value critique on five questions in particular:

1. **Pre-signed graph expressiveness:** Can TBV's pre-signed transaction graph and emulated covenant committee support conditional top-up routing *without* dynamic signature exchange at spend time?
2. **BABE reconciliation:** Is the 2–3 day challenge window well-suited to reconciling short-horizon optimistic advances, or does it need a different construction?
3. **Keeper risk pricing:** Is a flat 0.15% consolidated fee, plus perp hedging, sufficient risk-adjusted compensation across a multi-day lag — especially under *correlated* crashes when many positions breach at once and every keeper's hedge is stressed simultaneously? (I consider portfolio-scale capital adequacy the hardest open economic question here.)
4. **Prediction validation:** the core empirical ask — benchmarking sub-minute BTC microstructure forecasting on historical flash-crash data to calibrate the trigger threshold.
5. **Aave V4 edge cases:** any HF-calculation or interest-accrual corner where an optimistic top-up fails to clear the Danger Zone during cascading liquidations.

If this is interesting to you — whether to build on, poke holes in, or just argue about — I would love to hear from you.

---

*Built on real Babylon TBV primitives. Honest about the one thing that still has to be proven.*
