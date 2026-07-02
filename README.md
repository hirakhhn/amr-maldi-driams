# Beyond Basel: Can MALDI-TOF Predict Antibiotic Resistance Where It Matters Most?

## Introduction

Antimicrobial resistance (AMR) is typically diagnosed through culture-based susceptibility 
testing, a process that takes 24–72 hours. Machine learning models trained directly on 
MALDI-TOF mass spectrometry data; a tool already used for rapid species identification in 
Many hospital labs offer a way to predict resistance in minutes instead of days, using 
data that's often already being collected but not yet used this way.

This notebook builds a resistance classifier for *Klebsiella pneumoniae*, a WHO 
critical-priority pathogen, using the DRIAMS dataset (Weis et al., *Nat Med* 2022); a 
public, benchmarked collection of MALDI-TOF spectra from Swiss hospitals.

## Why it matters

DRIAMS was collected in Switzerland, where antibiotic resistance is comparatively rare. 
But the countries that would benefit most from a fast, low-cost resistance prediction tool 
are places with **high** resistance burden and limited lab capacity, like Indonesia, where 
national surveillance data (SINAR) shows K. pneumoniae resistance rates several times higher 
than this Swiss cohort. This notebook doesn't just build a model; it asks whether a method 
validated in a low-resistance setting is even the right starting point for the settings that 
need it most.

## Key findings

- Built a regularised logistic regression classifier predicting Ceftriaxone (ESBL-type) 
  resistance in *K. pneumoniae* from 6,000-bin MALDI-TOF spectra (n=2,846 isolates)
- Achieved **test AUROC 0.853**, exceeding the original DRIAMS benchmark of 0.74 for this species
- SHAP interpretability identified a spectral region (m/z bins ~45–62) that independently 
  matched a simple mean-difference analysis — convergent evidence of a real, learnable signal
- Discussion section quantifies the gap between where this technology was validated (low-AMR 
  Switzerland) and where it's most needed (high-AMR Indonesia), with a concrete proposal for 
  local implementation

## Discussion - Translating MALDI-TOF-Based AMR Prediction to a High-Burden LMIC Context

**Model performance in context**

The regularised logistic regression model developed in this study achieved a test-set AUROC of 0.853 for predicting Ceftriaxone (3rd-generation cephalosporin) resistance in Klebsiella pneumoniae isolates from MALDI-TOF mass spectra, using the DRIAMS-A cohort (n=2,846). This exceeds the AUROC of 0.74 reported for K. pneumoniae in the original DRIAMS benchmarking study by Weis et al. (1), though it should be noted that the original benchmark spanned a broader antibiotic panel rather than Ceftriaxone specifically, so the comparison should be read as indicative of comparable-or-better feasibility rather than a direct head-to-head replication. SHAP-based interpretability analysis identified a cluster of low-mass spectral bins (approximately m/z bins 45–62) that converged with an independent, model-free comparison of mean spectral differences between resistant and susceptible isolates, lending confidence that the model is learning a genuine, reproducible signal rather than fitting noise. Several additional bins with no univariate signal were also identified as important by SHAP, consistent with the model capturing multivariate interactions between spectral regions that a simple two-group comparison cannot detect.

**A mismatch between where this technology was validated and where it is most needed**

DRIAMS was collected across four Swiss hospital sites, a setting characterized by comparatively low antimicrobial resistance prevalence: in this cohort, only 0.8% of K. pneumoniae isolates were resistant to carbapenems, and even after combining meropenem and imipenem into a single carbapenem-resistance definition, only 31 of 2,846 isolates (1.1%) qualified; a rate too low to support reliable model training, which is why this study pivoted to the more prevalent Ceftriaxone/ESBL-type resistance phenotype (15.3% prevalence) as its outcome.

