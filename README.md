# Causal ML: Email Campaign Uplift Modeling

A causal inference project that answers a different question than a standard classifier would. Most models predict who is likely to buy. This one predicts who buys *because* they got the email, versus who would have bought anyway, and who the email actually annoys into buying less. That distinction is the difference between a marketing model that looks good on paper and one that actually improves profit.

## Why this matters

Email marketing at scale usually targets everyone or targets high-probability buyers. Both waste money. High-probability buyers were often going to convert regardless of the email, so targeting them adds cost without adding incremental sales. Uplift modeling instead ranks customers by how much the email itself changes their behavior, which is the number that should actually drive who gets targeted.

## Dataset

Kevin Hillstrom's MineThatData email A/B experiment, a well-known public dataset in the causal inference and marketing analytics space. 64,000 customers were randomly assigned to one of three groups: a men's merchandise email, a women's merchandise email, or no email at all (the control group). Visit, conversion, and spend were tracked for the two weeks following.

## Methodology

**1. Validating the experiment itself.** Before trusting any causal claim, I checked that randomization actually worked: balance checks and independent t-tests across pre-treatment features (recency, past spend, channel, customer tenure) confirmed the treatment and control groups looked statistically similar before the email went out. Without this, any observed difference afterward could just be confounding rather than a real effect.

**2. Measuring the average treatment effect.** An independent t-test on spend between the treated and control groups showed a statistically significant lift (p-value near zero), establishing that the email works on average before going any further into per-customer effects.

**3. Building a T-Learner.** Rather than one model predicting purchase probability, I trained two separate XGBoost classifiers: one fit only on customers who received an email, one fit only on customers who didn't. For any customer, subtracting the control model's predicted probability from the treatment model's predicted probability gives an individual estimate of how much that specific email changed their odds of converting. This is the "uplift" score.

**4. Segmenting customers by uplift.** Combining the uplift score with the baseline (no-email) conversion probability splits customers into four groups:
- **Persuadable**: positive uplift, the email causes the purchase
- **Sleeping dog**: negative uplift, the email actively pushes them away
- **Sure thing**: high baseline probability regardless of email, targeting them wastes a send
- **Lost cause**: low probability either way, not worth targeting

**5. Validating the ranking with a Qini curve.** A Qini curve and its AUUC (area under the uplift curve) score measure whether sorting customers by predicted uplift actually captures more incremental conversions than random targeting would. I built this metric from scratch to understand exactly what it measures, then cross-checked the result against the `causalml` library's implementation to confirm they agreed.

**6. Turning the ranking into a business decision.** Using an assumed profit margin per conversion and a cost per email sent, I built a cumulative profit curve across the uplift-ranked customer list. This identifies the actual point where additional targeting stops adding profit, which is a more concrete answer than "target everyone with positive uplift."

## Results

| Finding | Result |
|---|---|
| Average effect of men's email vs. control | +118% lift in spend |
| Average effect of women's email vs. control | +65% lift in spend |
| Statistical significance | p ≈ 0 (Welch's t-test) |
| Persuadable segment | 21% of customers |
| Sleeping dog segment | 6% of customers |
| Qini AUUC | ~17, beating random targeting |
| Optimal targeting cutoff | top ~9% of customers by predicted uplift |
| Email volume reduction at that cutoff | ~90% fewer emails sent |
| Profit retained at that cutoff | majority of total incremental profit |

The headline result: instead of emailing the full customer base, targeting only the top 9% by predicted uplift keeps most of the incremental profit from the campaign while cutting send volume by roughly 90%, which matters directly for cost and for avoiding the sleeping dog segment that the email actively harms.


## Running it

pip install -r requirements.txt
jupyter notebook uplift_modeling.ipynb




