# Notebook notes

`qwen2_audio_zero_shot.ipynb` is the initial Qwen2-Audio prototype supplied for this project.

**Important before interpreting results:** its current prompt predicts `Healthy`, `Dysarthria`, and `Non-Dysarthria`. These are not the same as the proposed disease labels `ALS`, `PD`, and `HC`. Do not report those outputs as ALS/PD diagnoses. Update and validate the task definition, prompt, audio handling, and output parser before running disease-group experiments.
