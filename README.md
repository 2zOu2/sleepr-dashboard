# SleepR — Circadian Sleep-Wake Transition Dashboard

**When are stroke recovery patients most vulnerable to waking or falling asleep?**

A flexdashboard built on top of a Bayesian hierarchical Weibull hazard model for sleep–wake transitions, fit to 5-day wrist actigraphy from 50 patients in stroke recovery.

🔗 **Live dashboard:** https://2zOu2.github.io/slepr-dashboard

🧪 **Capstone:** Rollins School of Public Health, Emory University  
👤 **Author:** Zhengyi Ou · MSPH Biostatistics, Class of 2026  
👥 **Advisors:** Emily N. Peterson · George D. Fulk  


---

## What's in here

| File | Purpose |
|---|---|
| `dashboard.Rmd` | Flexdashboard source — 4 tabs (Overview · About the data · Model · Impact) |
| `styles.css` | Custom theme (Inter + JetBrains Mono, teal accent, mobile-responsive) |
| `R/fit_model.R` | Stan fit script (not run at knit time; requires source data) |
| `R/posterior_summaries.R` | Collapses posterior draws into CSVs the dashboard reads |
| `data/posterior_duration_by_hour.csv` | One row per (hour × transition type) |
| `data/participant_deviations.csv` | One row per patient × transition type |
| `stan/circadian_weibull.stan` | Full Stan model code |

> **Source data is not included** — it is governed by the SleepR study data-use agreement.

---

## Dashboard widgets

1. **Circadian duration ribbon** — predicted sleep-episode duration by onset hour, with a 95% credible-interval ribbon and a hover tooltip showing hour, duration, CI bounds, and episode count.
2. **Survival curves** — P(episode ≥ t min) conditioned on start hour, with legend toggles for five representative hours.
3. **Per-participant deviation table** — searchable, sortable DataTable of sleep→wake (SA) and wake→sleep (AS) log-hazard random effects, with inline colour bars.

---

## Model overview

A continuous-time **Weibull proportional hazards** model with:

- **Fixed effects:** 24-hour and 12-hour Fourier harmonics of episode onset hour
- **Random effects:** per-patient log-hazard intercepts + cyclic AR(1) hour-level deviations
- **Inference:** Stan, 4 chains × 2,000 iterations, all R̂ < 1.01
- **Validation:** 97.3% posterior predictive interval coverage (wake-transition model); 1.6 min median duration error (sleep-transition model)

---

## Reproducing the dashboard

```bash
# 1. Install R packages
Rscript -e 'install.packages(c("flexdashboard","plotly","DT","dplyr","tidyr","htmltools","rstan","purrr"))'

# 2. Fit the model (requires source data — run once)
Rscript R/fit_model.R

# 3. Collapse posterior draws into CSVs
Rscript R/posterior_summaries.R

# 4. Knit the dashboard
Rscript -e 'rmarkdown::render("dashboard.Rmd", output_file = "index.html")'
```

---

## Deploying to GitHub Pages

```bash
git add index.html styles.css
git commit -m "deploy dashboard"
git push
# In the GitHub repo: Settings → Pages → Deploy from branch → main · / (root)
```

---

## Why this project matters

Quantifying *when* stroke recovery patients are most vulnerable to fragmented sleep gives clinicians a time-targeted window for intervention — medication scheduling, environmental cues, sleep-hygiene prompts — that a single nightly sleep score cannot. Applied at scale, this shifts rehabilitation from retrospective sleep summaries to prospective, hour-level guidance personalized to each patient's circadian profile.
