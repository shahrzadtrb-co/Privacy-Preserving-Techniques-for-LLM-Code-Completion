# Privacy–Utility Trade-off in LLM Code Completion

If you hide sensitive parts of your code before sending it to a code-completion model, how much worse do the completions get? This project measures that trade-off on 20 HumanEval tasks with CodeT5.

![Privacy vs. utility for all 60 completions](privacy_utility.png)

## Setup

- **Data:** the first 20 tasks of HumanEval (`openai/openai_humaneval`, test split), loaded with Hugging Face `datasets`
- **Model:** `Salesforce/codet5-small`, loaded with Hugging Face `transformers`. Beam search with 4 beams, up to 128 new tokens, prompts truncated to 512 tokens
- **Prompt versions:** every task prompt is sent in three versions, giving 60 completions in total
  - **Original:** unchanged
  - **Low obfuscation:** every identifier longer than two characters is renamed to `v0`, `v1`, ... (Python keywords are kept)
  - **High obfuscation:** low obfuscation, plus comments removed, string literals replaced with `"STR"`, and extra blank lines merged

## Metrics

- **Privacy:** normalized Levenshtein distance between the obfuscated prompt and the original prompt (0 = unchanged, 1 = completely different)
- **Utility:** character-level ROUGE-L style F1, based on the longest common subsequence between the generated completion and the canonical solution

## Results

Average scores across the 20 tasks:

| Prompt version   | Privacy | Utility |
| ---------------- | ------- | ------- |
| Original         | 0.000   | 0.228   |
| Low obfuscation  | 0.531   | 0.078   |
| High obfuscation | 0.889   | 0.008   |

Privacy rises steadily with obfuscation, but utility falls much faster. Low obfuscation already cuts utility by about two thirds, and high obfuscation leaves almost nothing the model can use.

## Limitations and what I would change

- **Low obfuscation is stronger than its name suggests.** The renaming regex also rewrites words inside the docstrings, so the model loses most of the natural-language task description, not only the variable names. This partly explains the sharp drop from 0.228 to 0.078. A next version would rename only real code identifiers, using Python's `ast` or `tokenize` module.
- **Weak baseline.** codet5-small is a small model that is not instruction-tuned. Even with the original prompts, utility is only 0.228, and some completions repeat parts of the prompt instead of solving the task. The differences between versions are measured against a low ceiling.
- **Utility measures similarity, not correctness.** Character-level F1 rewards text that looks like the reference solution. Running the HumanEval unit tests (pass@1) would be the stronger measure.
- **Privacy measures change, not leakage.** Edit distance shows how much the prompt text changed, not how much sensitive information a model could still recover from it.
- **Small sample.** 20 tasks, one model and one run, so the numbers are indicative rather than conclusive.

## Repository contents

- `Privacy-Preserving Techniques for LLM Code Completion.ipynb`: the full pipeline, covering data loading, obfuscation functions, completion generation, scoring, the plot and the analysis
- `privacy_utility.png`: the plot shown above

## How to run

```bash
pip install datasets transformers torch python-Levenshtein matplotlib evaluate
```

Open the notebook and run all cells. The model and the dataset are downloaded from the Hugging Face Hub on the first run. No GPU is needed.

## Tech

Python · Hugging Face Transformers and Datasets · PyTorch (as the model backend) · python-Levenshtein · Matplotlib · Jupyter
