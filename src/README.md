# Source code layout

- `audio/`: loading, validation, resampling, and segmentation.
- `models/`: model adapters for direct-audio and ASR-to-text pathways.
- `evaluation/`: metrics, confidence intervals, and prompt consistency.
- `utils/`: configuration, logging, and reproducibility helpers.

Keep model-specific code separate. Each adapter should return the raw response and a normalized label, without discarding invalid or uncertain outputs.
