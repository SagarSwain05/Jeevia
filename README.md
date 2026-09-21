# Jeevia (जीविया)
### Multimodal Healthcare Triage Assistant for Government and Institutional Health Facilities

[![Status: Production Prototype](https://img.shields.io/badge/Status-Production_Prototype-teal.svg)](#)
[![Compliance: Clinical Decision Support](https://img.shields.io/badge/Compliance-Decision_Support_Only-amber.svg)](#)
[![Protocols: AIIMS ATP & IMCI](https://img.shields.io/badge/Protocols-AIIMS_ATP_%26_IMCI-red.svg)](#)
[![Languages: 22 Indian Languages](https://img.shields.io/badge/Languages-22_Indian_Languages-blue.svg)](#)
[![License: MIT](https://img.shields.io/badge/License-MIT-gray.svg)](#)

---

## 1. Overview & Problem Statement

**Jeevia** is a human-in-the-loop multimodal healthcare triage platform engineered specifically for the diverse, high-volume healthcare ecosystem across India. It bridges the critical gap between front-line intake and qualified medical review in facilities ranging from tertiary Government Hospitals and rural Primary Health Centres (PHCs) to industrial-estate occupational health units, public health camps, and university campus infirmaries.

### Challenges Addressed:
* **Severe Patient Load & Surge Volumes**: High patient-to-doctor ratios require instantaneous, deterministic prioritization so that time-critical red flags are surfaced in seconds.
* **Linguistic Diversity**: Frontline patients speak regional languages and dialects; Jeevia integrates 22 Scheduled Indian Languages with voice recording, auto-translation, and local script display.
* **Low Digital Maturity & Paper Records**: Patients frequently arrive with paper lab slips, handwritten OPD prescriptions, or physical blood reports. Jeevia captures these via camera OCR and maps findings directly to clinical parameters.
* **Specialist Scarcity**: Referral options vary drastically by facility. Jeevia's dynamic facility engine restricts referral pathways strictly to on-site specialists or triggers regional tele-referral protocols.

---

## 2. Core Architectural Principles

```
  ┌────────────────────────────────────────────────────────────────────────┐
  │                           PATIENT INTAKE KIOSK                         │
  │   Text Input   │   Voice Recording   │   Document OCR   │   Visual     │
  └───────────────────────────────────┬────────────────────────────────────┘
                                      │
                                      ▼
  ┌────────────────────────────────────────────────────────────────────────┐
  │                    MULTIMODAL EXTRACTION PIPELINE                      │
  │   Regional ASR   │   PaddleOCR 2.6   │   Pixel & Audio Provenance Tag  │
  └───────────────────┬───────────────────────────────┬────────────────────┘
                      │                               │
                      ▼                               ▼
    ┌───────────────────────────────────┐   ┌──────────────────────────────┐
    │   DETERMINISTIC RULES ENGINE      │   │    AI CLINICAL SYNTHESIS     │
    │   - AIIMS Triage Protocol (Adult) │   │    - Jeevia-BioMistral-7B    │
    │   - IMCI Rules (Pediatric)        │   │    - Structured Triage Note  │
    │   - Hard-coded Urgency Tiering    │   │    - Non-diagnostic Summary  │
    └─────────────────┬─────────────────┘   └──────────────┬───────────────┘
                      │                                    │
                      └──────────────────┬─────────────────┘
                                         │
                                         ▼
                      ┌───────────────────────────────────────┐
                      │    CROSS-ENGINE DISAGREEMENT CHECK    │
                      │    (e.g., Voice BP vs OCR Lab BP)     │
                      └──────────────────┬────────────────────┘
                                         │
                                         ▼
  ┌────────────────────────────────────────────────────────────────────────┐
  │                        QUALIFIED CLINICAL REVIEW                       │
  │                  Doctor Dashboard / Supervisor Portal                  │
  │      [Review] ──> [Sign & Accept] │ [Override Flag] │ [Referral]       │
  └────────────────────────────────────────────────────────────────────────┘
```

1. **Human-in-the-Loop (Non-Diagnostic)**: Jeevia never generates standalone diagnoses or prescribes medicines. It synthesizes intake inputs into structured, audit-logged triage notes for qualified Medical Officers (MOs).
2. **Deterministic Rules Engine Separation**: Urgency classification (Tier 1 Critical Red, Tier 2 Semi-Urgent Yellow, Tier 3 Routine Green) is computed strictly by deterministic rule sets (AIIMS Triage Protocol for adults, IMCI for pediatric patients). The LLM is restricted to text summarization and cannot downgrade rule outcomes.
3. **End-to-End Source Traceability & Pixel Provenance**: Every vital sign and lab value displayed in the clinical dashboard contains an interactive provenance tag. Clinicians can click any value to view the exact physical report crop or play the source audio transcript.
4. **Disagreement Detection Engine**: If a patient reports a blood pressure of `160/100 mmHg` via voice intake, but their uploaded paper lab report reads `170/105 mmHg`, the system flags an actionable alert requiring physical verification during examination.
5. **Strict Data Privacy & Audit Trail**: In rural households, phone numbers are commonly shared across family members. Jeevia enforces multi-record candidate disambiguation and logs every clinical view and priority override to an immutable audit trail.

---

## 3. Platform Modules & Pages

| Page | File | Target User | Description |
|---|---|---|---|
| **Public Landing Page** | [`index.html`](file:///Users/sagarswain/Desktop/Jeevia/index.html) | Public / Staff | Platform introduction, facility matrix showcase, and portal launcher with responsive dual navigation. |
| **Staff Authentication** | [`auth.html`](file:///Users/sagarswain/Desktop/Jeevia/auth.html) | Doctors, Supervisors | Staff session authentication with smartcard/credential verification and facility context selector. |
| **Patient Intake Kiosk** | [`patient-intake.html`](file:///Users/sagarswain/Desktop/Jeevia/patient-intake.html) | Patients, ASHA / ANM Workers | Tablet/kiosk data collection engine: Text, Regional Voice, Lab Slip OCR, Skin Lesion/Rash photo, and Proxy Consent. |
| **Doctor Dashboard** | [`doctor-dashboard.html`](file:///Users/sagarswain/Desktop/Jeevia/doctor-dashboard.html) | Medical Officers | Real-time queue, auto-escalation timers, un-clustered modular clinical decision cards, longitudinal trending, and 1-click referral. |
| **Supervisor Dashboard** | [`supervisor-dashboard.html`](file:///Users/sagarswain/Desktop/Jeevia/supervisor-dashboard.html) | Facility Administrators | Facility classification setup, live specialist on-site toggles, threshold calibration, and full audit logs. |

---

## 4. UI/UX & Design System Standards

* **Zero-Emoji Policy**: Replaced all informal emojis with clean, accessible, scalable SVG vector icons (`18×18` and `24×24` standard viewboxes).
* **Typography**: Clean, professional sans-serif type stack (`Inter`, `-apple-system`, `BlinkMacSystemFont`, `Segoe UI`, `sans-serif`) across all 5 portals.
* **Un-Clustered Modular Cards**: The Doctor Dashboard clinical decision-support panel separates clinical disclaimer, patient demographics, conflict alerts, AI notes, rules traces, vitals, OCR findings, longitudinal trending, missing checklist, and follow-up interview questions into distinct, elevated cards with subtle slate borders and generous breathing room.
* **Adaptive Dual Navigation**:
  * **Desktop / Tablet Landscape**: Left side panel with clean icon-text navigation and facility indicators.
  * **Mobile View**: Centric layout with a touch-friendly, horizontal slider navigation bar and minimum `44×44px` touch targets.

---

## 5. Clinical Protocols & Urgency Tiers

Jeevia's triage prioritization strictly maps against established national and international healthcare guidelines:

### Urgency Classifications:
* 🔴 **Critical (Tier 1 - Immediate)**:
  * Acute retrosternal chest pain radiating to arm / jaw (AIIMS ATP)
  * Oxygen saturation ($\text{SpO}_2$) $< 90\%$ (Emergency COPD / Asthma)
  * Systolic BP $\ge 160\text{ mmHg}$ or Diastolic $\ge 100\text{ mmHg}$ with headache or visual disturbance (Pre-eclampsia Red Flag)
  * *Safety feature: Auto-escalates unreviewed critical cases to Senior Medical Officer after 15 minutes.*
* 🟡 **Semi-Urgent (Tier 2 - Within 30–60 min)**:
  * Persistent fever $> 102^\circ\text{F}$ for $> 3$ days in dengue-endemic zone
  * Acute right lower quadrant abdominal pain (evaluating appendicitis / acute abdomen)
  * Severe hyperglycaemia ($\text{Glucose} > 300\text{ mg/dL}$) with osmotic symptoms
* 🟢 **Routine (Tier 3 - Standard Outpatient Order)**:
  * Stable chronic follow-ups (Type 2 Diabetes, controlled hypertension)
  * Stable antenatal visits (routine 3rd trimester check)
  * Mild upper respiratory symptoms without respiratory distress

---

## 6. Repository File Map

```
Jeevia/
├── index.html                  # Public platform landing page
├── auth.html                   # Medical staff authentication portal
├── patient-intake.html         # Multimodal tablet/kiosk intake engine
├── doctor-dashboard.html       # Clinical decision-support dashboard for Medical Officers
├── supervisor-dashboard.html   # Facility administration & specialist availability matrix
├── README.md                   # Comprehensive platform documentation
├── .gitignore                  # Git exclusions (OS files, caches)
├── js/
│   ├── landing.js              # Landing page interactivity & live metric counters
│   ├── auth.js                 # Authentication logic & session storage handling
│   ├── patient.js              # Kiosk multimodal capture, audio recording, & OCR simulation
│   ├── doctor.js               # Queue sorting, auto-escalation timer, clinical cards renderer
│   ├── supervisor.js           # Facility setup, specialist matrix toggles, audit logs
│   └── nav.js                  # Shared mobile slider navigation & responsive behavior
└── styles/
    ├── base.css                # Global CSS variables, typography, colors, resets
    ├── landing.css             # Public landing page styling
    ├── auth.css                # Authentication portal styling
    ├── patient.css             # Kiosk tablet touch-optimized layout
    ├── doctor.css              # Doctor dashboard grid & de-clustered modular cards
    ├── supervisor.css          # Supervisor configuration panels & audit table
    ├── dashboard.css           # Shared dashboard elements & statistics cards
    └── nav.css                 # Responsive slider and side navigation styles
```

---

## 7. Getting Started & Local Setup

Jeevia is built using pure modern web standards (HTML5, CSS3, ES6+ JavaScript) with zero runtime dependencies. It runs seamlessly offline in resource-constrained rural clinics.

### Quick Start:

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/SagarSwain05/Jeevia.git
   cd Jeevia
   ```

2. **Launch via any Local Static Server**:
   ```bash
   # Using Python 3
   python3 -m http.server 3000

   # Or using Node.js
   npx serve .
   ```

3. **Open in Browser**:
   * Landing Page: `http://localhost:3000/index.html`
   * Doctor Dashboard: `http://localhost:3000/doctor-dashboard.html`
   * Patient Kiosk: `http://localhost:3000/patient-intake.html`
   * Supervisor Console: `http://localhost:3000/supervisor-dashboard.html`
   * Staff Auth: `http://localhost:3000/auth.html`

---

## 8. Clinical Safety & Legal Disclaimer

> [!CAUTION]
> **CLINICAL DECISION-SUPPORT SYSTEM ONLY — STRICTLY NON-DIAGNOSTIC PROTOTYPE**
>
> Jeevia is an assistive triage prototype engineered for research, facility workflow optimization, and operational testing. All outputs—including AI summaries, extracted vitals, and deterministic protocol triggers—are presented solely to assist qualified, licensed Medical Officers and healthcare professionals. Under no circumstances does Jeevia provide medical diagnosis, direct clinical treatment, or prescription authority. Ultimate diagnostic and therapeutic accountability remains strictly with the examining clinician.

---

## 9. Author & Contribution

* **Repository**: [https://github.com/SagarSwain05/Jeevia.git](https://github.com/SagarSwain05/Jeevia.git)
* **Author / Maintainer**: Sagar Swain
* **License**: MIT License
