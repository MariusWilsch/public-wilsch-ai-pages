---
publish: true
---

# FMM Doha Airport AI Feature Scope - Classification
[[client-archibus]]

**Source:** Mujahid's MoSCoW Requirements (2026-01-26)
**Timeline:** End of March 2026 (~2 months)
**Purpose:** Determine what can be offered in the FMM quotation
**Related:** [Original MoSCoW Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/mujahid-ai-requirements-moscow)

---

## IMPLEMENTED

| ID | Feature | Notes |
|----|---------|-------|
| MH8 | Work Requests via Chat | Core MCP tool exists (`create_work_request`). Voice (STT) is tangent add-on. |

---

## TANGENT (buildable within 2-month timeline)

| ID | Feature | Tracked By | Dependency |
|----|---------|------------|------------|
| MH1 | Asset Classification | #605 | Feasibility assessment in progress |
| MH2 | Asset Hierarchy Creation | #605 | Same as MH1 |
| MH4 | Intelligent Legacy Data Mapping | #373 | Already tracked |
| SH2 | Inconsistent Naming Detection | #605 | Same as MH1 |
| SH6 | Smart Work Order Classification | Extension of MH8 | Ryan: expose trade/category/priority/SLA API fields |

---

## SMART INSIGHT UMBRELLA (tangent data access + untouched analytics definition)

*Planned product direction. Requires: historical data access (Ryan) + analytics definition (what to analyze).*

| ID | Feature | Data Needed |
|----|---------|-------------|
| MH5 | Predictive Maintenance | Work order history, runtime data |
| MH6 | Corrective Action Recommendations | Work order resolutions |
| MH7 | AI Dashboards & Insights | Work order + KPI data |
| MH9 | SLA Breach Prediction | Work orders with SLA deadlines |
| MH10 | Compliance Gap Detection | Inspection/certification schedules |
| SH3 | PPM Recommendations | PPM history + outcomes |
| SH4 | Failure Pattern Detection | Work order history |
| SH5 | RUL Estimation | Asset age + maintenance history |
| CH2 | Root Cause Analysis | Work order resolutions |
| CH3 | Demand Forecasting | Historical demand data |

---

## UNTOUCHED (custom development, beyond 2-month scope)

| ID | Feature | Why Untouched |
|----|---------|---------------|
| CH4 | Auto-Generated Maintenance Procedures | Generative/execution, not analytics |
| CH5 | Technician Recommendation | Workforce management data (different domain) |

---

## UNCLEAR (need Mujahid clarification)

| ID | Feature | Question |
|----|---------|----------|
| MH3 | Auto-Population of Mandatory Fields | **Bulk import** (tangent #373) or **ongoing creation** (untouched - new `create_asset` tool)? |
| SH1 | Duplicate Asset Detection | **During import** (tangent #373) or **ongoing scan** (untouched - database hygiene tool)? |
| CH1 | Manufacturer & Model Intelligence | **Bulk onboarding** (tangent #373 + PDF parsing) or **ongoing inference** (Smart Insight)? |

---

## Summary

| Category | Count | FMM 2-Month Scope |
|----------|-------|-------------------|
| Implemented | 1 | Ready now |
| Tangent | 5 | Buildable by March |
| Smart Insight | 10 | Future direction |
| Untouched | 2 | Not in scope |
| Unclear | 3 | Depends on Mujahid answer |

---

## Next Steps

1. **Clarify with Mujahid:** Resolve the 3 unclear items (MH3, SH1, CH1)
2. **Ryan discussion:** Confirm API fields for SH6 (Smart Work Order Classification)
3. **Quotation positioning:** Lead with implemented (MH8) + tangent items as 2-month deliverables
4. **AI Hosting Costs:** Once feature scope is concrete, calculate infrastructure costs
