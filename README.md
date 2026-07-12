# Biomedical ECG Signal Processing, HRV Analysis and Signal Quality Assessment using MATLAB

## Overview

This project presents a MATLAB-based biomedical ECG signal processing and heart rate variability (HRV) analysis workflow using an open-source ECG record from the MIT-BIH Arrhythmia Database. The objective was to preprocess a raw ECG signal, remove common sources of physiological and electrical noise, detect physiologically relevant R-peaks, extract clinically meaningful time-domain HRV metrics, and evaluate the reliability of the extracted signal before interpretation.

The project follows a complete three-phase biomedical signal processing pipeline:

- **Phase 1:** ECG signal import, visualization, preprocessing, and filtering
- **Phase 2:** R-peak detection, RR interval extraction, heart rate estimation, and HRV analysis
- **Phase 3:** Engineering-oriented ECG Quality Assessment using heuristic signal validation metrics

Unlike a conventional HRV analysis workflow that reports physiological metrics directly, this project introduces an additional ECG Quality Assessment stage that evaluates signal reliability using RR interval plausibility, beat-to-beat stability, physiological heart-rate validation, and peak detection consistency before interpreting downstream HRV measurements.

The project combines:

- ECG signal preprocessing
- Frequency-domain inspection
- Digital filter design and validation
- R-peak detection
- RR interval extraction
- Heart rate estimation
- Time-domain HRV analysis (SDNN and RMSSD)
- ECG quality assessment
- Signal reliability scoring using engineering heuristics

The overall workflow demonstrates how signal preprocessing, feature extraction, physiological validation, and engineering-based quality assessment can be integrated into a single biomedical signal processing pipeline using MATLAB.



## Objective
The main goals of this project were to:

- import and visualize a raw ECG record in MATLAB from the MIT-BIH Arrhythmia Database
- preprocess the ECG signal using filtering techniques
- remove baseline wander, high-frequency noise, and power-line interference
- design an ECG preprocessing pipeline to suppress common physiological and electrical noise
- preserve ECG morphology while improving signal quality
- detect R-peaks automatically
- compute RR intervals and heart rate
- perform time-domain HRV analysis using SDNN and RMSSD
- Assess ECG signal reliability using an engineering-based ECG Quality Assessment framework before interpreting HRV metrics



## Dataset
- **Source:** MIT-BIH Arrhythmia Database
- **Record used:** 100
- **Sampling frequency:** 360 Hz
- **Signal duration:** ~30 minutes

The raw ECG data was first downloaded from PhysioNet / MIT-BIH, converted into a readable `.csv` format using Python and terminal-based processing, and then imported into MATLAB for analysis.



## Project Workflow

### Phase 1: ECG Signal Import and Preprocessing
1. Downloaded open-source ECG signals from the MIT-BIH Arrhythmia Database
2. Converted the raw record into a MATLAB-readable `.csv` file
3. Imported the signal into MATLAB and resolved toolbox / directory setup issues
4. Visualized the ECG in the time domain and configured sample rate and time units
5. Selected a representative 10-beat region of interest for filtering validation
6. Applied ECG preprocessing filters:
   - **High-pass filter (0.5 Hz)** to remove baseline wander
   - **Low-pass filter (40 Hz)** to suppress high-frequency noise
   - **Band-stop filter (59–61 Hz)** to remove 60 Hz power-line interference
7. Verified that ECG morphology was preserved after filtering
8. Recomputed average RR interval and heart rate after filtering

### Phase 2: R-Peak Detection and HRV Analysis
1. Exported the processed ECG signal from MATLAB Signal Analyzer
2. Used MATLAB code to detect R-peaks from the final filtered ECG
3. Converted peak sample locations into time values
4. Computed RR intervals across the full ECG record
5. Estimated average heart rate
6. Performed time-domain HRV analysis using:
   - **SDNN**: Standard Deviation of Normal-to-Normal Intervals
   - **RMSSD**: Root Mean Square of Successive Differences

### Phase 3: ECG Quality Assessment
1. Computed RR intervals from detected R-peaks
2. Checked for physiologically implausible RR intervals
3. Evaluated beat-to-beat RR stability
4. Estimated average heart rate
5. Assessed peak detection consistency using the RR coefficient of variation
6. Combined heuristic quality metrics into a cumulative ECG Quality Score
7. Classified recordings as:
• Good Quality
• Moderate Quality
• Low Confidence
8. Generated an engineering-oriented quality summary before downstream HRV interpretation

## ECG Preprocessing Pipeline
The ECG preprocessing pipeline consisted of:

- **High-pass filter (0.5 Hz)**  
  Used to remove baseline wander caused by breathing, motion, and slow drift.

- **Low-pass filter (40 Hz)**  
  Used to suppress high-frequency noise and muscle-related interference.

