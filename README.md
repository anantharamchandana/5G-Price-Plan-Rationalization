# 📊 5G Price Plan Rationalization

> Clustering and feature overlap analysis across 87K+ Verizon price plans to identify standard vs. non-standard plan families — supporting 5G network migration at Verizon Business Group

---

## 🔍 Problem

Verizon's network team manages thousands of FWA, Mobility, and IoT price plans. Over time, enterprise customers customized standard plans — changing prices, data limits, roaming settings, or feature sets — creating thousands of near-duplicate plans that were difficult to manage, provision, and migrate to 5G.

**The scale of the problem:**
- ~87,000 total price plans in the system
- ~47,000 belong to the VBG business segment
- ~11,000 are active with revenue and lines
- Of those 11,000 — only ~600 plans account for **90% of revenue and active lines**

This meant the majority of Verizon's business was concentrated in a small subset of plans, surrounded by thousands of near-duplicate variants that had branched off over time.

**The ask:** Identify which non-standard plans belong to the same "family" as standard plans — reducing duplication, simplifying network provisioning, and supporting 5G migration without having to manually re-evaluate every plan.

---

## 🏗️ Approach

Built a two-phase analysis framework:

**Phase 1 — MTAS Feature Overlap Analysis**
Compare non-standard plans against standard baselines using MTAS (Mobile Telephone Addressable System) feature codes as the matching key.

**Phase 2 — Random Forest Clustering**
Apply clustering to group plans by feature similarity across 19 engineered features.

---

## ⚙️ How It Works

### Phase 1 — MTAS Feature Overlap
Step 1: Identify top standard plans by billed lines → baseline plans
↓
Step 2: Extract MTAS codes for each baseline plan
↓
Step 3: Extract MTAS codes for each non-standard plan
↓
Step 4: Compare — does each MTAS ID in the non-standard plan
exist in the baseline plan's feature set?
↓
Step 5: Calculate match percentage
overlap % = matched MTAS IDs / total MTAS IDs in non-standard plan

**Example:**
Standard plan MTAS codes:     (A, B, C, D)
Non-standard plan 27976:      (A, C, D, X, Y)   → 3/5 = 60% overlap
Non-standard plan 51219:      (A, B, C, D)       → 4/4 = 100% overlap
Non-standard plan (unrelated):(X, Y, Z)          → 0/3 = 0% overlap

| Overlap % | Interpretation |
|---|---|
| 90–100% | Effectively a copy of the standard plan |
| 50–89% | Same plan family — partial customization |
| < 50% | Likely a different plan family |
| 0% | No relation to this standard plan |

---

### Phase 2 — Feature Engineering (19 Features)

Built features from plan attributes to enable clustering:

| # | Feature |
|---|---|
| 1 | Data Allowance |
| 2 | Initial Speed Restriction |
| 3 | Initial Priority |
| 4 | Data Throttling |
| 5 | Data Deprioritization |
| 6 | Data Alerting |
| 7 | Mobile Hotspot Allowance |
| 8 | Video Default |
| 9 | Roaming Section |
| 10 | LRA |
| 11 | Canada and Mexico |
| 12 | Travel Pass |
| 13 | SMS MO (Mobile Origination) |
| 14 | SMS MT (Mobile Termination) |
| 15 | Voice MO |
| 16 | Voice MT (Mobile Termination) |
| 17 | ILD |
| 18 | On Demand Suspends |
| 19 | On Demand WG |

---

### Phase 3 — Random Forest Clustering

- Applied Random Forest to group plans by feature similarity across all 19 features
- Started with FWA (~400 plans) — smallest and most manageable group
- Validated approach on FWA before scaling to Mobility and IoT
- Output: plan families — groups of standard and non-standard plans that can be treated as equivalent for provisioning purposes

---

## 📈 Scale

| Dimension | Value |
|---|---|
| Total price plans analyzed | 87K+ |
| Business segment plans | ~47,000 |
| Active plans with revenue | ~11,000 |
| Plans driving 90% of revenue | ~600 |
| Product groups | FWA, Mobility, IoT |
| Features engineered | 19 |
| Initial analysis scope | FWA (~400 plans) |

---

## 🧰 Tech Stack

| Component | Tool |
|---|---|
| Language | Python |
| Data processing | Pandas, NumPy |
| Clustering | Scikit-learn (Random Forest) |
| Feature analysis | MTAS feature overlap scoring |
| Data warehouse | GCP BigQuery |
| Reporting | Looker Studio |

---

## ✅ Results

- Identified that ~600 plans drive 90% of Verizon Business revenue — enabling focused rationalization effort
- Built MTAS overlap scoring to measure plan similarity without manual review
- Engineered 19 features from plan attributes to enable automated clustering
- Validated clustering approach on FWA plans — framework ready to scale to Mobility and IoT
- Directly supported Verizon's 5G network migration by reducing plan duplication and simplifying provisioning

---

## 💡 What I Learned

> The business insight came before the technical solution. Knowing that 600 plans out of 11,000 drive 90% of revenue completely changed the scope — instead of rationalizing everything, the focus became protecting and mapping against that core 600.

MTAS feature matching sounds simple — it is just set intersection — but the challenge was that MTAS codes encode features at different levels of specificity. Two plans with 80% MTAS overlap might still behave differently in practice because the 20% difference includes a critical feature. Getting domain experts to validate clustering outputs was as important as the algorithm itself.

---

## 📝 Note on Code

This analysis was conducted on proprietary Verizon price plan data and cannot be shared publicly. 

---

*Built and owned at Verizon Business Group — VBG Product AI/ML Team*
