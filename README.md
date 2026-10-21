# How to Use the Rwanda Seasonal Agricultural Survey 2024 Notebook

This guide explains how to set up and run `Rwanda_Seasonal_Agricultural_Survey_2024_RSAS.ipynb`.

## 1. Project overview

The notebook analyzes crop-production records from Rwanda's Seasonal Agricultural Survey (SAS) 2024. It compares traditional machine learning models with a deep learning model, a custom tabular ResNet.

The workflow includes:

- Loading crop-production data for Seasons A, B, and C
- Checking the survey codebook and target variable
- Cleaning data and exploring patterns
- Splitting data by group to reduce household overlap between train, validation, and test sets
- Auditing possible target leakage
- Training and comparing models
- Evaluating results with metrics such as PR-AUC, precision, recall, F1, and confusion matrices
- Saving model files, experiment results, and other outputs

**Important:** The notebook uses `s2q9` as its target column, but you must confirm the exact question wording and response codes in the official survey codebook before interpreting the results as improved-seed use.

## 2. What you need

- The notebook file: `Rwanda_Seasonal_Agricultural_Survey_2024_RSAS.ipynb`
- The SAS 2024 crop-production CSV files for Seasons A, B, and C
- The matching metadata or codebook files
- Python with Jupyter Notebook, or a Google Colab account
- A stable internet connection for package installation and, if using Colab, loading files from Google Drive

The notebook uses libraries such as pandas, NumPy, scikit-learn, Matplotlib, Seaborn, joblib, TensorFlow/Keras, and optionally Weights & Biases.

## 3. Option A: Run in Google Colab

1. Upload the notebook to Google Drive, or open it in Google Colab.
2. Make sure the SAS 2024 dataset is available in your Google Drive.
3. Run the notebook cells from top to bottom.
4. When prompted, grant Colab permission to access your Drive.
5. Check the data path in the configuration section before running the data-loading cells.
6. Keep the browser tab open while model training runs.

In Colab, the notebook is configured to use:

- Output folder: `/content/drive/MyDrive/Rwanda_SAS_2024`
- Data folder: `/content/drive/MyDrive/Rwanda_SAS_2024/rsas_data`

Place the dataset under the configured data folder, or change the configuration to match where you stored it.

## 4. Option B: Run locally

1. Install Python and Jupyter Notebook or JupyterLab.
2. Put the notebook in your project folder.
3. Create a `data` folder in the same directory.
4. Put the extracted SAS 2024 CSV files and codebook files in `data`, keeping their folder structure if possible.
5. Install the required packages:

   ```bash
   pip install "scikit-learn>=1.3" pandas numpy matplotlib seaborn joblib wandb tensorflow jupyter
   ```

6. Start Jupyter:

   ```bash
   jupyter notebook
   ```

7. Open `Rwanda_Seasonal_Agricultural_Survey_2024_RSAS.ipynb` and run the cells in order.

TensorFlow setup can vary by operating system and hardware. If installation fails, use a compatible Python/TensorFlow version or run the notebook in Google Colab.

## 5. Configure the notebook

In **Section 1: Setup and configuration**, there is one main configuration cell. Review it before running the notebook.

| Setting | Purpose |
|---|---|
| `TARGET_COL` | Column the model is trained to predict. The current value is `s2q9`. |
| `TARGET_MEANING` | Human-readable description of the target. Replace the placeholder with the exact wording from the codebook. |
| `POSITIVE_LABEL` | Which target class is treated as positive for precision, recall, and PR-AUC. Confirm the class spelling and codebook meaning. |
| `MANUAL_DROP` | Columns excluded from modelling because they may reflect outcomes recorded after the target. Check these against the codebook. |
| `QUICK_MODE` | Set to `True` for a short test run. Set to `False` for the full run. |
| `NN_RANDOM_TRIALS` | Number of extra random-search trials for the neural network. |
| `USE_WANDB` | Enables offline Weights & Biases run logging when the package is available. |
| `DATA_ZIP` | Optional path to the source ZIP file. Leave as `None` when data files are already in the configured data folder. |

Do not change the target or positive class without checking the codebook and making sure the rest of the analysis uses the intended label.

## 6. Run the notebook in order

Run every cell from the top to the bottom. The sections are designed to build on earlier variables and outputs.