- **Band-stop filter (59–61 Hz)**  
  Used in place of a dedicated notch filter to suppress 60 Hz power-line interference present in the US-recorded dataset.

Higher-order harmonics were not separately filtered because they were already sufficiently attenuated by the low-pass stage.



## Peak Detection Parameter Validation
A sensitivity analysis was performed on the peak detection algorithm to study the effect of threshold settings.

When the detection parameters were relaxed from:
- `MinPeakHeight = 0.8`
- `MinPeakDistance = 200`

to:
- `MinPeakHeight = 0`
- `MinPeakDistance = 20`

the number of detected peaks increased from **2265** to **9966**.

This demonstrated that the tuned threshold settings effectively suppressed false detections arising from P waves, T waves, and residual noise while preserving physiologically relevant R-peaks.

---

## Results

### Heart Rate
- **Average RR interval before filtering:** ~0.81 s
- **Average heart rate before filtering:** 74.07 BPM
- **Average heart rate after filtering:** 73.87 BPM

The negligible change in estimated heart rate before and after filtering suggests that the filtering pipeline improved signal quality without distorting the underlying physiological rhythm.

### HRV Metrics
Time-domain HRV analysis on the filtered ECG yielded:

- **SDNN = 59 ms**
- **RMSSD = 73.9 ms**
- **Average Heart Rate = 73.9 BPM**

## ECG Quality Assessment

Following HRV analysis, an engineering quality assessment layer was implemented to evaluate whether the detected ECG signal was sufficiently reliable for downstream physiological interpretation.
Instead of attempting clinical diagnosis, this stage estimates signal reliability using a series of heuristic quality indicators derived from the detected RR intervals.

The assessment combines four complementary metrics.

### RR Interval Plausibility
RR intervals shorter than 0.3 s or longer than 2.0 s are considered physiologically implausible and may indicate missed or false R-peak detections.

### Beat-to-Beat Stability
Large differences (>200 ms) between consecutive RR intervals are treated as potential detector instability or residual signal corruption.

### Average Heart Rate Validation
The estimated heart rate is compared against a heuristic physiological range of 40–180 BPM.
Values outside this range contribute additional penalty points to the overall quality score.

### Peak Detection Consistency
The RR Coefficient of Variation (CV) is computed to estimate detector consistency.
Unlike the previous quality metrics, elevated RR variability contributes only a minor penalty because higher RR variability may naturally occur in healthy individuals.

The cumulative heuristic score is converted into one of three engineering confidence levels:
- Good Quality
- Moderate Quality
- Low Confidence

### ECG Quality Assessment Results

- Bad RR Intervals: 0.00%
- Large RR Jumps: 2.25%
- RR Coefficient of Variation: 0.074
- Average Heart Rate: 75.24 BPM
- Quality Score: 0
- Quality Label: Good Quality

### Interpretation
The average heart rate of 73.9 BPM is consistent with a normal resting rhythm for this ECG record.

An **SDNN of 59 ms** indicates a normal level of overall beat-to-beat variability across the recording, suggesting the presence of natural fluctuations in cardiac rhythm rather than an excessively rigid heart rate pattern.

An **RMSSD of 73.9 ms** indicates a reasonable amount of short-term beat-to-beat variability, reflecting meaningful variation in consecutive RR intervals and demonstrating that the extracted ECG features capture physiological information beyond average heart rate alone.

> Note: These HRV values are interpreted only as signal-analysis results for this dataset and are not intended as a clinical diagnosis.

The ECG quality assessment further indicated that the detected R-peaks were highly reliable. No physiologically implausible RR intervals were identified, beat-to-beat stability remained high, and the estimated heart rate fell within the expected physiological range. The resulting quality score of 0 classified the recording as Good Quality, providing additional confidence in the downstream HRV analysis.

---

## MATLAB Scripts

### `scripts/Day1_Raw_ECG.m`
Contains:
- ECG import
- raw ECG plotting
- initial visualization and exploration of the signal

### `scripts/ECG_HRV_Analysis.m`
Contains:
- loading the processed ECG signal
- R-peak detection using `findpeaks`
- RR interval extraction
- average heart rate calculation
- SDNN and RMSSD calculation
- RR interval tachogram and histogram generation

### `scripts/Phase3_ECG_Quality_Assessment.m`
Contains:
- RR interval plausibility assessment
- Beat-to-beat RR stability analysis
- Average heart-rate validation
- RR Coefficient of Variation (CV) calculation
- ECG quality scoring
- ECG quality classification
- ECG quality summary generation

---

## MATLAB Documentation

### `docs/ECG_Filtering_Workflow.mldatx`  
MATLAB Signal Analyzer workflow file containing the ECG preprocessing pipeline, intermediate filtering stages, and figure generation workflow.

