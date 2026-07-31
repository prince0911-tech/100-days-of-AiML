# Exoplanet Detection from TESS Light Curves

An end-to-end pipeline that takes a raw star observation from NASA's TESS mission, detects periodic transit dips, and classifies whether the signal is a real planet or a false positive — with a confidence score.

Built for **ISRO's Bhartiya Antariksh Hackathon 2026 (BAH2026)**, Problem Statement 7.
Team **Nakshatras** — Parul University.

---

## What it does

When a planet passes in front of its star, the star's brightness drops by a fraction of a percent. TESS records these brightness measurements every two minutes. The hard part is that most dips aren't planets — eclipsing binary stars, instrument artifacts, and background noise produce the same signature.

This pipeline handles both halves of the problem:

```
Star name/TIC ID
      ↓
  Download SPOC light curve          (lightkurve → MAST)
      ↓
  Flatten & remove NaNs              (strip long-term stellar variability)
      ↓
  BLS periodogram                    (search 1–15 day periods for box-shaped dips)
      ↓
  Extract period, t0, duration, depth, SNR
      ↓
  Look up stellar radius & temp      (TESS Input Catalog)
      ↓
  Random Forest classifier           (planet vs. false positive + confidence %)
```

---

## The model

**Training data** — the ExoFOP TOI catalog (8,064 objects of interest). Filtered to firmly-labelled rows only:

| TFOPWG Disposition | Meaning | Label |
|---|---|---|
| `CP` Confirmed Planet | real | 1 |
| `KP` Known Planet | real | 1 |
| `FP` False Positive | not a planet | 0 |
| `FA` False Alarm | not a planet | 0 |

Ambiguous dispositions (`PC`, `APC`) are dropped. After removing rows with missing values: **2,496 objects**, near-balanced at 1,302 planets / 1,194 false positives.

**Features (7)**

`Period (days)` · `Duration (hours)` · `Depth (ppm)` · `Planet SNR` · `Planet Radius (R_Earth)` · `Stellar Eff Temp (K)` · `Stellar Radius (R_Sun)`

**Classifier** — Random Forest, 200 trees, 80/20 stratified split.

### Results

```
                precision    recall  f1-score   support

False Positive       0.86      0.80      0.83       239
   Real Planet       0.83      0.88      0.85       261

      accuracy                           0.84       500
```

**Feature importance**

| Feature | Importance |
|---|---|
| Period (days) | 0.170 |
| Planet Radius (R_Earth) | 0.168 |
| Stellar Radius (R_Sun) | 0.163 |
| Depth (ppm) | 0.131 |
| Stellar Eff Temp (K) | 0.129 |
| Duration (hours) | 0.123 |
| Planet SNR | 0.117 |

Importance is spread fairly evenly — no single feature dominates, which suggests the model is combining evidence rather than latching onto one shortcut.

---

## Validation

Run end-to-end on Sector 1 targets:

| Target | Ground truth | Period (d) | Depth (ppm) | Prediction | Confidence |
|---|---|---|---|---|---|
| Pi Mensae | Confirmed planet (Pi Men c) | 6.271 | 186.6 | Real Planet | 88.5% |
| WASP-100 | Confirmed hot Jupiter | 2.848 | 2761.7 | False Positive | 26.0% |
| WASP-18 | Confirmed hot Jupiter | 3.288 | 2278.0 | False Positive | 40.0% |

Pi Mensae recovers correctly. The two hot Jupiters do not — see **Known limitations** below.

---

## Known limitations

**Train/serve feature mismatch.** The classifier is trained on ExoFOP catalog measurements but receives BLS-derived measurements at inference. These are not on the same scale, which is the leading suspect for the WASP-100 and WASP-18 misclassifications:

- *Depth suppression* — `flatten(window_length=401)` uses a window (~13 h at 2-min cadence) comparable to the transit duration itself, so deep transits are partially absorbed by the smoother. WASP-18b's true depth is roughly 8,800 ppm; the pipeline recovers 2,278 ppm. Fix: widen the window or mask in-transit points before flattening.
- *SNR definition* — the pipeline computes `(depth / noise) × √n_in_transit`, which is not the same quantity as the catalog's `Planet SNR`.
- *Radius propagation* — `Planet Radius` is derived from the measured depth, so the depth error carries into the second-most-important feature.

**Sector 1 only.** The pipeline defaults to `sector=1`; multi-sector stitching is not implemented.

**Single-planet assumption.** BLS returns only the strongest periodic signal. Multi-planet systems will surface just one candidate.

**Period search window.** Fixed at 1–15 days. Longer-period planets fall outside the search range.

---

## Files

| File | Purpose |
|---|---|
| `model.ipynb` | Full pipeline — detection, training, classification, figures |
| `exoplanet_classifier.pkl` | Trained Random Forest + feature list (joblib) |
| `tois.csv` | ExoFOP TOI catalog (see setup) |
| `showcase_pi_mensae.png` | Two-panel figure: raw light curve + folded transit with classification |

---

## Setup

```bash
pip install lightkurve numpy pandas matplotlib scikit-learn joblib astroquery
```

Download the TOI catalog from [ExoFOP-TESS](https://exofop.ipac.caltech.edu/tess/view_toi.php) and save it as `tois.csv` in the project root. It isn't committed here — it updates continuously, and the version you pull will differ from the one used for the reported numbers.

Light curves download automatically from MAST on first run and cache locally, so an internet connection is needed initially.

---

## Usage

**Analyze one star**

```python
result = full_pipeline('Pi Mensae', sector=1)
```

```python
{'target': 'Pi Mensae',
 'status': 'analyzed',
 'period_d': 6.2707,
 'duration_h': 2.4,
 'depth_ppm': 186.6,
 'snr': 25.1,
 'classification': 'Real Planet',
 'planet_confidence_%': 88.5}
```

**Batch**

```python
targets = ['Pi Mensae', 'TIC 38846515', 'TIC 25155310']
df = pd.DataFrame([full_pipeline(t, sector=1) for t in targets])
```

**Detection only, no classification**

```python
det = analyze_star('Pi Mensae', sector=1)
```

**Reload the trained model**

```python
import joblib
saved = joblib.load('exoplanet_classifier.pkl')
model, features = saved['model'], saved['features']
```

---

## Next steps

1. Align inference features with the catalog's measurement conventions — the fix most likely to recover the hot Jupiters
2. Multi-sector light curve stitching for longer baselines
3. Iterative BLS to recover additional planets in multi-planet systems
4. Calibrate predicted probabilities so the confidence score is directly interpretable

---

## Data & tools

- **TESS** light curves via [MAST](https://mast.stsci.edu/) — NASA/STScI
- **ExoFOP-TESS** TOI catalog — NASA Exoplanet Science Institute
