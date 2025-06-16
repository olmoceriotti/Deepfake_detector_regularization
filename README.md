# DeepFake Detection and Adversarial Robustness Analysis
## Team
Ceriotti Olmo 2193258  
Gravili Andrea 2180997  

## Overview

This Colab notebook integrates the training of DeepFake detection models and the analysis of their robustness against adversarial attacks. It explores the capability of EfficientNet and its regularized version. To further compare the perfomances, two other techniques are used.

## Setup Environment

1.  **Dependencies:** Required Python packages should be installed from the requirements.txt files when ran in a local environment.
2.  **Execution Environment (Colab/Local):**
    *   The notebook auto-detects if it's running in Google Colab or a local environment.
    *   **Colab:** Mounts Google Drive and configures paths for datasets and models within a `CV` folder in your Drive. Prompts for WandB login.
    *   **Local:** Configures paths relative to a local `./CV` directory. Requires prior WandB login via CLI (`wandb login`).
3.  **Dataset:**
    *   Expects raw image data in `true_dataset` and `fake_dataset_one_source` subdirectories, further split into `train`, `validation`, and `test` sets.
    *  Supports loading preprocessed datasets to speed up subsequent runs. If not found, data is processed from raw images and saved as `.pkl` files.
    *  To download the datasets, the following links are available:
       *  [Pickles](https://drive.google.com/drive/u/0/folders/1HuVkDLCSkjK6LIypwzke6yklx05jEu-6)
       *  [Raw Dataset](https://drive.google.com/drive/u/0/folders/1bVAmb8ShGR9NGFnEkRUhiapKEdAYlXQD)
       *  [Models](https://drive.google.com/drive/u/0/folders/1W6q6EfUUVkemT_wnOJleoJZiPqg19zl1)
    * These files should be organised as follows:
      * The pkl files should be inserted in at the following path: `./data/Dataset_Preprocessed`
      * The raw datasets folder should be insterted at the following path: `./data/Dataset_Raw`
      * The models' pth files should be inserted at the following path: `./models/`
    * All these directories should be set in the same location as the notebook.
## Configuration

Key parameters can be adjusted in the "Globals" cell before execution:

*   `model_name_flag`: Choose the model architecture:
    *   `'efficientnet'`
    *   `'efficientnet_pim'` (Perturbation Invariant Model)
    *   `'efficientnet_freq'` (Frequency-based input)
    *   `'efficientnet_adv'` (Spatial DropBlock)
*   `epochs`: Number of training epochs.
*   `learning_rate`: Optimizer learning rate.
*   `optimizer_name_flag`: Optimizer type (e.g., `'adamw'`).
*   `R_PIM`, `ALPHA_PIM`, `SHALLOW_FEATURE_IDX`: Hyperparameters for the PIM model.
*   `WANDB_PROJECT_NAME`, `WANDB_ENTITY`: Your Weights & Biases project details.
*   `LOAD_PREPROCESSED_DATASETS`: `True` to use saved `.pkl` datasets if available, `False` to reprocess from raw images.

## How to Run

Execute the notebook cells sequentially. The primary workflows are:

1.  **Initial Setup and Data Preparation:**
    *   **Action:** Run cells under "Imports", "Globals", and "Data".
    *   **Outcome:** Sets up the environment, defines dataset paths, loads/preprocesses data, and prepares `DataLoader` instances (`train_loader`, `val_loader`, `test_loader`).

2.  **Training a Model:**
    *   **Action:**
        1.  Configure `model_name_flag` and other training hyperparameters in the "Globals" cell.
        2.  Run all cells in the "Train" section.
    *   **Outcome:** Initializes a WandB run, trains the selected model, logs metrics, and saves model checkpoints (best validation and final) and optimizer states.

3.  **Evaluating on Clean Test Data:**
    *   **Action:** After training (the model is still in memory), run cells in the "Test" -> "Baseline" section.
    *   **Outcome:** Evaluates the trained model on the standard test set and logs detailed performance metrics (accuracy, precision, recall, F1, AUC, confusion matrix) to WandB.

4.  **Evaluating Adversarial Robustness:**
    *   **Action:** After a model is trained or loaded (e.g., from the OOD step), run cells in the "Test" -> "Attack" section.
    *   **Outcome:** Tests the model's robustness against configured adversarial attacks (FGSM, PGD). Logs adversarial accuracy, loss, perturbation norms, Attack Success Rate (ASR), and detailed metrics per attack to WandB. A summary table and visualizations of adversarial examples are also generated and logged.