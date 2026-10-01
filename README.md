# AgriDiag

## Diagnostic performance and sensor reliability for agricultural systems

AgriDiag is a Python/Jupyter research workflow for evaluating visual diagnosis and sensor-data reliability as separate components of an IoT and multimodal LLM agribusiness platform. It audits public datasets, compares supervised image classifiers with local multimodal language models, evaluates robustness and confidence, and measures calibration and missing-data reconstruction errors.

This repository accompanies the manuscript **Quantifying Diagnostic Performance and Sensor Reliability for an Integrated IoT and Multimodal LLM Agribusiness Platform**. It contains the experimental notebook; the source datasets, model weights and complete result archive are not bundled with it. The manuscript is not identified here as an accepted or published article.

[Open the notebook in Google Colab](https://colab.research.google.com/github/erjes/agridiag-code/blob/main/paper_agridiag_multimodal_LLM_local_gpu_T4_v4_9_RAR.ipynb)

## Repository contents and version

| File | Purpose |
| --- | --- |
| `paper_agridiag_multimodal_LLM_local_gpu_T4_v4_9_RAR.ipynb` | Main experimental notebook, including worker code, analysis, checkpoint handling and result export. |
| `README.md` | Setup, execution, experiment scope and interpretation. |
| `Licence.txt` | Full Apache License 2.0 text. |

The notebook implementation is **v4.9**. The SoftwareX metadata describes the initial repository release as **v1**; this is distinct from the notebook's internal experiment version. The implementation inspected for this documentation is available at [commit da9668dca80b1ec8971d5479fce5618b77241c08](https://github.com/erjes/agridiag-code/tree/da9668dca80b1ec8971d5479fce5618b77241c08). This is a source snapshot, not a claim that a `v1` Git tag exists.

The legacy project name `paper_radja_iot_gemini`, experiment identifier `local_v4_1` and run label `public_local_v4_1` are retained for checkpoint compatibility. Some notebook headings refer to the earlier cognitive-workload study plan. They do not change the scope of the completed component evaluations reported in the manuscript.

## Requirements

The supported entry point documented here is **Google Colab with Google Drive mounted**. The current notebook is configured for an NVIDIA T4 or a compatible CUDA GPU when new LLM inference is needed. It checks available GPU memory before starting the model worker.

- A Google account with access to Colab and enough Google Drive storage for datasets, results and model caches.
- A CUDA GPU for new inference in this notebook's GPU worker. Existing results can be inspected without generating new predictions, and many sensor/statistical stages run on the CPU.
- Sufficient host RAM and local disk space for model loading and temporary files, in addition to GPU memory.
- Internet access for installing dependencies and downloading model weights.
- The datasets listed below, placed in the existing Drive project directory.

The notebook installs missing analysis packages and creates an isolated environment for GPU model dependencies. Its recorded GPU installer specifies PyTorch `2.11.0`, torchvision `0.26.0` with CUDA 12.6 wheels, Transformers `5.16.1`, bitsandbytes `0.49.0`, accelerate `1.14.0` and huggingface_hub `1.30.0`. Other analysis dependencies include NumPy, pandas, SciPy, scikit-learn, scikit-image, Pillow, openpyxl, Matplotlib, IPython, ipywidgets and statsmodels. The notebook's installer and exported environment records are the authoritative configuration; not every analysis dependency is pinned.

Inference runs locally in the Colab runtime. A paid Gemini, Groq or OpenAI inference API is not required. Model-provider access conditions and download availability still apply. A warning about unauthenticated Hugging Face downloads is distinct from an inference error.

Free Colab GPU allocation, connection stability and session duration are not guaranteed. The notebook cannot guarantee completion of all experiments within one four-hour session. Its model and classifier scheduling deadlines are approximately 190 and 210 minutes from setup, respectively; these are scheduling safeguards, not a guarantee of total wall-clock duration. This GPU notebook does not automatically substitute CPU inference for unavailable CUDA hardware.

## Datasets and Drive layout

Obtain the original data from the following sources and retain their original units and labels:

| Source | Files used | Persistent source |
| --- | --- | --- |
| Sorghum Disease Image Dataset, version 1 | `Sorghum Disease Image Dataset.zip` or the corresponding extracted image folders | https://doi.org/10.17632/fgb3cmfrg2.1 |
| Low-cost capacitive soil-moisture sensor study | `Laboratory experiments.xlsx`; `Field experiment GN1.xlsx`; `Field experiment GN2.xlsx`; `Field experiment TH.xlsx`; `Field experiment UKR.xlsx` | https://doi.org/10.5281/zenodo.16911016 |
| AgriDataValue IoT Environmental Data | `pilot_2.0.csv`; `pilot_5.0.csv`; `pilot_8.0.csv` | https://doi.org/10.5281/zenodo.18954708 |

Use the existing project folder in **My Drive**:

| Drive directory | Contents |
| --- | --- |
| `paper_radja_iot_gemini/raw_data/sorghum/` | Sorghum image archive or extracted images. |
| `paper_radja_iot_gemini/raw_data/calibration/` | Laboratory and field workbooks. |
| `paper_radja_iot_gemini/raw_data/agridatavalue/` | The three pilot CSV files. |
| `paper_radja_iot_gemini/runs/public_local_v4_1/` | Persistent experiment outputs and checkpoints; created by the notebook. |
| `paper_radja_iot_gemini/model_cache/` | Downloaded model cache; created when needed. |
| `paper_radja_iot_gemini/exports/` | Exported result archives. |

The notebook also recognizes the expected raw files directly under `paper_radja_iot_gemini/raw_data/`, matching the original setup. Keep one intended version of each input: conflicting copies can block input validation. The Colab path for the project is `/content/drive/MyDrive/paper_radja_iot_gemini`.

The three sources are independent. Images and sensor observations are not paired by farm, plant or timestamp. No joint image–sensor fusion evaluation should be inferred from this directory arrangement. Dataset and model licences remain those of their respective providers.

## Running the notebook

1. Open the Colab link above and save a personal copy if required.
2. Select **Runtime > Change runtime type > T4 GPU** when new model predictions or GPU feature extraction are required.
3. Confirm that the raw files and any previous run directory are in the Drive locations above.
4. Select **Runtime > Run all** and authorize the Google Drive mount when prompted. Confirm that Drive mounted successfully before relying on persistent checkpoints.
5. Read stage status messages. The notebook audits inputs and existing outputs before scheduling missing work. The first model download and environment installation may take substantial time.
6. Review the completion tables, logs and final archive. A successful archive export is not proof that every experiment completed.

The notebook writes and uses its own worker scripts. It is not packaged as a separate command-line application, and no standalone `create_grouped_split()` API is provided. Run cells in order so that shared configuration and helper definitions exist before downstream analyses execute.

## Resuming an interrupted session

Keep the **same Google Drive project and run directory** and rerun the notebook from the beginning. Recreating Python variables and checking files are necessary even when expensive computations are already complete.

- A valid cached prediction is reused when its task identity, expected schema and checksum match.
- Completed model tasks are skipped; missing or invalid tasks remain eligible for execution.
- Supervised feature caches and supplementary checkpoints allow supported stages to resume.
- Changes to inputs, prompts, model revisions, seed, configuration or relevant dependencies may invalidate derived results. A configuration conflict should be investigated rather than bypassed.
- Some summary tables and figures may be regenerated from existing predictions. This is different from repeating inference.

Do not delete configuration locks, raw predictions or checkpoint files merely to remove a warning. Back up the complete project/run directory before intentionally changing an experimental configuration. The exported result archive is useful for review, but it is not guaranteed to include every large cache needed for a full computational resume. Retaining the original Drive directory is the preferred continuation path.

## Experiments reported in the manuscript

| Component | Procedure and scope |
| --- | --- |
| Image integrity and similarity audit | Decoded-pixel hashes and 64-bit difference hashes; Hamming-distance threshold four; transitive similarity grouping; cross-label conflicts excluded and recorded. |
| Controlled data split | Seed `20260906`; one retained representative per similarity group; 60 development images and 300 test images, balanced over six disease classes. |
| Supervised visual baselines | Majority baseline; HOG and RGB histogram features with logistic regression; frozen ImageNet ResNet18 features with logistic regression. The two fitted feature pipelines use the same capped training subset. |
| Local multimodal LLMs | `Qwen/Qwen3.5-9B` and `google/gemma-4-12B-it`; structured labels and optional abstention; model selection on development data. |
| Diagnostic metrics | Accuracy, macro-F1, per-class recall, completion, diagnostic coverage and selective accuracy; class-stratified bootstrap intervals and paired comparisons with Holm adjustment. |
| Robustness and input validity | Matched 60-image subset; Gaussian blur, darkening and JPEG compression; separate blank/missing-image probes and repeated-response checks. |
| Exploratory classifier reliability | Training-only grouped five-fold cross-validation; development-selected temperature scaling, Brier score, negative log-likelihood, ECE and risk/coverage analysis. |
| Laboratory calibration | 1,880 readings and 41 soil/probe groups; leave-one-moisture-level-out evaluation of training-mean, linear and quadratic predictors. |
| Field calibration | UKR profile-held-out evaluation of 32 pairs from two physical profiles. Other sheets are inspected, but unresolved units are not treated as valid accuracy evidence. |
| Missing-data reconstruction | Five eligible moisture series; five seeds, three masking fractions, four gap patterns and two reconstruction methods, yielding 600 evaluations. Causal forward filling is distinguished from retrospective time interpolation. |

The six image labels are Anthracnose and Red Rot, Cereal Grain molds, Covered Kernel smut, Head Smut, loose smut and Rust. There is no healthy-image class in this benchmark.

## Additional notebook sections and incomplete evidence

The historical notebook also contains templates or analysis paths for integrated image–sensor cases, device/network/economic evaluation, optional audio, expert assessment, participant trials, NASA-TLX, trust, SUS and qualitative responses. These require additional real observations or participant data. Template files and synthetic pipeline checks are not empirical human-study or deployment evidence.

The v4.9 supplement also implements a development-only forced-choice prompt diagnostic. It is separate from the frozen optional-abstention test comparison and must not be used to relabel original predictions or retrospectively select a better test result. It is not included in the manuscript's completed clean-test table below.

`Run all` can visit these additional sections. `WAITING`, `BLOCKED`, `PARTIAL` or paused statuses must remain visible rather than being converted to successful results. The historical readiness checks cover the broader study plan and can remain incomplete even when the manuscript's narrower component evaluations are complete.

## Reported reference results

These numbers describe the completed archived study, not measurements automatically produced by opening this repository. Reproduction requires the corresponding data, configuration and complete execution or matching validated caches.

| Model | Correct / test images | Accuracy | Macro-F1 | Diagnostic coverage |
| --- | --- | --- | --- | --- |
| Frozen ResNet18 + logistic regression | 274 / 300 | 91.33% | 0.9133 | 100% |
| HOG + color features + logistic regression | 228 / 300 | 76.00% | 0.7603 | 100% |
| Qwen3.5-9B | 77 / 300 | 25.67% | 0.1894 | 100% |
| Gemma4-12B | 14 / 300 | 4.67% | 0.0688 | 18.67% |
| Majority baseline | 50 / 300 | 16.67% | 0.0476 | 100% |

Gemma completed all 300 clean-test responses but abstained on 244 images. Thus, its low overall accuracy is not evidence of missing tasks. The supervised models receive labelled crop training data, whereas these LLM evaluations are zero-shot. The comparison does not isolate model architecture from supervision, precision or prompting.

ResNet18 and expanded reliability analyses were added after inspection of the original test results and are exploratory. Laboratory linear calibration achieved mean group MAE `0.0614 m³/m³`; the UKR linear result was `2.507` percentage points. These endpoints have different units and aggregation. Reconstruction errors concern masked observed readings, not independent physical reference measurements.

## Outputs and export

Within `runs/public_local_v4_1/`, the notebook creates or updates:

| Location | Purpose |
| --- | --- |
| `tables/` | Predictions, metrics, progress and statistical summaries in CSV format. |
| `figures/` | Analysis plots, generally PNG and SVG. |
| `raw/` | Recorded model responses and supporting audit records. |
| `protocol/` | Configuration locks, task manifests, environment information and generated worker scripts. |
| `checkpoints/` | Persistent task/feature/classifier state where implemented. |
| `logs/` | Setup, worker and stage error logs. |
| `supplement_v4_9/` | Supplementary robustness, calibration, cross-validation and reconstruction outputs. |
| `execution_status.json` | Status records for the current execution. |

**The repository's current v4.9 notebook exports RAR, not ZIP.** Its final cell writes a `paper_radja_results_local_gpu_v4_9_*.rar` bundle to the project's `exports/` directory and attempts a browser download. If the browser does not download it, retrieve the archive from Drive. Archive creation does not imply that all earlier stages succeeded. Inspect stage statuses and task counts before using results in a manuscript.

## Troubleshooting

| Symptom | What to check |
| --- | --- |
| Dataset not found | Confirm Drive is mounted and filenames are under the expected raw-data directory. |
| Conflicting input copies | Compare the files and retain the intended version; do not silently substitute another dataset revision. |
| GPU unavailable or insufficient memory | Use a supported CUDA runtime for new model work. Preserve Drive checkpoints and resume when resources are available. |
| Download or package installation stalls | Inspect the setup/worker logs and available disk space, then resume in a new session if needed. Cached complete tasks remain reusable when valid. |
| Runtime disconnects | Reopen the same notebook and Drive directory, then run from the beginning to rebuild in-memory state and inspect checkpoints. |
| Configuration or checksum mismatch | Investigate the changed inputs/configuration or damaged file. Preserve the prior run before starting a distinct experiment. |
| A cell finishes but a stage is incomplete | Read `execution_status.json`, progress tables and error logs. The stage wrapper records failures so other independent work can continue. |

## Interpretation and limitations

This software supports component evaluation rather than validated agronomic advice. It does not establish farm-separated generalization, performance on healthy or unseen diseases, expert-level explanations, image–sensor fusion benefits, network availability or improved human decision-making. Blank-image rejection is distinct from disease recognition. Retrospective interpolation can use future observations and must not be interpreted as a causal real-time alerting method.

The original result ledger includes interrupted CPU and GPU sessions. Pooled timing must not be presented as a controlled model-speed benchmark. A complete fresh run on another environment is not guaranteed to be bit-for-bit identical. Record the source commit, dataset provenance, configuration and exported environment with any replication.

## Support and citation

Report reproducible problems through [GitHub Issues](https://github.com/erjes/agridiag-code/issues). Include the notebook filename, source commit, affected stage, relevant status/log excerpt and runtime hardware. Do not post credentials or private participant data. No dedicated support email is specified here.

Until a publication DOI is available, reference this repository and the exact commit used, and identify the associated manuscript by its title above. Cite the original datasets and model/method sources separately; do not describe the manuscript as published without a bibliographic record.

## Licence

Copyright 2026 AgriDiag contributors.

The original AgriDiag source code and this documentation are licensed under the **Apache License, Version 2.0**. See [Licence.txt](Licence.txt) for the complete terms and the [official licence text](https://www.apache.org/licenses/LICENSE-2.0.txt).

Dataset files, pretrained weights and third-party libraries are governed by their own licences and are not relicensed by this repository. This software licence does not assert ownership of those materials or grant publication rights for a separate manuscript.
