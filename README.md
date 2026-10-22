# PsychoPy Neuropsychological Task Battery

**Ready-to-adapt PsychoPy experiments for neuropsychological assessment (theory of mind, object naming, action naming, text comprehension), plus an offline tool that scores spoken naming responses in Greek.**

![PsychoPy](https://img.shields.io/badge/PsychoPy-2021.1.3-3E8E41)
![Python](https://img.shields.io/badge/Python-3-3776AB?logo=python&logoColor=white)
![Status](https://img.shields.io/badge/status-work%20in%20progress-orange)

## Overview

Each experiment is built in the PsychoPy Builder and shipped both as a Builder file (`.psyexp`, editable in the GUI) and as the exported Python script (`.py`, runnable directly). Stimuli live in plain CSV condition files, so adapting a task to a new stimulus set or language means editing a spreadsheet rather than code.

## Tasks

| Task | Folder | What the participant does | Response | Condition file |
|---|---|---|---|---|
| **Non-verbal Theory of Mind** | `Theory of Mind/` | Watches a first video, a fixation cross, then a second video, and judges whether the second video is a reasonable continuation of the story | Mouse (correct / incorrect) | `videoList.csv` |
| **Object naming** | `Object Naming/` | Sees an image and names the object aloud | Voice (microphone recording) | `imageList.csv` |
| **Action (verb) naming** | `Verb Naming/` | Sees an image of an action and names it aloud | Voice (microphone recording) | `imageList.csv` |
| **Text reading and comprehension** | `Text Reading/` | Reads a short text, then answers multiple-choice questions about it | Mouse (4 answer options per question) | `first.csv`, `QnAs*.csv` |

Stimulus lists in the repository are **templates**: they contain placeholder entries and one example image or dummy video. Replace them with your own materials and update the CSV files (`fname` / `corrName` for naming tasks; `firstVideo`, `secondVideo`, `congruent`, `corrAns` for Theory of Mind).

## Speech analysis for naming tasks

`speechAnalysis/speechAnalysis.py` post-processes the recordings from the two naming tasks. Given a PsychoPy log CSV, it:

1. **Checks the spoken word**: runs CMU Sphinx keyword search (via the `SpeechRecognition` package) with a Greek acoustic model and compares the result with the expected word (`corrName`).
2. **Estimates response latency**: uses [auditok](https://pypi.org/project/auditok/) to detect speech regions in each recording; when exactly one region is found, its start time is taken as the response latency.
3. **Writes an enriched CSV** (original log plus word found, match flag, speech regions, latency) and saves a **waveform plot** with the detected regions beside each recording, so latencies can be verified by eye.

<p align="center">
  <img src="speechAnalysis/test1.jpg" alt="Detected speech region in a naming recording" width="420">
  <img src="speechAnalysis/test2.jpg" alt="Second example of detected speech region" width="420">
</p>

Latencies are approximate and the detection parameters are empirical, which is why the plots are saved for manual inspection. See [`speechAnalysis/README.md`](speechAnalysis/README.md) for installation of the Greek model, usage and caveats.

## Getting started

1. Install [PsychoPy](https://www.psychopy.org/download.html). The experiments were built with **version 2021.1.3**; other versions may need the Builder file re-exported.
2. Open the task's `.psyexp` in the Builder, or run the exported script, for example:
   ```bash
   cd "Object Naming"
   python objectNaming.py
   ```
3. Add your stimuli and edit the task's condition CSV (see the table above). The Theory of Mind task additionally needs your videos in `Theory of Mind/TomVideos/`, with instruction text set in the experiment.
4. Run. A dialog asks for participant and session. Results are saved in a `data/` folder next to the script (`.csv`, `.log`, `.psydat`); the naming tasks also save one `.wav` per trial in a `*_wav` folder alongside the data file.

## Repository structure

```
.
├── Theory of Mind/     ToM.psyexp, ToM.py, videoList.csv, TomVideos/, true.png, false.png
├── Object Naming/      objectNaming.psyexp, objectNaming.py, imageList.csv, objectnaming/
├── Verb Naming/        verbNaming.psyexp, verbNaming.py, imageList.csv, actionImages/
├── Text Reading/       TR.psyexp, TR.py, first.csv, QnAs1-3.csv
└── speechAnalysis/     speechAnalysis.py + README and example plots
```

## Status

Work in progress: the four tasks above are available; further tasks and per-task documentation are planned.

## Credits

Developed by [@JungleHippo](https://github.com/JungleHippo). Experiments are built with [PsychoPy](https://doi.org/10.3758/s13428-018-01193-y) (Peirce et al., 2019). The Greek speech model is by Pantazoglou et al. (see `speechAnalysis/`).
