# blush-season-capital-analysis
Does brand age/funding structure predict blush shade distribution across the 16-season color framework? Color science + statistical testing + ML on Ulta's blush catalog
Brand Blush Composition Analysis
Season, Depth, Undertone & the Age/Capital Question

A data-driven follow-up to "Have We Reached Peak Blush?" — investigating whether brand age and ownership/funding structure predict how a brand's blush shade range is distributed across the 16-season color analysis framework.

Status: 🚧 Work in progress. The core analysis pipeline and classifier are functional, but the notebook is still being revised (cleanup, documentation passes, and reproducibility fixes are ongoing). See Roadmap below.

Overview

Using Ulta's blush catalog as the dataset, this project builds a full pipeline that:

Extracts and verifies shade colors directly from product swatch images (rather than trusting only the vendor-labeled color).
Converts colors to CIELAB space and derives lightness, chroma, and hue features.
Classifies every shade into one of the 16 seasonal color analysis subtypes using a rules-based, quartile-driven classifier (with a multi-tier patch layer to resolve cross-seasonal edge cases).
Tests whether individual brands' shade ranges are statistically skewed toward specific seasons, using Monte Carlo permutation testing (since standard chi-square assumptions weren't met at the per-brand level).
Enriches the dataset with brand metadata (founding year, founder, parent company, ownership structure) via Wikipedia lookups and manual verification.
Tests whether brand age and ownership/funding structure (Independent, PE/VC-Backed, Absorbed Conglomerate) correlate with seasonal skew direction, using Kruskal-Wallis, Dunn's post-hoc, and ANOVA/Tukey HSD.
Trains and compares Random Forest and XGBoost classifiers to predict a brand's ownership structure from its shades' color-science fingerprint alone, then audits the misclassified brands as case studies.
Key Questions
Do legacy/prestige brands actually skew toward oversaturated seasons, as the original article hypothesized — or does skew (when it exists) more often reflect intentional specialization toward underserved seasons?
Does a brand's funding/ownership structure (bootstrapped, VC/PE-backed, or conglomerate-absorbed) show up as a measurable signature in its color choices?
Can a shade's color-science properties alone predict what kind of company made it?
Data
Primary dataset: Ulta blush catalog (brand, shade name, vendor-labeled dominant colors, swatch image URL, original season type label).
Enrichment: brand founding year, founder, parent company, and ownership structure sourced from Wikipedia (wptools/wikipedia packages) and manually verified/corrected.

(Raw CSV not included in this repo — add data source/access notes here once finalized.)

Methodology Highlights
Color extraction: K-Means dominant-color extraction from swatch images, validated against vendor labels using Delta-E (CIE color difference); outliers investigated and traced to formula type (cream/liquid vs. powder).
Classification: Quartile-based (not "natural gap") cutoffs for lightness, chroma, and hue, since the underlying distributions are continuous — with a hue-wraparound correction and a documented fallback-path bug fix.
Statistical testing: Monte Carlo permutation testing for brand-level seasonal skew (standard chi-square was structurally inapplicable given 16 categories and real-world brand sample sizes); Kruskal-Wallis, Dunn's post-hoc (Bonferroni-corrected), and Tukey HSD for ownership-structure comparisons.
Modeling: Random Forest baseline, then XGBoost with inverse-frequency class weighting to address PE/VC-Backed misclassification; serialized production pipeline (.pkl).
Tech Stack

Python · pandas / numpy · scikit-learn · XGBoost · SciPy / statsmodels (Kruskal-Wallis, Tukey HSD, Dunn's test) · scikit-posthocs · seaborn / matplotlib · Pillow + KMeans (color extraction) · wptools / wikipedia (metadata enrichment) · Google Colab

Repo Structure
├── Brand_Blush_Comp.ipynb     # main analysis notebook
├── data/                      # (add) raw + processed CSVs
├── models/                    # (add) serialized classifier (.pkl)
├── README.md
Roadmap
 Finish revising/cleaning the notebook (remove exploratory dead-ends, add markdown documentation throughout)
 Push the trained classifier to Hugging Face (Model Hub + model card)
 Build an interactive dashboard to explore seasonal distribution by brand/ownership structure (platform still TBD — evaluating options)
 Add a proper requirements.txt / environment file
 Add data access/licensing notes for the underlying Ulta dataset
Findings (Summary)
Statistically significant seasonal skew was found in ~20% of brands tested — more often reflecting specialization toward underserved seasons than toward oversaturated ones, complicating the original hypothesis.
Chanel and Bobbi Brown, both named in the original article as likely offenders, did not show statistically significant skew once permutation-tested.
Ownership structure shows a measurable relationship with color choices (chroma, lightness, extraction reliability), with Independent/self-owned brands trending toward higher saturation and PE/VC-backed brands trending lower/tighter.
A gradient-boosted classifier predicts ownership structure from color-science features alone at ~52% accuracy (vs. a ~33–41% baseline), with delta_e_vs_original (extraction reliability) as a top feature — suggesting operational/production signatures, not just aesthetic ones, are detectable in the data.
Author

Tia · korrehq.com · @korrehq

License

(Add a license — MIT is a common default for portfolio/analysis repos if you want others to be able to reuse the methodology.)
