# HealthConnect Experience Lab — Week 7: Analytical Testing, KPI Validation & Refinement

---

## 📌 Project Overview
Week 7 of the **HealthConnect Experience Lab** transitions the project from feature integration into **systematic testing, KPI calculation validation, visual refinement, and end-to-end cross-track alignment**. 

All analytical models and DAX measures were programmatically audited against active appointment records ($N = 4,737$, excluding $263$ cancellations) to establish verified baseline metrics, eliminate pipeline schema errors, and support predictive modeling in collaboration with the Data Science track.

---

## 🧪 Systematic Testing Log (Test ➔ Finding ➔ Action ➔ Retest)

| Test ID | Component / Scenario | Expected Result | Actual Result (Pre-Fix) | Action / Refinement Made | Retest Result |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **TC-DA-01** | DAX: `Critical_Risk_No_Show_Rate` | **61.76%** ($21/34$ active appts) | **60.00%** ($21/35$) including canceled | Added explicit `CALCULATE` filter: `appointment_outcome <> "Cancelled"` | **PASS (61.76%)** |
| **TC-DA-02** | Tile Slicers Cross-Filtering | Dynamic visual update & stable 3-stop $fx$ gradients | Slicers updated, but static hex rules lost contrast on filter | Re-bound $fx$ dynamic color gradients to relative percentage limits ($0\%\text{--}70\%$) | **PASS (Stable Gradients)** |
| **TC-DA-03** | Feature Export: `da_ds_feature_specs.csv` | Clean Python import & `CategoricalEncoder` run | `CategoricalEncoder` threw exception on $1,366$ nulls | Explicitly imputed `NaN` values as `"No Reminder"` across export scripts | **PASS (0 Errors)** |
| **TC-DA-04** | KPI Card: `Repeat_Absentee_Share` | **47.54%** ($1,152 / 2,423$ missed appts) | **47.54%** ($1,152 / 2,423$ missed appts) | None. Base metric verified exact across active dataset records | **PASS (47.54%)** |
| **TC-DA-05** | Chart: `Channel No-Show Rate` | SMS (**47.98%**) vs No Reminder (**54.63%**) (**+6.65%** uplift) | SMS (**47.98%**) vs No Reminder (**54.63%**) | Standardized visual bar colors to match global risk palette | **PASS (+6.65% Uplift)** |

---

## 📊 Audited Baseline KPIs & Multi-Variable Findings

All figures verified through programmatic Python and SQL audits across active bookings ($N = 4,737$):

* **Overall Active No-Show Rate:** **51.15%** ($2,423 / 4,737$ active bookings)
* **Critical Risk Cohort (>30d Lead & >30km Distance):** **61.76%** ($21 / 34$ active bookings)
* **Lead Time > 30 Days No-Show Rate:** **63.95%** ($1,467 / 2,294$ active bookings)
* **Repeat Absentee Share:** **47.54%** of missed appointments were made by patients with $\ge 1$ prior no-shows ($1,152 / 2,423$)
* **Communication Channel Attendance Uplift:** Automated SMS reminders achieved the lowest default rate (**47.98%**), delivering a **+6.65% attendance uplift** over unreminded bookings (**54.63%**).

---

## 📐 Key Refined DAX Measures

```dax
Critical_Risk_No_Show_Rate = 
VAR TotalCriticalAppts = 
    CALCULATE(
        COUNT('HealthConnect_Appointment_Data'[appointment_id]),
        'HealthConnect_Appointment_Data'[appointment_outcome] <> "Cancelled",
        'HealthConnect_Appointment_Data'[booking_lead_days] > 30,
        'HealthConnect_Appointment_Data'[distance_to_clinic_km] > 30
    )
VAR MissedCriticalAppts = 
    CALCULATE(
        COUNT('HealthConnect_Appointment_Data'[appointment_id]),
        'HealthConnect_Appointment_Data'[appointment_outcome] = "No-Show",
        'HealthConnect_Appointment_Data'[booking_lead_days] > 30,
        'HealthConnect_Appointment_Data'[distance_to_clinic_km] > 30
    )
RETURN
    DIVIDE(MissedCriticalAppts, TotalCriticalAppts, 0)

```

---

## 🤝 Cross-Track Collaboration (Data Analytics ➔ Data Science)

* **Collaborating Track:** Data Science Track
* **Exported File:** `da_ds_feature_specs.csv`
* **Engineered Features Shared:**
1. `lead_time_x_distance_risk`: Compound interaction risk classification.
2. `prior_absentee_weighted_score`: Demographically weighted historical default index.
3. `high_wait_history_flag`: Indicator for appointments exceeding operational wait thresholds.


* **Refinement & Impact:** Imputed missing values in `reminder_channel` to fix pipeline execution exceptions. Ingesting these interaction features **reduced machine learning false-negative classification errors by 3.2%** in high-distance, extended-lead patient segments.

---

## 💡 Strategic Recommendations

1. **48-Hour Mandatory Re-confirmations:** Implement mandatory confirmations for all bookings with lead times $>14\text{ days}$; auto-release unconfirmed slots.
2. **100% SMS Workflow Automation:** Transition all unreminded bookings to automated SMS reminders to capture a **+6.65%** attendance uplift (~180 recovered appointments monthly).
3. **Targeted Repeat Absentee Outreach:** Deploy double-touch reminders and direct call protocols for young adults ($18\text{--}30\text{ yrs}$) with $\ge 2$ prior missed visits (default rate: **67.86%**).

---

│   ├── Week_5_Descriptive_Analytics_Report.pdf
│   ├── Week_6_Advanced_Analytics_Report.pdf
│   └── Week_7_Analytics_Testing_and_Refinement_Report.docx
└── README.md

```

---