### `docs/ECG_Quality_Assessment_Workflow.mlx`
MATLAB Live Script documenting the complete ECG Quality Assessment workflow, including RR interval plausibility checks, beat-to-beat stability analysis, heart rate validation, RR coefficient of variation analysis, heuristic quality scoring, and engineering interpretation of signal reliability.

---

## Repository Structure

```text
ECG-Signal-Processing-and-HRV-Analysis-MATLAB/
│
├── README.md
├── scripts/
│   ├── Day1_Raw_ECG.m
│   ├── ECG_HRV_Analysis.m
│   └── Phase3_ECG_Quality_Assessment.m
│
├── data/
│   ├── ecg100.csv
│   └── ProcessedSignals.mat
│
├── figures/
│   ├── 01_raw_ecg_full_signal.png
│   ├── 02_raw_ecg_first_2000_samples.png
│   ├── 04_filtered_ecg_full_signal.png
│   ├── 05_filtered_ecg_first_2000_samples.png
│   ├── 06_detected_r_peaks.png
│   ├── 07_effect_of_peak_detection_threshold.png
│   ├── 08_rr_interval_tachogram.png
│   ├── 09_rr_interval_histogram.png
│   │
│   └── signal_analyzer_filtering_workflow/
│       ├── 01_raw_ecg_roi_with_peak_annotations.png
│       ├── 02_lowpass_filter_output.png
│       ├── 03_lowpass_highpass_filter_output.png
│       ├── 04_lowpass_highpass_notch_filter_output.png
│       ├── 05_final_filtered_ecg_with_r_peak_annotations.png
│       ├── 06_raw_to_lowpass_comparison.png
│       ├── 07_raw_to_lp_to_hp_comparison.png
│       ├── 08_full_filtering_pipeline_comparison.png
│       └── 09_raw_vs_final_filtered_comparison.png
│
└── docs/
    ├── ECG_Filtering_Workflow.mldatx
    └── ECG_Quality_Assessment_Workflow.mldatx
```
  
---

## Figures

Figure set for this repository:

### Main project figures

1. Raw ECG full signal  
   `figures/01_raw_ecg_full_signal.png`

2. Raw ECG first 2000 samples  
   `figures/02_raw_ecg_first_2000_samples.png`

3. Filtered ECG full signal  
   `figures/04_filtered_ecg_full_signal.png`

4. Filtered ECG first 2000 samples  
   `figures/05_filtered_ecg_first_2000_samples.png`

5. Detected R-peaks on filtered ECG  
   `figures/06_detected_r_peaks.png`

6. Effect of peak detection threshold on ECG feature extraction  
   `figures/07_effect_of_peak_detection_threshold.png`

7. RR interval tachogram  
   `figures/08_rr_interval_tachogram.png`

8. RR interval histogram  
   `figures/09_rr_interval_histogram.png`

### Signal Analyzer filtering workflow figures

9. Raw ECG ROI with peak annotations  
   `figures/signal_analyzer_filtering_workflow/01_raw_ecg_roi_with_peak_annotations.png`

10. Low-pass filter output  
    `figures/signal_analyzer_filtering_workflow/02_lowpass_filter_output.png`

11. Low-pass + high-pass filter output  
    `figures/signal_analyzer_filtering_workflow/03_lowpass_highpass_filter_output.png`

12. Low-pass + high-pass + notch filter output  
    `figures/signal_analyzer_filtering_workflow/04_lowpass_highpass_notch_filter_output.png`

13. Final filtered ECG with R-peak annotations  
    `figures/signal_analyzer_filtering_workflow/05_final_filtered_ecg_with_r_peak_annotations.png`

14. Raw to low-pass comparison  
    `figures/signal_analyzer_filtering_workflow/06_raw_to_lowpass_comparison.png`

15. Raw to low-pass to high-pass comparison  
    `figures/signal_analyzer_filtering_workflow/07_raw_to_lp_to_hp_comparison.png`

16. Full filtering pipeline comparison  
    `figures/signal_analyzer_filtering_workflow/08_full_filtering_pipeline_comparison.png`

17. Raw vs final filtered comparison  
    `figures/signal_analyzer_filtering_workflow/09_raw_vs_final_filtered_comparison.png`

---

## References
The HRV interpretation used in this project was informed by commonly cited standards and literature, including:

- Task Force of the European Society of Cardiology and the North American Society of Pacing and Electrophysiology, **Heart Rate Variability: Standards of Measurement, Physiological Interpretation, and Clinical Use**, 1996
- Malik et al., HRV standards and biomedical signal processing literature

---

## Future Improvements

Potential future extensions include:

- Frequency-domain HRV analysis (LF/HF power)
- Pan-Tompkins or wavelet-based QRS detection
- Multi-record validation using additional MIT-BIH datasets
- Signal Quality Index (SQI) implementation based on published literature
- Arrhythmia feature extraction and beat classification
- Machine learning-based ECG quality prediction
- Integration with a real-time biomedical monitoring dashboard
