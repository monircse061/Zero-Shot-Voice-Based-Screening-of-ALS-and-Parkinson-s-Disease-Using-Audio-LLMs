# Zero-Shot Voice-Based Screening of ALS and Parkinson's Disease Using Audio and Language Models

> **Research prototype. Not a medical diagnostic tool.** This repository is for controlled research on whether pretrained language and audio-language models can identify speech patterns associated with amyotrophic lateral sclerosis (ALS) and Parkinson's disease (PD). It must not be used to diagnose, triage, or make treatment decisions.

## Project overview

This project investigates the use of pretrained models in a **zero-shot setting** for voice-based screening research. The initial models of interest are:

- **Qwen2-Audio**: direct audio-language inference.
- **Falcon3-Audio**: direct audio-language inference, subject to confirming the selected checkpoint's input and inference interface.
- **Llama**: text-based inference after an automatic speech recognition (ASR) step, unless an audio-capable Llama variant is selected.

The main research question is:

**Can pretrained models, without task-specific fine-tuning, distinguish voice samples from people with ALS or PD from samples collected from healthy controls?**

The project will evaluate both disease-level predictions and speech-characteristic descriptions. A speech characteristic such as dysarthria is not equivalent to an ALS or PD diagnosis. The analysis must keep these labels separate.

## Research objectives

1. Establish reproducible zero-shot inference pipelines for audio-language and text-language models.
2. Compare direct audio prompting with an ASR-to-text-to-LLM pipeline.
3. Evaluate ALS, PD, and healthy-control (HC) classification where the dataset supports these labels.
4. Examine whether predictions are stable across prompt wording, audio duration, and repeated runs.
5. Report errors, uncertainty, and limitations rather than relying on accuracy alone.

## Proposed experimental design

### Task A: Disease-group screening

Use this task only when the dataset has reliable participant-level diagnostic labels.

| Label | Meaning |
|---|---|
| `ALS` | Participant diagnosed with amyotrophic lateral sclerosis |
| `PD` | Participant diagnosed with Parkinson's disease |
| `HC` | Healthy control participant |

Possible experiments:

- Three-class classification: ALS vs. PD vs. HC.
- Separate binary tasks: ALS vs. HC and PD vs. HC.
- ALS vs. PD only, if sample size and study design justify it.

Do not infer a disease label from a dysarthria label. Dysarthria can occur in different conditions and has multiple causes.

### Task B: Speech-characteristic analysis

Ask the model to describe observable speech characteristics, such as articulation, phonation, speaking rate, pauses, and intelligibility. Treat these as model-generated observations, not clinical findings. Compare them with expert annotations or validated measurements when available.

### Model pathways

| Pathway | Model | Input |
|---|---|---|
| Direct audio | Qwen2-Audio | Audio + prompt |
| Direct audio | Falcon3-Audio | Audio + prompt; verify checkpoint interface |
| ASR + text LLM | Llama | Transcript + prompt |
| Optional comparator | Other audio-language models | Audio + prompt |

A standard text-only Llama checkpoint cannot directly interpret a waveform. In that case, transcribe the recording first and document the ASR model and settings.

## Repository structure

```text
zero-shot-voice-screening-als-pd/
├── configs/
│   └── experiment.yaml
├── data/
│   ├── raw/                 # Keep private or licensed audio out of Git
│   ├── processed/
│   └── metadata/            # De-identified metadata and split files
├── docs/
│   ├── data_card.md
│   └── experimental_protocol.md
├── notebooks/
│   └── qwen2_audio_zero_shot.ipynb
├── prompts/
│   ├── disease_screening.txt
│   └── speech_characteristics.txt
├── results/
│   ├── figures/
│   └── predictions/
├── src/
│   ├── audio/
│   ├── evaluation/
│   ├── models/
│   └── utils/
├── tests/
├── .gitignore
├── requirements.txt
└── README.md
```

## Getting started

### 1. Clone the repository

```bash
git clone https://github.com/<YOUR-USERNAME>/zero-shot-voice-screening-als-pd.git
cd zero-shot-voice-screening-als-pd
```

### 2. Create an environment

```bash
python -m venv .venv
```

Activate it:

