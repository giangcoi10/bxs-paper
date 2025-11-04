# Pre-Registration: Bitcoin-Seconds (BXS) Empirical Tests — v0.6.6

## Objectives
Test whether durability-adjusted flow f(t) predicts holding behavior and early liquidation better than balance-only or coin-age-only baselines.

## Hypotheses
- H1 (Durability): Higher f(t) at time τ predicts a higher probability of HOLD over [τ, τ+Δ], controlling for W(t).
- H2 (Early Warning): Large negative Δf(t) over a short window precedes liquidation events within L days.
- H3 (Component Value): Each driver (A, I, SSR) adds incremental predictive power in nested models.

## Outcome & Labels
- HOLD(τ; Δ, x): 1 if wallet retains ≥ (1−x)% of W(τ) after Δ days, else 0.
  - Primary: Δ = 90 days, x = 5%.
  - Robustness: Δ ∈ {60,120}, x ∈ {3%, 10%}.

## Features (computed at τ)
- W(τ): balance (sats)
- A(τ): value-weighted coin age (s)
- I(τ): mechanical protocol expansion rate (s⁻¹)
- SSR(τ): [s(τ)+r·i(τ)−CP(τ)] / [max{τ−τ₀, t_min} · max{μ(τ), μ_min}]
- f(τ): i(τ) · (A/A₀) · (I/I₀) · SSR(τ)

Baselines: A₀, I₀ fixed 180-day medians; t_min = 30 d; μ_min = 1 sat/s.

## Model Specs (fixed)
- M1: HOLD ~ W
- M2: HOLD ~ W + A
- M3: HOLD ~ W + A + I
- M4: HOLD ~ W + A + I + SSR
- M5: HOLD ~ f

## Estimation & Metrics
- Classification: Logistic regression; Report AUC, Brier, accuracy.
- Survival (H1): Cox model on time-to-sell; log-rank across f(t) quartiles.
- Early warning (H2): Rule: flag if f drops ≥20% over 14 days; report TPR/FPR, lead time.
- Nested comparisons (H3): Likelihood ratio tests; AICc, ΔAICc; out-of-sample AUC.

## Splits & CV
- Rolling origin CV with 70/30 expanding window; final hold-out = last 20% of timeline.
- No target leakage: features only from τ.

## Significance Thresholds
- p < 0.05 (LR tests); ΔAICc > 10 = strong evidence; AUC improvement ≥ 0.05 = meaningful.

## Freeze Notice
This plan is locked prior to viewing test outcomes. Any deviations will be addended and time-stamped.
