# LREC26 – Evaluating Model–Human Alignment in Detecting Contentious Language in Historical Corpora

Code and data for the LREC 2026 paper:
**LLMs as Annotators: Evaluating Model–Human Alignment in Detecting Contentious Language in Historical Corpora**

---

## Required files

To run the code in this repository, several files downloaded or created from the **ConConCor** project (https://github.com/cultural-ai/ConConCor) are required:

- Download `Annotations.csv` – annotator IDs and their annotations for each sample  
- Download `mean_alpha_per_annotator_original.csv` – mean pairwise alpha per annotator
- Create `data.csv` – text of each excerpt sample, sample ID, target term in sample, and its compound form (Due to copyright restrictions, the full dataset cannot be uploaded. Please follow the instructions in the ConConCor repository to recreate the dataset that contains the mentioned components.)

---

## Data Preprocessing

- `filtering_ConConCor.ipynb`  
   Filters annotations and annotators that meet the quality requirements.  
   Output: `ready_annotations.csv` with columns `[anonymous_participant_id, extract_id, score, suggestion]`

- `Figure 1: Inter-human agreement.ipynb`  
   Generates Figure 1 using `ready_annotations.csv`, and filters samples that reach full human consensus (all annotators chose 0 or all chose 1) into `1254_ready_samples.csv`. 

- `create_input_dataset.ipynb`    
   Combines filtered annotations with sample texts from `data.csv` and prepares LLM input data.  
   Output: `2090_ready_samples.csv` (full dataset) and `1254_ready_samples.csv` (consensus dataset) with columns: `[extract_id, target, target_compound, text, annotations, score]`

---

## Running the LLMs

Create separate files containing your API keys before running the notebooks.

- `LLM_single_run.ipynb`      
   Loads `2090_ready_samples.csv`, runs GPT-4o or Llama-3-70B once per sample (`temperature = 0`), and converts outputs to binary labels (`0 = not contentious`, `1 = contentious`). Results are saved in `./gpt and llama/` for batch evaluation.

- `LLM_multiple_runs.ipynb`      
   Loads `2090_ready_samples.csv`, uses the original English prompt (`ori-en-CandR`), runs the model multiple times per sample (`temperature = 1`) with the number of runs equal to the number of human annotators, and computes majority vote across runs.

---

## Evaluation

**Single-run outputs**

- `Evaluation-IAA.ipynb`   
   Calculates the pairwise inter-annotator agreement (IAA) between each LLM and each human annotator (only expert, only crowdworker, or mixed).
  
- `Evaluation-accuracy.ipynb`   
   Calculates the accuracy of LLM compared to the majority vote of human annotators for expert-annotated samples, crowdworker-annotated samples, or mixed (all samples).
  
- `Figure 2: confusion matrix.ipynb`   
   Generates the normalized confusion matrix based on the LLM outputs and the majority vote of human annotators.

**Multiple-run outputs**

- `Figure 3: distribution of contentious label proportions per sample.ipynb`   
   Generates a histogram of the distribution of contentious label proportions per sample of humans and LLMs.
  
- `Figure 4&5: per-sample agreement between human and llm.ipynb`   
   Generates scatterplots of per-sample agreement between human annotations and GPT or Llama, and randomly selects 50 samples from 7 strata from a scatterplot due to the level of difference between human and LLM labels.
  
- `Figure 6: selected samples in the strata.ipynb`   
   Generates a scatterplot of the selected samples from the previous step with their target terms and colored by contextual categories.
   
- `Figure 7: per term contentious sample proportion.ipynb`   
   Generates the plot of the proportion of samples labeled contentious for each target term by humans and LLMs.
   