This low baseline resistance is not representative of the settings where rapid, low-cost AMR prediction would have the greatest clinical impact. Indonesia offers an instructive contrast. Surveillance data from the Indonesian Antimicrobial Resistance Surveillance network (SINAR), coordinated by the Indonesian Association of Clinical Microbiology Specialists (PAMKI), reported third-generation cephalosporin resistance in K. pneumoniae at 52% and in E. coli at 66%, based on isolates collected across 70 hospitals in 15 provinces (2). Carbapenem resistance in the same report was 17% for K. pneumoniae, more than fifteen times the rate observed in the DRIAMS-A cohort. The most recent SINAR surveillance cycle, covering 2024 data, expanded to 136,017 bacterial isolates collected from 139 hospitals across 24 provinces (3), indicating both the scale of the resistance burden and the scale of existing laboratory data collection infrastructure in Indonesia.

This contrast reframes the translational question. It is not simply "does this technology work," since this study and the original DRIAMS work suggest that it does, within the limits of available labelled data. The more important question is whether a tool validated in a low-resistance-prevalence, high-resource setting generalises to a setting where resistance is common enough that a missed prediction — a false negative, as characterised in this study's evaluation- carries substantially higher population-level consequences.

**Infrastructure: further along than a "resource-limited setting" framing suggests**

It would be inaccurate to characterise Indonesia as lacking the infrastructure for this approach. The Fleming Fund Country Grant to Indonesia, now in its second phase (2024–2026, GBP 5.8 million), has supported laboratory capacity strengthening and whole-genome sequencing investment across a network of more than 35 national laboratories, working directly with the Ministry of Health (4). MALDI-TOF-based species identification is already in active clinical use at a meaningful number of Indonesian referral and teaching hospitals as part of this surveillance expansion. The core infrastructure gap is therefore narrower and more specific than "Indonesia does not have MALDI-TOF", it is that routine clinical microbiology workflows typically retain only the final species, identification and susceptibility outputs of a MALDI-TOF run, discarding the raw or binned spectral data this study's modelling approach depends on.

**A concrete, low-marginal-cost next step**

Given that SINAR-affiliated laboratories are already generating MALDI-TOF spectra at substantial scale — over 136,000 isolates in the most recent surveillance cycle alone — the infrastructure required to build a DRIAMS-equivalent Indonesian dataset may already exist in large part. What is likely missing is a data retention and standardisation protocol: configuring existing MALDI-TOF systems to export and archive raw or binned spectra (rather than only the final identification result), linked to the same antimicrobial susceptibility testing results already collected for GLASS-AMR reporting.

A feasible pilot would not require new capital investment in additional mass spectrometers. It would require: (1) selecting a small number of SINAR-network hospitals already operating MALDI-TOF instruments, (2) implementing spectral data export and storage alongside existing susceptibility workflows for a defined pilot period, and (3) applying the modelling pipeline developed in this study, or an extension of it, to the resulting Indonesian-context dataset. Because Indonesian K. pneumoniae resistance prevalence is severalfold higher than in the Swiss cohort used here, such a pilot would likely yield substantially larger positive-class sample sizes over a shorter collection period than were available in this study, potentially allowing for more robust modelling, including approaches (such as gradient-boosted trees) that underperformed in this study specifically because of limited positive-class sample size.

This approach would also generate a direct, population-relevant answer to the question this study could only address indirectly: whether MALDI-TOF-based resistance prediction, once trained on local epidemiology rather than transferred from a low-resistance-prevalence setting, can meet or exceed the performance benchmarks established here and in the original DRIAMS work.


## References


1. Weis C, Cuénod A, Rieck B, et al. Direct antimicrobial resistance prediction from clinical MALDI-TOF mass spectra using machine learning. Nat Med. 2022;28(1):164-174.
2. Indonesian Association of Clinical Microbiology Specialists (PAMKI). Antibiotic Resistance Report from the Surveillance of Indonesia Network on Antimicrobial Resistance (SINAR) 2023.
3. PAMKI. Indonesian Antibiotic Resistance Report 2024 (SINAR surveillance, 2024 data). Jakarta: PAMKI; 2025.
4. Fleming Fund. Phase II of Fleming Fund launches in Indonesia [Internet]. 2025 Jan 28. Available from: https://www.flemingfund.org/publications/phase-ii-of-fleming-fund-launches-in-indonesia/