```bash
# Linux/macOS
source .venv/bin/activate

# Windows PowerShell
.venv\Scripts\Activate.ps1
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

For GPU inference, install a PyTorch build compatible with your CUDA version. Large audio-language models may require substantial GPU memory.

### 4. Prepare the dataset

- Obtain each dataset from its official source and follow its licence and access conditions.
- Keep original recordings outside version control.
- Create a de-identified metadata file with at least `sample_id`, `participant_id`, `label`, `audio_path`, and `dataset`.
- Use participant-level splits. Recordings from the same participant must not appear in both training/development and test sets.
- Do not upload names, dates of birth, contact details, clinical records, or identifiable voice recordings to a public repository.

See [`docs/data_card.md`](docs/data_card.md) before adding a dataset.

### 5. Run the Qwen2-Audio prototype

Open:

```text
notebooks/qwen2_audio_zero_shot.ipynb
```

The notebook is an initial implementation supplied with this project. Review the model and processor versions, audio loading, prompt, and output parsing before running a full experiment. Update local audio paths to match your environment.

## Initial prompt design

The prompts in `prompts/` are starting points, not validated clinical instruments. Keep the prompt text fixed within each experiment and report the exact prompt in the paper.

For disease-group screening, the model should be instructed to return one of the allowed labels or `UNCERTAIN`. It should not be asked to provide a definitive diagnosis. Include an option to abstain when the audio is unclear or insufficient.

## Evaluation plan

Report, where applicable:

- Accuracy and balanced accuracy
- Macro-precision, macro-recall, and macro-F1
- Per-class precision, recall, and F1
- Confusion matrix
- AUROC and AUPRC for binary tasks
- Sensitivity and specificity
- Coverage and selective performance when the model can abstain
- Prompt consistency across paraphrases and repeated runs

Use participant-level confidence intervals or bootstrap resampling where appropriate. Compare model predictions against the dataset's reference labels. Do not treat a model's explanation as evidence that its prediction is correct.

## Important methodological considerations

1. **Zero-shot must be defined clearly.** No task-specific training or fine-tuning should be performed on the evaluation participants. If examples are included in the prompt, report the setting as few-shot or in-context learning, not zero-shot.
2. **Avoid data leakage.** Split by participant, not by individual audio file.
3. **Control recording conditions.** Document microphone, environment, sample rate, language, task, duration, and preprocessing when available.
4. **Separate disease from symptom labels.** Dysarthria, hypophonia, and voice abnormalities are speech characteristics, not disease diagnoses.
5. **Check class imbalance.** Report class counts and use balanced metrics.
6. **Assess prompt sensitivity.** Evaluate more than one carefully designed prompt, without selecting a prompt using the final test set.
7. **Handle uncertainty.** Preserve invalid, ambiguous, and abstained responses. Do not silently convert them into a class.
8. **Respect privacy.** Voice recordings can be identifying biometric data. Follow ethics approval, consent, data-use agreements, and local regulations.

## Current status

<sub>Initial project setup</sub>

- [x] Define the research direction and initial model list.
- [x] Add the supplied Qwen2-Audio notebook as a prototype.
- [ ] Verify Qwen2-Audio inference on a small, permitted sample.
- [ ] Confirm the exact Falcon3-Audio checkpoint and inference interface.
- [ ] Implement the Llama ASR-to-text pathway.
- [ ] Finalize dataset labels and participant-level splits.
- [ ] Implement structured prediction logging and evaluation.
- [ ] Run prompt-sensitivity and repeatability experiments.
- [ ] Document limitations and prepare a reproducible report.

## Initial literature starting points

The following references were included in the supplied literature notes. Verify bibliographic details and read the full papers before citing them in a manuscript.

- *Linguistic changes in spontaneous speech for detecting Parkinson's disease using large language models*. PLOS Digital Health (2025). DOI: [10.1371/journal.pdig.0000757](https://doi.org/10.1371/journal.pdig.0000757)
- *Detecting neuropsychiatric fluctuations in Parkinson's through assessments of speech and large language model analysis*. npj Parkinson's Disease (2025). DOI: [10.1038/s41531-025-00939-8](https://doi.org/10.1038/s41531-025-00939-8)
- *Using large language models to accelerate communication for eye gaze typing users with ALS*. Nature Communications (2024). DOI: [10.1038/s41467-024-53873-3](https://doi.org/10.1038/s41467-024-53873-3)
- *Qwen2-Audio Technical Report*. arXiv (2024). [arXiv:2407.10759](https://arxiv.org/abs/2407.10759)
- *A Study on Zero-shot Non-intrusive Speech Assessment using Large Language Models*. arXiv (2024). [arXiv:2409.09914](https://arxiv.org/abs/2409.09914)
- *Pathological Speech Detection with In-Context Learning: Exploring Multimodal Large Language Models for Speech Language Pathology*. [arXiv:2503.23873](https://arxiv.org/abs/2503.23873)
- *Zero-Shot Recognition of Dysarthric Speech Using Commercial Automatic Speech Recognition and Multimodal Large Language Models*. [arXiv:2512.17474](https://arxiv.org/abs/2512.17474)

The supplied literature summary notes that direct zero-shot ALS-versus-healthy classification from raw audio is less established than LLM work on Parkinson's speech and ALS communication support. This gap should be verified through a current literature search before making a novelty claim.

## Citation and use

If you use this repository in academic work, cite the original model papers, datasets, and relevant clinical studies. This repository does not claim clinical validity. Predictions must not be used for patient care.
