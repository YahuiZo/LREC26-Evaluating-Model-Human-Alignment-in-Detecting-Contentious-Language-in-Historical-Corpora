# LREC26 – Evaluating Model–Human Alignment in Detecting Contentious Language in Historical Corpora

Code and data for the LREC-COLING 2026 paper:

**LLMs as Annotators: Evaluating Model–Human Alignment in Detecting Contentious Language in Historical Corpora**

---

## Required External Data

To run the code, several files from the **ConConCor** project are required:

https://github.com/cultural-ai/ConConCor

Due to copyright restrictions, the full dataset cannot be uploaded here. Please follow the instructions in the ConConCor repository to recreate the dataset.

Required files:

- `data.csv` – text of each news excerpt, sample ID, target term, and compound form  
- `Annotations.csv` – annotator IDs and their annotations  
- `mean_alpha_per_annotator_original.csv` – mean pairwise alpha per annotator

---

## Data Preprocessing

1. **`filtering ConConCor.ipynb`**  
   Filters annotations and annotators based on quality requirements.  
   Output: `ready_annotations.csv`  
   `[anonymous_participant_id, extract_id, score, suggestion]`

2. **`Figure 1: Inter-human agreement.ipynb`**  
   Generates Figure 1 and identifies consensus samples (all annotators choose 0 or 1).  
   Output: `1254_ready_samples.csv`

3. **`create input dataset.ipynb`**  
   Combines filtered annotations with sample texts from `data.csv` and prepares LLM input data.  
   Output:  
   - `2090_ready_samples.csv` (full dataset)  
   - `1254_ready_samples.csv` (consensus dataset)

Columns:  
`[extract_id, target, target_compound, text, annotations, score]`

---

## Running the LLMs

Create separate files containing your API keys before running the notebooks.

### Single Run

**`LLM single run.ipynb`**

- Loads `2090_ready_samples.csv`
- Runs GPT-4o or Llama-3-70B once per sample (`temperature = 0`)
- Converts outputs to binary labels  
  `0 = not contentious`, `1 = contentious`

Results are saved in `./gpt and llama/`.

### Multiple Runs

**`LLM multiple runs.ipynb`**

- Loads `2090_ready_samples.csv`
- Uses the original English prompt (`ori-en-CandR`)
- Runs the model multiple times per sample (`temperature = 1`)
- Number of runs equals the number of human annotators
- Computes majority vote across runs

---

## Evaluation

**Single-run outputs**

- `Evaluation-IAA.ipynb` – pairwise agreement between LLMs and human annotators  
- `Evaluation-accuracy.ipynb` – accuracy against human majority vote  
- `Figure 2: confusion matrix.ipynb` – normalized confusion matrix

**Multiple-run outputs**

- `Figure 3: distribution of contentious label proportions per sample.ipynb`  
- `Figure 4&5: per-sample agreement between human and llm.ipynb`  
- `Figure 6: selected samples in the strata.ipynb`  
- `Figure 7: per term contentious sample proportion.ipynb`
