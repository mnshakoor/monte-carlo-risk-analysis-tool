# Quanta Analytica Monte Carlo Risk Lab
Client-side web application for risk modeling in conflict prevention, peacebuilding, humanitarian operations, and NGO security management.

**Runs entirely in the browser**  
- No server required  
- Single HTML file with embedded CSS and JavaScript  
- Only dependency is Chart.js via CDN  

---

## Table of Contents
- [1. Purpose and Audience](#1-purpose-and-audience)
- [2. What This App Does](#2-what-this-app-does)
- [3. Quick Start](#3-quick-start)
- [4. Interface Tour](#4-interface-tour)
- [5. Core Concepts](#5-core-concepts)
  - [5.1 Monte Carlo Simulation](#51-monte-carlo-simulation)
  - [5.2 Random Distributions](#52-random-distributions)
  - [5.3 Aggregation of Factors](#53-aggregation-of-factors)
  - [5.4 Correlation Modeling](#54-correlation-modeling)
  - [5.5 Sensitivity Analysis](#55-sensitivity-analysis)
  - [5.6 Risk Threshold and Gauge](#56-risk-threshold-and-gauge)
  - [5.7 Scenarios and Comparison](#57-scenarios-and-comparison)
  - [5.8 Sample Size and Convergence](#58-sample-size-and-convergence)
- [6. Data Schemas](#6-data-schemas)
  - [6.1 CSV Schema](#61-csv-schema)
  - [6.2 JSON Schema](#62-json-schema)
  - [6.3 Worked Example Datasets](#63-worked-example-datasets)
- [7. Workflows](#7-workflows)
  - [7.1 Manual Entry Workflow](#71-manual-entry-workflow)
  - [7.2 File Upload Workflow](#72-file-upload-workflow)
  - [7.3 Saving and Loading Scenarios](#73-saving-and-loading-scenarios)
  - [7.4 Batch Parameter Sweep](#74-batch-parameter-sweep)
  - [7.5 Exporting Results and Charts](#75-exporting-results-and-charts)
- [8. Modeling Playbook for Conflict and NGO Security](#8-modeling-playbook-for-conflict-and-ngo-security)
- [9. Performance Tuning](#9-performance-tuning)
- [10. Troubleshooting](#10-troubleshooting)
- [11. FAQ](#11-faq)
- [12. License and Attribution](#12-license-and-attribution)

---

## 1. Purpose and Audience
This tool is designed for security analysts, humanitarian logisticians, field managers, and peacebuilding practitioners who need fast, transparent, and repeatable risk estimation under uncertainty. It helps you quantify plausible outcome ranges, identify the factors that matter most, and compare intervention scenarios using transparent inputs suitable for decision briefings.

---

## 2. What This App Does
- Accepts risk factors and their probability distributions by manual entry or file upload  
- Simulates thousands to hundreds of thousands of outcomes client-side  
- Supports Normal, Triangular, Uniform, and PERT distributions  
- Models dependence among variables via a correlation matrix  
- Aggregates factors using Sum, Average, or Weighted Sum  
- Produces summary statistics, histogram, cumulative distribution curve, risk gauge, and sensitivity tornado chart  
- Saves scenarios to local storage and exports results and charts  
- Compares up to three scenarios side by side  
- Runs parameter sweeps for stress testing

---

## 3. Quick Start
1. Open the HTML file in a modern browser.  
2. Optional: toggle Dark Mode with the top right button.  
3. Load a preset from the left panel dropdown, then click **Load**.  
4. Set **Iterations** to 50,000 for a quick check. For larger studies, increase toward 500,000.  
5. Set **Aggregation** and **Risk threshold** based on your goal metric.  
6. Click **Run simulation**.  
7. Review Summary, Histogram, CDF, Gauge, and Tornado sensitivity.  
8. Click **Add to compare** to store results. Load another preset or dataset and repeat for side-by-side overlays.  
9. Export CSV and PNGs from the top right of the Results panel if needed.

---

## 4. Interface Tour
**Left panel - Configuration**
- **Scenario**  
  - Scenario name  
  - Iterations: from 1,000 to 500,000  
  - Mode: Quick caps iterations at 20,000 for speed, Full uses your requested count  
  - Aggregation: Sum, Average, or Weighted sum  
  - Risk threshold: threshold applied to the aggregated outcome  
  - Percentiles: comma separated values for the summary table (for example 5,10,25,50,75,90,95)

- **Risk Factors Table**  
  Columns:  
  - Name  
  - Distribution  
  - Params  
    - Normal: mean, sd  
    - Triangular: min, mode, max  
    - Uniform: min, max  
    - PERT: min, mode, max, lambda (shape, default 4)  
  - Weight  
  - Notes  
  - Remove

- **Correlation**  
  Pairwise entries from -0.9 to 0.9. Diagonal is 1 and locked.

- **Import and Save**  
  Upload CSV or JSON to populate. Manage saved scenarios in local storage.

- **Run Actions**  
  - Run simulation  
  - Add to compare  
  - Status chip shows run time or errors

**Right panel - Results**
- Summary statistics table  
- Sensitivity tornado chart  
- Histogram  
- Cumulative distribution function (CDF) curve  
- Risk gauge that shows P(outcome exceeds threshold)  
- Multi-scenario compare chart and table  
- Batch parameter sweep with a chart and table  
- Buttons to export CSV and save all charts as PNG

---

## 5. Core Concepts

### 5.1 Monte Carlo Simulation
Monte Carlo simulates many random draws from specified distributions to approximate the full outcome distribution of a model. Instead of a single point estimate, you get a range with probabilities, which is critical when data are sparse or uncertainty is high.

**Monte Carlo error**  
Sampling error decreases roughly with the square root of iterations. Doubling iterations reduces error by about 1 divided by the square root of 2.

### 5.2 Random Distributions
Pick a distribution that reflects the nature of each factor.

- **Normal**  
  Parameters: mean, sd  
  Use for symmetric noise around a central value.

- **Triangular**  
  Parameters: min, mode, max  
  Use when you have credible bounds and a most likely value, common in logistics planning.

- **Uniform**  
  Parameters: min, max  
  Use when only bounds are known and all values in the range are equally plausible.

- **PERT**  
  Parameters: min, mode, max, lambda  
  A smoothed version of Triangular using a Beta shape. Lambda controls how strongly the mode pulls the distribution toward itself. Lambda 4 is a standard choice.

### 5.3 Aggregation of Factors
The app builds an overall outcome from per-draw samples across factors using one of three methods:
- **Sum**  
  Useful when each factor contributes additively to risk or cost.
- **Average**  
  Useful when metrics are comparable and should be normalized by count.
- **Weighted sum**  
  Use **Weight** to emphasize or de-emphasize factors. Negative weights can represent protective factors that reduce risk.

### 5.4 Correlation Modeling
Real-world variables co-move. This app uses the Iman-Conover approach to impose a target rank correlation structure:
1. Generate independent samples for each factor.  
2. Generate correlated normal scores using your correlation matrix.  
3. Rank-match factors to the correlated scores.

This produces dependence that is robust for non-Normal marginals. Entries must be between -0.9 and 0.9. If the matrix is not positive definite, the app adds a small diagonal jitter and will warn if needed.

### 5.5 Sensitivity Analysis
The tornado chart reports Pearson correlation between each factor and the aggregated outcome across all Monte Carlo draws.  
- Values near +1 or -1 indicate strong influence.  
- Sign indicates direction: positive means higher draws increase the outcome, negative means higher draws reduce it.  
- With a weighted sum, negative weights can lead to negative sensitivity, which is expected for protective factors.

### 5.6 Risk Threshold and Gauge
You define a threshold for the aggregated outcome. The gauge displays
- **P(outcome > threshold)** for risk exceedance, or  
- For success models, set the threshold to your minimum success target and interpret the gauge as **P(outcome >= target)**.

### 5.7 Scenarios and Comparison
You can store up to three distinct simulation results in a compare set and overlay their CDFs. Use this to contrast Baseline, Deterioration, and Improved conditions, or to compare alternative program designs.

### 5.8 Sample Size and Convergence
- Use **Quick mode** to prototype structure and check modeling logic.  
- Use **Full mode** and increase iterations for final reporting or when tail probabilities matter.  
- Track the stability of key percentiles across re-runs to assess convergence.

---

## 6. Data Schemas

### 6.1 CSV Schema
Headers are fixed and case-insensitive. Only fill the fields needed for the chosen distribution.

```
name,dist,mean,sd,min,mode,max,lambda,weight,notes
```

- `name` string  
- `dist` one of Normal, Triangular, Uniform, PERT  
- `mean` number (Normal only)  
- `sd` number greater than 0 (Normal only)  
- `min` number (Triangular, Uniform, PERT)  
- `mode` number within [min, max] (Triangular, PERT)  
- `max` number greater than min (Triangular, Uniform, PERT)  
- `lambda` number greater than 0, default 4 (PERT only)  
- `weight` number, can be negative for protective factors  
- `notes` free text

### 6.2 JSON Schema
Top level fields fill the left panel settings and table. This is ideal when you also want to preload correlation.

```json
{
  "name": "Scenario Name",
  "iterations": 50000,
  "agg": "sum | average | weighted",
  "threshold": 1.2,
  "factors": [
    {
      "name": "FactorName",
      "dist": "Normal | Triangular | Uniform | PERT",
      "mean": 0.2,
      "sd": 0.05,
      "min": 0.0,
      "mode": 0.2,
      "max": 0.6,
      "lambda": 4,
      "weight": 1.0,
      "notes": "Optional"
    }
  ],
  "corr": [[1,0.3],[0.3,1]]
}
```

### 6.3 Worked Example Datasets
Use these to validate both manual and upload workflows.

**CSV - GSOC Conflict and NGO Security test**

```csv
# schema: name,dist,mean,sd,min,mode,max,lambda,weight,notes
Escalation_Risk_P,PERT,,,,0.08,0.25,0.60,4,1.0,Probability of armed conflict escalation (0 to 1)
Ceasefire_Break_P,Triangular,,,,0.05,0.18,0.50,,0.9,Likelihood of ceasefire breakdown in the next 60 days
Aid_Delivery_Delay_Days,PERT,,,,2,7,24,4,0.6,Expected delivery delay in days due to checkpoints and access
Route_Disruption_Index,Uniform,,,,0.00,,0.50,,0.7,Road closures and detours as a proportion
Team_Incident_P,PERT,,,,0.01,0.06,0.20,4,0.8,Probability of a security incident impacting a field team
Intel_Uncertainty,Normal,0.12,0.05,,,,,0.5,Uncertainty scaling factor for information quality
Community_BuyIn,PERT,,,,0.30,0.60,0.90,4,-0.7,Higher buy-in reduces overall risk with weighted aggregation
Health_System_Stress,Triangular,,,,0.10,0.35,0.80,,0.7,Stress index from facility load and supply shortages
Border_Clearance_Days,Triangular,,,,1,3,10,,0.5,Average time to clear shipments at border posts
```

**JSON - Scenario A Baseline Stabilization**  
File name suggestion: `gsoctest_scenario_A.json`. Matches the CSV above and includes correlation.  
**JSON - Scenario B Deterioration Alert**  
File name suggestion: `gsoctest_scenario_B.json`. Parameters shifted to stress test.  
**JSON - Scenario C Peacebuilding Performance**  
File name suggestion: `gsoctest_scenario_C_peacebuilding.json`. Models a success index with protective factors.

> You already received these JSON payloads separately. If desired, copy them into files with the names above and upload them from the app.

---

## 7. Workflows

### 7.1 Manual Entry Workflow
1. In **Scenario**, set Name, Iterations, Mode, Aggregation, Threshold, Percentiles.  
2. In **Risk Factors**, click **Add factor** for each variable.  
3. Choose **Distribution** per factor and fill the required parameters.  
   - Normal: mean, sd  
   - Triangular: min, mode, max  
   - Uniform: min, max  
   - PERT: min, mode, max, optional lambda  
4. Set **Weight** for each factor.  
   - Use negative weights for protective factors like Community_BuyIn.  
5. Open **Correlation** and fill pairwise entries as needed. Leave zeros for independence.  
6. Click **Run simulation** and review results.  
7. Click **Add to compare** to store this run. Rerun with altered assumptions and compare.

### 7.2 File Upload Workflow
1. Click **Upload CSV or JSON** then **Load file**.  
2. CSV fills the factor table. JSON fills the factor table, global settings, and correlation.  
3. Inspect the populated inputs for correctness.  
4. Click **Run simulation**.  
5. Use **Add to compare** if you want to overlay with other stored results.

### 7.3 Saving and Loading Scenarios
- Click **Save** to persist a configured scenario to local storage.  
- Use the **Local scenarios** dropdown to **Load** or **Delete**.  
- Scenarios persist in the same browser and device until cleared.

### 7.4 Batch Parameter Sweep
Use this to explore how a single parameter drives a chosen metric.

1. Select **Factor** and **Parameter** (mean, sd, min, mode, max, or lambda).  
2. Set Start, End, Steps, Iterations per step, and Metric (mean, median, std, p90, p95).  
3. Click **Run sweep**.  
4. Inspect the sweep line chart and the table of parameter values vs metric.

### 7.5 Exporting Results and Charts
- **Export CSV** generates a concise summary including percentiles.  
- **Save PNGs** saves histogram, CDF, gauge, tornado, compare, and sweep charts if present.

---

## 8. Modeling Playbook for Conflict and NGO Security
Use this section as a checklist when building scenarios.

**1. Define the outcome and unit**
- Are you modeling a risk score, days of delay, probability of incident, or a composite index  
- Pick an aggregation method that matches the outcome meaning  
  - Sum for additive risk or time costs  
  - Weighted sum for composite risk or success indices  
  - Average for normalized indices

**2. Identify drivers**
- Escalation_Risk_P  
- Ceasefire_Break_P  
- Team_Incident_P  
- Aid_Delivery_Delay_Days  
- Route_Disruption_Index  
- Border_Clearance_Days  
- Health_System_Stress  
- Intel_Uncertainty  
- Community_BuyIn (protective factor, negative weight in a risk score)  
- Social_Cohesion, Mediator_Access, Govt_Coordination for peacebuilding success models

**3. Pick distributions and parameters**
- Triangular or PERT when you have expert min, mode, max  
- Normal when symmetric variation is reasonable  
- Uniform when only bounds are known

**4. Choose weights**
- Start with domain judgment and revise after sensitivity results  
- Set negative weights for protective factors in risk models  
- Consider rescaling weights so that typical contributions are on comparable magnitudes

**5. Calibrate threshold**
- For risk: choose a tolerable risk level and use the gauge to read exceedance. If modeling success, choose the minimum required success target and interpret the gauge as attainment probability.

**6. Add correlations**
- Positive links among escalation, team incidents, and health system stress are realistic in many theaters  
- Negative link between Community_BuyIn and primary risks is common  
- Keep entries within [-0.9, 0.9]; avoid extreme pairs that can break positive definiteness

**7. Run, compare, iterate**
- Prototype with Quick mode  
- Finalize with Full mode and higher iterations  
- Store Baseline, Deterioration, and Improvement cases to show decision impact  
- Use the sweep to highlight the most actionable levers for decision makers

---

## 9. Performance Tuning
- **Iterations**: raise until key percentiles stabilize. Tail probabilities require larger runs.  
- **Factors count**: many variables with high iterations can tax mobile devices.  
- **Mode**: Quick caps to 20k iterations for snappy iteration.  
- **Browser**: desktop browsers with hardware acceleration perform best.  
- **Charts**: complex overlays are fine, but avoid unnecessary compares during exploratory steps.

---

## 10. Troubleshooting
- **Error: Add at least one factor**  
  Add rows before running.

- **Invalid parameter error**  
  - Normal needs sd greater than 0  
  - Triangular requires min less than or equal to mode less than or equal to max  
  - Uniform requires max greater than min  
  - PERT requires min less than or equal to mode less than or equal to max and lambda greater than 0

- **Correlation matrix not positive definite**  
  Reduce extreme entries, make entries symmetric, or start with smaller magnitudes. The app adds a small diagonal jitter but cannot fix severely inconsistent matrices.

- **Results differ across runs**  
  This is expected. Monte Carlo sampling is random. Increase iterations for stability.

- **Charts do not appear**  
  Ensure there are outputs by running a simulation. If memory is constrained, reduce iterations.

- **Import fails**  
  Validate CSV header names and JSON structure. Empty cells are allowed where parameters are not required by the chosen distribution.

---

## 11. FAQ
**Q. How should I pick lambda for PERT**  
A. Lambda 4 is a common default. Higher lambda pulls samples closer to the mode. Lower lambda spreads mass toward min and max.

**Q. Should I set negative weights**  
A. Yes for protective factors in a risk score. For success indices, consider positive weights for supportive factors and negative weights for headwinds like Spoiler_Activity.

**Q. Does the correlation matrix enforce exact Pearson correlations**  
A. The Iman-Conover method imposes rank correlations that approximate your targets in a robust way with non-Normal marginals.

**Q. Can I reproduce exactly the same run**  
A. The app uses the browser RNG, which is not seeded. For reproducible studies, document inputs and raise iterations until percentiles stabilize within acceptable tolerance.

**Q. What percentiles should I report**  
A. Common choices are p50, p90, p95 for risk exceedance planning and service levels. Add p5 or p10 to show favorable tail risk where relevant.

---

## 12. License and Attribution
- App runs entirely on the client for privacy.  
- Only external library is Chart.js via CDN.  
- Include attribution to Quanta Analytica Monte Carlo Risk Lab if you redistribute or adapt.  

---

## Appendix A. Parameter Reference

| Field | Distribution | Meaning |
|------|--------------|---------|
| mean | Normal | Central value |
| sd | Normal | Standard deviation greater than 0 |
| min | Triangular, Uniform, PERT | Lower bound |
| mode | Triangular, PERT | Most likely value within [min, max] |
| max | Triangular, Uniform, PERT | Upper bound greater than min |
| lambda | PERT | Shape parameter greater than 0; default 4 |
| weight | Any | Contribution in Weighted sum; can be negative |
| notes | Any | Free text for documentation |

---

## Appendix B. Example CSV Snippet
Paste into a file named `gsoctest_conflict_ngo.csv` and load in the app.

```csv
# schema: name,dist,mean,sd,min,mode,max,lambda,weight,notes
Escalation_Risk_P,PERT,,,,0.08,0.25,0.60,4,1.0,Probability of armed conflict escalation (0 to 1)
Ceasefire_Break_P,Triangular,,,,0.05,0.18,0.50,,0.9,Likelihood of ceasefire breakdown in the next 60 days
Aid_Delivery_Delay_Days,PERT,,,,2,7,24,4,0.6,Expected delivery delay in days due to checkpoints and access
Route_Disruption_Index,Uniform,,,,0.00,,0.50,,0.7,Road closures and detours as a proportion
Team_Incident_P,PERT,,,,0.01,0.06,0.20,4,0.8,Probability of a security incident impacting a field team
Intel_Uncertainty,Normal,0.12,0.05,,,,,0.5,Uncertainty scaling factor for information quality
Community_BuyIn,PERT,,,,0.30,0.60,0.90,4,-0.7,Higher buy-in reduces overall risk with weighted aggregation
Health_System_Stress,Triangular,,,,0.10,0.35,0.80,,0.7,Stress index from facility load and supply shortages
Border_Clearance_Days,Triangular,,,,1,3,10,,0.5,Average time to clear shipments at border posts
```

---

## Appendix C. Interpretation Guide
- **Histogram**  
  Read the shape for skew, spread, and multi-modality that might suggest sub-scenarios.

- **CDF**  
  For any target t, the vertical value at t is P(outcome ≤ t). Use to answer percentile questions quickly.

- **Gauge**  
  If modeling risk, set a threshold at the tolerance limit and read exceedance. If modeling success, set at the minimum acceptable performance and read attainment probability.

- **Tornado**  
  Focus stakeholder attention on the top three drivers by absolute correlation. These are your leverage points for mitigation or investment.

- **Compare Overlay**  
  Show how operational decisions or contextual changes shift the full outcome distribution, not just the mean.

---

**Prepared for analysts operating in volatile, resource-constrained environments. Use this app to make uncertainty explicit, decisions traceable, and assumptions auditable.**
