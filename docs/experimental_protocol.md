# Experimental protocol

1. Register the research question, task labels, primary metrics, and prompt versions before evaluating the held-out test set.
2. Confirm dataset permissions, ethics requirements, and participant-level identifiers.
3. Create participant-level development and test splits. Keep recordings from one participant in one split only.
4. Standardize audio loading and document all transformations.
5. Run each model with fixed decoding settings. Record checkpoint name, revision, software versions, hardware, prompt, and timestamp.
6. Store raw model responses as well as normalized labels. Preserve errors and abstentions.
7. Evaluate disease labels only against the dataset's diagnostic reference labels. Evaluate speech descriptions separately against appropriate annotations.
8. Repeat inference when the model is stochastic. Measure prompt sensitivity and response consistency.
9. Report per-class results, confidence intervals, class counts, and failure cases.
10. Do not describe the system as clinically validated without prospective external validation and appropriate clinical review.
