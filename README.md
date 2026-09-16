# Othello Filter™ for Flipper Zero

[![Flipper Zero](https://img.shields.io/badge/Flipper%20Zero-FAP-orange.svg)](https://flipperzero.one/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Framework: Scientific NLP](https://img.shields.io/badge/Model-Scientific%20NLP™-blue.svg)](#trademarks--intellectual-property)

An embedded, portable implementation of the **Othello Filter™** deciban calculation engine on the **Flipper Zero** hardware platform, grounded in **Scientific NLP™** and the **Othello Interrogation Protocol™ (OIP™)**.

---

## 📌 Repository Description (< 350 characters)

> Flipper Zero implementation of Othello Filter™ from Scientific NLP™ & Othello Interrogation Protocol™. Computes sequential deciban (dB) updates from 4-scale P(D|H) & P(D|¬H) inputs with prior odds, dynamic thresholds, 1-step undo, reset, and stealth haptic/sound/LED alerts.

---

## 📖 Overview

The **Othello Filter™** is a sequential Bayesian inference and evidence-weighting model designed to mathematically eliminate the *Othello Error*—the cognitive bias where an observer mistakes physiological arousal, stress, or nervousness for deceit or guilt.

Originally formulated in **Scientific NLP™** by Masahiro Sugaya and operationalized through the **Othello Interrogation Protocol™ (OIP™)**, this application transforms the Flipper Zero into an unobtrusive, field-ready Bayesian profiler. By quantifying observational evidence in **decibans** ($\text{dB} = 10 \log_{10} \frac{P(D \mid H)}{P(D \mid \neg H)}$) using Alan Turing’s *Banburismus* methodology, operators can track probability shifts in real-time and receive covert multimodal notifications (vibration, LED flash, or audio chimes) when predefined decision boundaries are breached.

---

## 📐 Mathematical Foundations

### 1. The 4-Tier Rarity Scale

Every observed nonverbal cue, micro-expression, or autonomic leakage event ($D$) is evaluated against two competing hypotheses:
- **$H$**: Target hypothesis (e.g., concealment, deception, specific knowledge state)
- **$\neg H$**: Baseline alternative hypothesis (e.g., room temperature, baseline stress, benign habit)

Likelihoods are mapped onto a calibrated 4-tier discrete scale:

| Scale Level | Probability ($P$) | Practical Interpretation |
| :--- | :---: | :--- |
| **Common** | $0.5$ | Routine baseline reaction (occurs $\approx 1$ in 2 times) |
| **Uncommon** | $0.1$ | Contextually limited reaction ($\approx 1$ in 10 times) |
| **Rare** | $0.01$ | Distinct atypical reaction ($\approx 1$ in 100 times) |
| **Very Rare** | $0.001$ | Extreme outlier reaction ($\approx 1$ in 1,000 times) |

---

### 2. Othello Filter™ Deciban Matrix

The Weight of Evidence ($W$) added to the running log-odds is calculated as:

$$W = 10 \log_{10} \left( \frac{P(D \mid H)}{P(D \mid \neg H)} \right) \quad [\text{decibans / dB}]$$

| $P(D \mid H) \ \backslash \ P(D \mid \neg H)$ | Common ($0.5$) | Uncommon ($0.1$) | Rare ($0.01$) | Very Rare ($0.001$) |
| :--- | :---: | :---: | :---: | :---: |
| **Common ($0.5$)** | **$0\text{ dB}$** *(Null)* | **$+7\text{ dB}$** | **$+17\text{ dB}$** | **$+27\text{ dB}$** *(Max Leverage)* |
| **Uncommon ($0.1$)** | **$-7\text{ dB}$** | **$0\text{ dB}$** *(Null)* | **$+10\text{ dB}$** | **$+20\text{ dB}$** |
| **Rare ($0.01$)** | **$-17\text{ dB}$** | **$-10\text{ dB}$** | **$0\text{ dB}$** *(Null)* | **$+10\text{ dB}$** |
| **Very Rare ($0.001$)** | **$-27\text{ dB}$** *(Disproof)* | **$-20\text{ dB}$** | **$-10\text{ dB}$** | **$0\text{ dB}$** *(Null)* |

#### Key Geometric Zones:
* **The Diagonal ($0\text{ dB}$)**: The *Othello Error Hot Zone*. Even if a somatic reaction is extremely rare ($P = 0.001$), if it occurs with equal rarity under the baseline hypothesis $\neg H$, its diagnostic value is strictly **$0\text{ dB}$** and discarded.
* **Upper-Right Triangle ($+7\text{ to }+27\text{ dB}$)**: High positive leverage confirming hypothesis $H$.
* **Lower-Left Triangle ($-7\text{ to }-27\text{ dB}$)**: Exculpatory negative evidence rapidly disproving hypothesis $H$.

---

### 3. Prior Odds Initialization (5-Tier Scale)

Before observational sampling begins, a base-rate prior score is registered:

| Level | Confidence Context | Prior Odds $O(H : \neg H)$ | Initial dB Score |
| :---: | :--- | :---: | :---: |
| **Level 1** | Highly Probable / Overwhelming Baseline | $9 : 1$ | **$+10\text{ dB}$** |
| **Level 2** | Moderate Suspicion / Favorable | $3 : 1$ | **$+5\text{ dB}$** |
| **Level 3** | Neutral / Uninformative (50/50) | $1 : 1$ | **$0\text{ dB}$** |
| **Level 4** | Skeptical / Unfavorable | $1 : 3$ | **$-5\text{ dB}$** |
| **Level 5** | Highly Unlikely / Exceptional | $1 : 9$ | **$-10\text{ dB}$** |

---

### 4. Decision Boundaries (Thresholds)

* **Accept Threshold ($\Delta S \ge +20\text{ dB}$ / Posterior Odds $\ge 100 : 1$)**: Single-hypothesis dominance. Evidence reaches $>99\%$ posterior probability, triggering a confirmation lock-in alert.
* **Reject Threshold ($\Delta S \le -20\text{ dB}$ / Posterior Odds $\le 1 : 100$)**: Hypothesis $H$ is purged from working memory.
* **Indeterminate Band ($-10\text{ dB} < S < +10\text{ dB}$)**: Full suspension of judgment; sampling loop must continue without premature convergence.

---

## ⚡ Key Features

1. **Sequential Bayesian Real-Time Engine**: Instantaneous calculation and log-odds addition on an embedded STM32WB55 microcontroller.
2. **Ergonomic D-Pad Input Loop**: Rapid dual-step entry for $P(D \mid H)$ and $P(D \mid \neg H)$ without looking at the screen.
3. **Single-Step Undo (Rollback)**: Revert the previous observation and restore prior state with a single keystroke.
4. **Hard Session Reset**: Instant zeroing/reset back to initial prior odds.
5. **Configurable Multimodal Alerting**:
   - **Vibro**: Covert haptic vibration motor (silent in-pocket operation).
   - **Sound**: Piezo buzzer tone patterns.
   - **LED**: RGB LED notification pulses.
   - **Tactical Stealth**: Vibro + LED mute modes for low-profile field operations.
6. **Persistent Configuration**: Threshold boundaries and notification preferences saved across sessions via Flipper LittleFS storage.

---

## 🎮 Navigation & UI Controls

### Main Calculation View

```
+-----------------------------------+
| OTHELLO FILTER         [Alert:VIB]|
| Score: +13 dB          Step: #04  |
| [P(D|H): Rare ] -> Input P(D|!H)  |
|   UP: Common      DOWN: Rare      |
|   LEFT: Uncommon  RIGHT: V.Rare   |
| Back: Undo        OK(Hold): Reset |
+-----------------------------------+
```

#### D-Pad Probability Mapping
- **`UP`**: **Common** ($P = 0.5$)
- **`LEFT`**: **Uncommon** ($P = 0.1$)
- **`DOWN`**: **Rare** ($P = 0.01$)
- **`RIGHT`**: **Very Rare** ($P = 0.001$)

#### Key Sequences & Shortcuts
| Button Action | Main Screen Context | Settings Screen Context |
| :--- | :--- | :--- |
| **D-Pad Press** | Select likelihood ($P(D \mid H)$, then auto-prompts $P(D \mid \neg H)$) | Navigate settings items / Adjust values |
| **OK (Short Press)** | Confirm selection / Advance step | Toggle selection / Enter sub-menu |
| **Back (Short Press)** | **1-Step Undo** (Reverts last dB increment) | Return to previous view / Cancel |
| **OK (Long Press)** | **Session Reset** dialog (clears score to Prior Odds) | Save & Exit to Main View |
| **Back (Long Press)** | Open **Settings & Thresholds Menu** | Exit to Flipper OS Desktop |

---

## ⚙️ Operational Workflow

```
               [ Start Session ]
                       │
             [ 1. Set Thresholds ]
         (Default: +20 dB / -20 dB)
                       │
           [ 2. Select Prior Odds ]
         (+10, +5, 0, -5, or -10 dB)
                       │
       ┌───────► [ 3. Input P(D|H) ]
       │         (D-pad: C / U / R / VR)
       │               │
       │         [ 4. Input P(D|¬H) ]
       │         (D-pad: C / U / R / VR)
       │               │
       │         [ 5. Compute & Accumulate ]
       │         (Score = Score + Matrix_dB)
       │               │
       │         [ Undo Requested? ] ──Yes──► [ Rollback 1 Step ]
       │               │ No                         │
       │         [ Threshold Breached? ]            │
       │          ├── Over Upper (+20 dB) ──► [ Haptic/LED Lock-In Alert ]
       │          ├── Under Lower (-20 dB) ──► [ Rejection Alert ]
       │          └── Inside Window ──────────┐
       └──────────────────────────────────────┘
```

---

## 🏗️ Technical Architecture

```
applications_user/othello_filter/
├── application.fam          # FAP manifest and build definitions
├── othello_filter.c         # Application entry point & lifecycle dispatch
├── othello_filter_app.h     # Main context, views, and data structures
├── engine/
│   ├── deciban_matrix.c     # Discrete lookup table and math transforms
│   ├── deciban_matrix.h     # Likelihood enum definitions and matrix weights
│   ├── bayes_engine.c       # Accumulator, undo buffer, and threshold evaluation
│   └── bayes_engine.h       # Engine interfaces and state descriptors
├── views/
│   ├── view_main.c          # Primary radar/deciban accumulation screen
│   ├── view_prior.c         # Prior odds selection screen
│   ├── view_settings.c      # Threshold and notification configuration menu
│   └── view_alert.c         # Decision lock-in visual splash
└── assets/
    ├── icons/               # 1-bit monochrome icons for Flipper LCD
    └── notification/        # Notification sequences for vibro/speaker/LED
```

---

## 🛠️ Build and Installation

### Prerequisites
- [ufbt](https://github.com/flipperdevices/flipperzero-ufbt) (Micro Flipper Build Tool) installed and configured.

### Build with uFBT (Recommended)

1. Clone this repository into your development directory:
   ```bash
   git clone https://github.com/your-username/flipperzero-othello-filter.git
   cd flipperzero-othello-filter
   ```

2. Connect your Flipper Zero via USB.

3. Compile and launch directly onto the hardware:
   ```bash
   ufbt launch
   ```

### Manual FAP Deployment
1. Build the standalone `.fap` package:
   ```bash
   ufbt
   ```
2. Copy the resulting binary from `dist/othello_filter.fap` to your Flipper Zero microSD card at:
   ```
   SD Card/apps/Tools/othello_filter.fap
   ```
3. Run the application from **Applications -> Tools -> Othello Filter** on your device.

---

## 📜 Trademarks & Intellectual Property

* **Othello Filter™**
* **Othello Interrogation Protocol™ (OIP™)**
* **Scientific NLP™**

The terms, architectural formulations, and mathematical constructs of the **Othello Filter™**, **Othello Interrogation Protocol™ (OIP™)**, and **Scientific NLP™** are proprietary trademarks and intellectual property of **Masahiro Sugaya**. All rights reserved across applicable jurisdictions.

Third-party concepts, including Turing's deciban metrics and standard Bayesian probability mechanics, remain within the public academic domain.

---

## ⚖️ Disclaimer

This software is developed for cognitive science research, mathematical modeling, and training purposes. It is not an automated or deterministic lie detector and must not be used as authoritative or sole evidence in judicial, legal, medical, or diagnostic proceedings. The authors and copyright holders accept no liability for decisions made using this application.

---

## 📄 License

This project is open-sourced under the [MIT License](LICENSE).
