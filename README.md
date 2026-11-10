# Uncanny Valley in Human-Robot Interaction: Sensor Fusion Code

Code and result files for master's thesis (M.Eng. Artificial Intelligence for Smart Sensors and Actuators, Technische Hochschule Deggendorf, Technology Campus Cham).

The study measures how people react to four robot videos and one human video. Two sensors are used at the same time:

- **Heart signal:** pulse intervals from a Polar Verity Sense, used to compute RMSSD.
- **Face signal:** webcam video analysed with Py-Feat (facial Action Units AU04, AU05, AU07, AU09, plus Arousal and Valence).

These are compared with the participants' Godspeed questionnaire ratings.

## What is in this repository

```
data_collection/
    MasterScript.py              Runs the experiment (videos, Polar sensor, webcam, questionnaire)
analysis/
    IndividualAnalysis.ipynb     One participant: video to Py-Feat to RMSSD and trial summary
    Combine_and_Analyze.ipynb    Combines all participants into one trial summary file
    Final_Analysis.ipynb         All statistics and the main figures (fig1 to fig3)
    Graphs_Figures.ipynb         Appendix figures (Figure_S1 to Figure_S4)
checks/
    check_facescore_filter.ipynb Checks how many frames the face-quality filter removes
    checkCaptureFPS.ipynb        Checks the real webcam frame rate of each recording
detector_comparison/
    Detector_Comparison_Fresh.ipynb   Runs the Py-Feat detector comparison
    VerifyComparison.ipynb            Computes the correlations between detectors
results/
    final_results/               Output of Final_Analysis and Graphs_Figures
    checks/                      Output of the two check notebooks
    detector_comparison_results/ Output of the detector comparison
```

## Run order

1. `data_collection/MasterScript.py` (recording)
2. `analysis/IndividualAnalysis.ipynb` (once per participant)
3. `analysis/Combine_and_Analyze.ipynb`
4. `analysis/Final_Analysis.ipynb`
5. `analysis/Graphs_Figures.ipynb`

The notebooks in `checks/` and `detector_comparison/` are separate. They can be run any time after step 2.

## The raw data is not included

The webcam videos, the pulse interval files and the participant-level files contain personal data. They are **not published**. For this reason the notebooks cannot be run from this repository alone: they read from a Google Drive folder (`/content/drive/MyDrive/ThesisExperiment_cleann`) that holds the private recordings. To use the code with your own data, change the folder path at the top of each notebook and record data in the same file format.

The result files in `results/` contain only summary numbers per trial, condition or participant number (no names, no video, no raw signals).

## Requirements

- Analysis notebooks were run in Google Colab. The versions printed in `results/final_results/00_run_log.txt` were: Python 3.13.18, pandas 2.2.3, numpy 2.4.6, scipy 1.16.3, matplotlib 3.10.0.
- Py-Feat 2.1.x (the comparison used 2.1.3).
- `MasterScript.py` runs on **macOS only** (it uses the AVFoundation camera backend and VLC installed in `/Applications`). It needs Python with `bleak`, `polar-python`, OpenCV, `pygame` and `tkinter`, and a Polar Verity Sense.

## Result files

`results/final_results/`

| File | Content |
|---|---|
| `00_run_log.txt` | Everything the script printed, library versions, SHA-256 of the input file |
| `01_data_completeness.csv` | Which participant x condition trials were included (True) or excluded (False) |
| `02_descriptives_by_condition.csv` | Descriptive statistics per measure and condition |
| `03_friedman_tests.csv` | Friedman tests across the five conditions, with Holm-adjusted p |
| `04_correlations_pooled.csv` | 55 correlations between sensor measures and Godspeed subscales |
| `05_correlations_robustness.csv` | The same pairs: within participant, within condition, leave-one-participant-out |
| `06_godspeed_by_condition.csv` | Godspeed ratings per condition, with Friedman tests |
| `07_godspeed_reliability.csv` | Cronbach's alpha per Godspeed subscale |
| `08_sensitivity_excluding_trials.csv` | The same tests without participant 5's Human trial |
| `10_heart_vs_camera_correlations.csv` | RMSSD delta compared with each camera measure |
| `11_sensors_vs_godspeed_side_by_side.csv` | Each measure with its correlation to RMSSD delta and to each Godspeed subscale |
| `fig1` to `fig3` (.png) | Main figures |
| `Figure_S1` to `Figure_S4` (.png) | Appendix figures |

`results/checks/`: `facescore_filter_check.csv`, `capture_fps_check.csv`
`results/detector_comparison_results/`: `detector_correlations.csv`, `detector_correlations_log.txt`

## Known notes (please read before running)

- **Participants:** Participant 2 has no pulse interval data and is left out. For participant 1, the Robot 4 trial is excluded (`excluded_cross_participant`). This gives 5 participants in the file, 25 trials, and 24 trials analysed.
- **File names:** Some intermediate files are named `participant_X_..._openface.csv`. This is a legacy name. They contain Py-Feat output, not OpenFace output.
- **Step 09 is optional and was not run.** `Final_Analysis` can compare results before and after a frame-rate correction if a file called `all_participants_trial_summary_BEFORE_fps_correction.csv` exists. No frame-rate correction was applied, so that file does not exist, no `09_...csv` was produced, and the run log says "09 skipped". The file list printed at the end of the log still mentions `09`; ignore that line.
- **Files the scripts write that are not in this repository:** `Final_Analysis` also writes `README_outputs.txt` (a plain file list, which repeats the table above and still mentions the unused `09` file). `Combine_and_Analyze` writes `all_participants_trial_summary.csv` (trial-level data per participant number, kept private) and some older exploratory files (`correlation_results_*.csv`, `descriptive_stats_*.csv`) that the thesis does not use.
- **`Figure_S3_all_55_correlations.png`** was made by an earlier version of `Graphs_Figures.ipynb`. The current notebook only creates `Figure_S3_reported_35_correlations.png`.
- **Old comments in the code:** The opening comments of `IndividualAnalysis.ipynb` and `Combine_and_Analyze.ipynb` still say "6 participants", "30 videos" and "30 rows". The real study has 5 participants with usable data and 25 trials. `Combine_and_Analyze` uses the participant list written in its code (`PARTICIPANT_IDS = [1, 3, 4, 5, 6]`). The first check cell of `IndividualAnalysis` looks for files of participants 1 to 6 and only lists what is missing. These comments were left as they were when the results were produced.
- **`IndividualAnalysis.ipynb`** has extra test cells after the main script (cells 3 to 10, for example checks on participant 4 and 5). They are not needed for the results. The last two print Py-Feat function signatures; the one that imports `Detector` is for a Py-Feat 1.x name and may not work with Py-Feat 2.x.
- **`VerifyComparison.ipynb`:** the first cell runs a separate file `verify_detector_correlations.py` that is not in this repository. The second cell contains the same code, so run the second cell only.
- **`MasterScript.py`:** one comment still calls the analysis script `IndividualAnalysis.py`; it is now the notebook `IndividualAnalysis.ipynb`.
- **Reproducibility:** `Final_Analysis` was run twice (October and November 2026) on the same input file. The two run logs are identical apart from the date and the library versions.

## Licence

MIT, see `LICENSE`. The licence covers the code. It does not cover any participant data, which is not published.