1. **Setup and configuration:** install or import packages, set random seeds, and define paths.
2. **Load the survey tables:** find and combine crop-production CSV files for Seasons A, B, and C.
3. **Verify the target:** inspect the metadata/codebook for the target's wording and response values.
4. **Build the modelling table:** rename selected fields, clean the target, and prepare features.
5. **Exploratory data analysis:** review class balance and feature patterns.
6. **Group-aware split:** create training, validation, and test sets while keeping related household records together when the required group ID is available.
7. **Leakage audit:** identify features that may reveal the target rather than provide a valid prediction signal.
8. **Preprocessing:** fit data transformations on training folds to reduce data leakage.
9. **Traditional ML experiments:** train and tune models such as Logistic Regression and tree-based models.
10. **Deep learning experiments:** train a custom tabular ResNet with TensorFlow/Keras.
11. **Compare experiments:** review the experiment table and learning curves.
12. **Final evaluation:** select models using validation results and evaluate them on the held-out test set.
13. **Error analysis:** compare performance across available groups, such as season, province, or crop.
14. **Save artifacts:** export model files and experiment outputs.
15. **Report summary and conclusion:** use the generated results to support the project write-up.

If a cell fails, read the full error message, fix the path or dependency issue, and rerun the failed cell and any later cells that depend on it.

## 7. How to read the results

Do not rely on accuracy alone, especially if one target class is much more common than the other.

- **PR-AUC / average precision:** summarizes precision and recall across thresholds; useful when the positive class is less common.
- **Precision:** among records predicted as positive, how many are truly positive.
- **Recall:** among truly positive records, how many the model finds.
- **F1-score:** balances precision and recall.
- **Confusion matrix:** shows correct and incorrect predictions by class.
- **Learning curves and generalization gap:** help show whether the model is overfitting.
- **Group error analysis:** helps reveal whether performance varies across seasons, provinces, or crop groups.

Use validation results to choose models and thresholds. Treat the test set as a final check, not as a way to keep tuning the model.

## 8. Check for data leakage

Very high or near-perfect scores can be a warning sign. Survey skip patterns, fields filled in after the target event, or fields that directly encode the target may let a model learn the answer instead of a useful pattern.

Before reporting strong results:

1. Confirm the target meaning and response codes in the official codebook.
2. Review the leakage-audit output.
3. Check whether any feature is collected after, or as a result of, the target outcome.
4. Review the `MANUAL_DROP` list and revise it only with a clear reason.
5. Rerun the experiments after removing suspect features.
6. Report the final metrics from the revised, leakage-checked run.

Predictive patterns do not prove cause and effect. Results from the 2024 survey may not generalize to other years or settings.

## 9. Output files

The notebook writes outputs to the configured output folder.

In Google Colab, the default is:

`/content/drive/MyDrive/Rwanda_SAS_2024`

When running locally, the default is:

`./outputs`

Depending on which cells complete, outputs may include:

- `experiment_results.csv`
- Saved traditional ML pipeline
- Saved neural network model
- Preprocessing objects and configuration metadata
- Evaluation plots, learning curves, and confusion matrices
- TensorBoard logs and other experiment artifacts

Use the exact filenames printed by the notebook. Output files are created only when the related cells run successfully.

## 10. Common problems

**The notebook cannot find the CSV files**
- Check `DATA_DIR` and `DATA_ZIP` in the configuration cell.
- Confirm the files have been extracted and that the Seasons A, B, and C crop-production tables are present.
- Rerun the data-loading cell.

**The codebook does not show the target**
- Check that the matching metadata/codebook CSV files are present.
- Verify the target column name and the codebook file format.
- Do not assume the target meaning if the codebook cannot confirm it.

**A package import fails**
- Install the missing package in the same Python environment used by Jupyter.
- Restart the kernel and rerun the notebook from the top if needed.

**Training takes too long**
- Set `QUICK_MODE = True` to test the workflow on a smaller sample with fewer search iterations.
- Use `QUICK_MODE = False` for the full experiment and final results.
- Do not report quick-mode results as the final full experiment.

**Google Drive is not available**
- Run the notebook in Colab and approve the Drive access prompt, or use local paths by running it outside Colab.

**The model gets an unusually high score**
- Inspect the leakage-audit results and survey skip patterns.
- Check whether any input fields reveal the target.
- Rerun after removing suspect features before making claims about model quality.

## 11. Data use and privacy

Use the dataset according to the data provider's access and license terms. Do not upload raw survey records, private household data, credentials, or other restricted files to a public GitHub repository. Keep data and large generated model artifacts out of version control unless sharing them is permitted and necessary.

## 12. Data source

National Institute of Statistics of Rwanda (NISR), *Seasonal Agricultural Survey (SAS) 2024*, crop-production tables for Seasons A, B, and C, with associated metadata/codebook. Refer to the official data source and documentation for access terms and exact variable definitions.
