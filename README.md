# Third-Party Risk Prediction for Audit

## Overview
This project is a reproducible demonstration of how Internal Audit can use predictive analytics to prioritise third-party vendors for review. It starts with eleven separate hypothetical source-system extracts, prepares and joins them into an analytical vendor table, trains two classification models, compares performance, selects a demonstration model, ranks vendors by predicted risk and explains the result in plain language. All data is synthetic and must not be represented as genuine vendor, customer or organisational data.

## Business problem
An audit team may have thousands of vendors but enough capacity to deeply test only a small proportion. Random sampling can support population-level assurance when properly designed, but it may not maximise the number of higher-risk records examined. This project demonstrates a complementary risk-focused approach: use information known at an assessment date to estimate which vendors are more likely to experience at least one defined risk event during the following 12 months, rank the population and focus early audit attention on the highest-scored vendors. The model is a triage tool, not audit evidence and not a substitute for statistically valid sampling where projection is required.

## Measurement outcome in plain English
For each vendor, the model returns a probability between 0% and 100%. It measures the estimated chance that the vendor will experience at least one defined third-party risk event in the next 12 months. The five simulated events are an overdue periodic review, control deficiency, concentration-risk event, financial distress and contract non-compliance. A score of 72% means the model estimates a 72% chance of at least one event based on the hypothetical training patterns. It does not mean the vendor is 72% non-compliant, that 72% of controls failed, or that an audit finding exists. Audit must obtain evidence and test controls before reaching a conclusion.

## Predictor coverage
The notebook includes more than 50 variables across vendor profile, country and industry risk, criticality, outsourcing category, tenure, spend, contracts, governance timeliness, ownership changes, attestations, training, issues, remediation, risk acceptances, assurance, service performance, incidents, cyber security, financial health, concentration, alternate providers, subcontracting, relationship changes, escalations, complaints and invoice disputes.

## Source-system architecture
The project deliberately does not assume one system contains every model field. Procurement provides the vendor population anchor. Accounts payable provides spend measures. Contract lifecycle management provides contract dates and complexity. The third-party governance workflow provides review, assessment, attestation and training information. Governance, risk and compliance issue management provides issue and remediation measures. The assurance register provides control-assurance results. Service management provides incidents and service performance. The cyber platform provides vulnerability and security measures. Financial risk and invoicing provide financial health and dispute measures. Resilience and relationship management provide dependency and alternative-provider measures. A separate future event registry provides training labels observed after the assessment date.

## Data preparation and lineage
The notebook loads every extract separately, renames source-specific keys, parses dates, derives days overdue and contract timing as at 31 December 2025, checks missing and duplicate identifiers, anchors the population to the procurement master, performs validated one-to-one joins, adds the separately observed future outcomes and creates the composite target. `hypothetical_vendor_risk_data.csv` is therefore an analytical output, not a source-system input.

## Files
- `third_party_risk_prediction.ipynb`: complete self-contained notebook.
- `README.md`: this documentation.
- `requirements.txt`: Python dependencies.
- `source_01_...csv` to `source_11_...csv`: separate hypothetical source-system extracts.
- `hypothetical_vendor_risk_data.csv`: integrated analytical table produced by the notebook.
- `vendor_risk_scores_test_set.csv`: generated ranked test population.
- `model_performance.csv`: generated candidate-model comparison.

## How it works
1. Load eleven separate source-system extracts.
2. Standardise keys and field names, parse dates and derive model features.
3. Check source-level key quality, then join the extracts to the procurement vendor population.
4. Add the separate future event labels and combine them into `any_risk_event_12m`.
5. Exclude all future outcome fields from predictors to prevent direct target leakage.
5. Split the data into training and held-out test populations.
6. Preprocess numeric and categorical variables in reusable pipelines.
7. Fit logistic regression and random forest models.
8. Compare ROC AUC, average precision, accuracy, precision, recall and F1.
9. Select the candidate with the higher test ROC AUC for demonstration.
10. Assign relative audit-priority bands and calculate the share of actual synthetic events captured when testing the highest-scored vendors.
11. Calculate permutation importance and prepare layman-friendly review reasons.
12. Export data, scores and performance results.

## Run locally
Python 3.10 or later is recommended.

```bash
pip install -r requirements.txt
jupyter notebook third_party_risk_prediction.ipynb
```

Select **Run All**. The notebook does not download data from the internet.

## Performance measures
- **ROC AUC** measures ranking quality. 0.5 is random ranking and 1.0 is perfect.
- **Average precision** summarises event detection when events may be uncommon.
- **Precision** is the share of classified high-risk vendors that had an event.
- **Recall** is the share of event vendors identified by the model.
- **F1** balances precision and recall.
- **Accuracy** is the overall share classified correctly but can mislead with imbalanced outcomes.
- **Calibration** tests whether estimated probabilities align with observed event rates.

## Audit use
Use the output to rank the population, select an initial high-risk group, inspect contextual indicators, obtain source evidence and complete normal control testing. Priority bands are workload bands, not formal vendor ratings. Consider a combined selection strategy: representative or random items for broader assurance, model-targeted items for higher-risk coverage and mandatory judgemental items such as critical vendors or known incidents.

## Limitations
Synthetic data proves the code can run, not that a real model will work. Simulated relationships may be cleaner than reality. A composite target can conceal which specific outcome drives the score. Production validation should use earlier periods for training and later periods for testing. Importance indicates model reliance, not causation. A high score is not a finding and a low score is not assurance. Real vendor data may be commercially confidential or restricted.

## Production checklist
1. Define outcome, materiality, observation date and horizon.
2. Confirm data ownership, access, lineage, retention and permitted use.
3. Establish a stable vendor identifier across systems.
4. Prove every predictor pre-dates the outcome.
5. Assess missingness, duplicates, stale values, backfilling and survivorship bias.
6. Train on resolved and consistently labelled events.
7. Use time-based back-testing and compare with simple rules and current ratings.
8. Set thresholds using capacity and false-positive versus false-negative costs.
9. Test calibration and performance across legitimate vendor segments.
10. Review privacy, sensitive attributes and proxy variables.
11. Define human review, challenge, override and escalation.
12. Version the code, model, data, features, thresholds and outputs.
13. Monitor data drift, event drift, calibration and false negatives.
14. Revalidate and retire the model when necessary.

## Governance disclaimer
The model should direct attention, not autonomously determine findings, issue severity, contract termination or accountability. Adapt it only after appropriate data, model, legal, privacy, security and audit-methodology review.
