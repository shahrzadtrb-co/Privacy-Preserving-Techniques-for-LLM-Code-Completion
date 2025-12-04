# Privacy Utility Tradeoff in Code Completion

This project analyzes the privacy utility tradeoff in code completion as part of an application for the JetBrains internship program in Europe. The study uses the first 20 tasks from the HumanEval dataset and evaluates how different levels of prompt obfuscation affect code generation quality.

## Overview

Three versions of each prompt were tested:

* **Original**
* **Low obfuscation** (identifier renaming)
* **High obfuscation** (renaming, comment removal, string masking)

Completions were generated using the `Salesforce/codet5-small` model.
Privacy and utility were measured for all 60 completions.

## Metrics

**Privacy**
Normalized Levenshtein distance between the obfuscated prompt and the original prompt.

**Utility**
Character level ROUGE-L style F1 score based on the longest common subsequence between the generated completion and the canonical solution.

## Results

Average scores across all 20 tasks:

| Level            | Privacy | Utility |
| ---------------- | ------- | ------- |
| Original         | 0.000   | 0.228   |
| Low Obfuscation  | 0.531   | 0.078   |
| High Obfuscation | 0.889   | 0.008   |

Higher obfuscation provides stronger privacy but reduces the model’s output quality.
Low obfuscation offers a moderate balance between the two.

## What This Project Shows

* How prompt transformations influence model behavior
* How privacy and utility can be measured in code generation tasks
* How obfuscation can protect sensitive code or user input
* How model performance degrades when key structural information is removed

## Repository Contents

* Jupyter Notebook with full code
* Obfuscation functions
* Completion generation using CodeT5
* Privacy and utility metric implementations
* Final scatter plot and analysis

## Skills Used

* Python
* Machine learning model inference
* Text processing
* Metric design and evaluation
* Visualization and analysis

## Usage

Run the notebook end to end.
Dependencies include `datasets`, `transformers`, `torch`, `python-Levenshtein`, and `matplotlib`.

## Contact

For questions or collaboration, feel free to reach out through LinkedIn.
