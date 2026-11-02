# Reading a Penalty from Broadcast Video

Computer vision analysis of what a football broadcast can reveal about a penalty kick, which methods improve the outputs, and where the evidence remains insufficient. This is the **NUS IND5003 Vision II, Group 19** course project by Yangyi Guo, Zhangenmai Gao, Ruizhe Xu and Zimo Pi.

**[Final report PDF](docs/reports/vision-ii-final-report-2026-10-30.pdf)** · **[Portfolio case study](https://rx422040.github.io/vision-ii.html)** · **[Portable inference release](https://github.com/Gabric-cau/ind5003-vision-ii/releases/tag/v2026.10.28-portable-v5)**

## Project and data

The development data comprise **39 penalties from four 2018 World Cup shoot-outs**: Colombia–England (10), Croatia–Denmark (10), Spain–Russia (9), and Russia–Croatia (10). A separate eight-penalty check uses the 2022 World Cup final. Results from the development data and this separate match are reported separately.

The pipeline combines YOLO ball and person detection, human pose estimation, original/mirrored pose consistency, heel/toe support evidence, optical flow and RANSAC camera-motion compensation, pitch-plane homography, and scoreboard/trajectory outcome rules. Small supervised heads learn from frozen visual and pose features. An output without sufficient support remains `unknown` with a recorded reason.

Original match footage, account configuration and private local paths are not required to view the report. Source videos and model weights are distributed separately from this Git source checkout.

## Results and evaluation boundaries

The final report, dated 30 October 2026, is the current summary. Older dated documents and saved outputs remain a development record and may describe different model/reference versions.

| Evaluation | Result | Interpretation |
|---|---|---|
| Development coverage | Contact time **33/39**, ball trajectory **33/39**, fused outcome **35/39** | An output was produced; coverage is not accuracy. |
| Rule-based, single-reviewer agreement | Kicking foot **12/14** answered cases; goalkeeper direction **19/23** | Conditional agreement on development footage, with unanswered cases retained. |
| Learned kicking foot | Macro-F1 **0.50 → 0.72** | Leave-one-match-out evaluation within the 39-penalty development data. |
| Learned landing events | **125/156** reference events matched in the same **27 comparable windows**; precision **0.74**; count MAE **0.89** steps | A development comparison against one reviewer's event list, not an independent population estimate. |
| Separate 2022 match check | **8/8** executions completed; foot output **5/8**, strict foot accuracy **3/8** (answered cases **3/5**) | Successful execution does not establish generalisation. Outcome and qualitative keeper-height outputs both abstained on **8/8** clips. |

The reference labels come from a single reviewer; inter-reviewer reliability is unknown. The development sample contains only three left-footed kickers. Strict dive height, the ball's final physical position, and the four requested camera-angle timing categories remain unresolved. Slow-motion classification has no confirmed outputs across the 320 camera intervals in the report. Data augmentation did not improve the selected baselines under the reported comparisons.

The separate eight-penalty check is too small to establish reliable performance on new matches. Installation checks, regression tests, saved development agreement and end-to-end completion each answer different questions and are not interchangeable accuracy evidence.

## Reproduce inference

The [portable v5 release](https://github.com/Gabric-cau/ind5003-vision-ii/releases/tag/v2026.10.28-portable-v5) includes the weights, fitted heads, installer and checksums. Download `ind5003_offline_video_v5_20261028.zip` from the release assets and extract it. GitHub's automatic source ZIP is not the model bundle.

Inside `portable_bundle_v5`, on Windows with Python 3.11:

```powershell
py -3.11 verify_bundle.py
py -3.11 install.py --torch cu118
.\.venv\Scripts\python.exe run_video.py --video C:\videos\penalty.mp4 --output runs\penalty_001 --device cuda:0 --threads 2 --batch-size 1
```

Use `--torch cu128` for a supported newer NVIDIA GPU/driver, or `--torch cpu` with `--device cpu`. Installation requires network access; subsequent inference reads local video and weights. See the [complete reproduction instructions](docs/REPRODUCE_PORTABLE_V5_2026-10-28.md) for Linux/macOS, environment separation and validation scope.

Input is one isolated penalty MP4 beginning with its main shooting view, including the approach, strike and visible result. The entry point does not automatically locate all penalties in a complete match and does not require human answers at inference. Select a new output directory for each run. `analysis.json` contains `required_outputs`, `final_feature_table`, stage receipts and timings. `accuracy_validated` remains false for an execution-only run.

Apply the [30 October goal-context hotfix](docs/GOAL_CONTEXT_HOTFIX_2026-10-30.md) to an existing v5 bundle using its supplied updater. It permits pose analysis to continue in qualifying ambiguous-goal scenes while preserving unknown goal geometry. A Git source update alone does not update an already extracted bundle. This is a coverage fix; frozen accuracy results have not been recomputed.

## Code and evidence

| Location | Purpose |
|---|---|
| [`src/`](src/) | Reusable detection, tracking, pose, geometry, feature and inference modules. |
| [`scripts/`](scripts/) | Training, inference, controlled comparisons, reviews and audits. |
| [`tests/`](tests/) | Regression checks for code behaviour, evidence gates and provenance. |
| [`configs/`](configs/) | Dataset manifests and experiment configuration. |
| [`docs/`](docs/) | Protocols, training audits, reproduction records and dated historical reports. |
| [`outputs/fresh_course_experiment/`](outputs/fresh_course_experiment/) | Saved development tables, figures and review artefacts. |

Source hashes, source-video hashes, timestamps, model/code bindings, stage outputs and unknown reasons support traceability. Sparse ground projections are not a complete run-up trajectory, and the last visible ball point is not its physical resting position.

Useful records: [training and deployment](docs/TRAINING_AND_DEPLOYMENT_2026-10-26.md), [2022 full-pipeline evaluation](docs/WORLDCUP_FULL_PIPELINE_EVALUATION_2026-10-27.md), [final augmentation audit](docs/FINAL_AUGMENTED_TRAINING_2026-10-29.md), and [RTX 2060 installation/execution validation](docs/DELL_PORTABLE_V5_VALIDATION_2026-10-28.md).

## Source and report provenance

This repository preserves the original project history and incorporates the team's public implementation updates through [`3fb9d4a`](https://github.com/Gabric-cau/ind5003-vision-ii/commit/3fb9d4a46bc9aed12cc6202a70a99bcb534c7d9a). The [team implementation repository](https://github.com/Gabric-cau/ind5003-vision-ii) hosts the portable releases and development records. Incorporating these commits retains their authorship; the project is group work.

The linked final PDF is the supplied 22-page final file, published without content changes. SHA-256: `af5eea2a08240bdba2790ede83418c8aa31b10ddc967665b6be7f90a3c69fc2f`. Public project summary updated **2 November 2026**.
