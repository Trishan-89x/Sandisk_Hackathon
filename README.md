Die Yield Prediction

SanDisk Cerebrum 2026 Hackathon

A machine-learning solution for predicting semiconductor die failures
from wafer-level and die-level process/test data.

The project develops and compares two progressively richer
classification approaches:

Model A: Die-level features + spatial context

Model B: Die-level features + spatial context + block-level
statistical context

The final solution uses Model B with threshold optimization to improve
failure detection on the highly imbalanced dataset.

Problem Overview

The objective is to predict whether an eligible semiconductor die will
pass or fail.

The supplied dataset contains wafer-level data with hundreds of
die-level measurements. Since failures are relatively rare compared with
passing dies, the problem is treated as an imbalanced binary
classification task.

The solution evaluates:

AUC-PR

Failure precision

Failure recall

Failure F1-score

Confusion matrix

Dataset

Dataset      Wafers   Total dies            Failed             Passed

Training        160      173,099   25,581 (14.78%)   147,518 (85.22%)
Test             40       39,351    8,133 (20.67%)    31,218 (79.33%)

After eligibility filtering:

Dataset      Eligible dies   Failures

Training           154,037      6,519
Test                32,598      1,380

Generated files:

input/
├── train.csv
├── test.csv
└── validation.csv

The raw LSWMD.pkl dataset is not included in the repository because
of its large size.

Approach

Model A --- Die + Spatial Context

Model A uses:

500 die-level features

Die row and column information

Normalized spatial coordinates

Radial distance

Spatial context features

A logistic regression classifier is trained after feature
standardization.

Model B --- Die + Spatial + Block Context

Model B extends Model A with block-level statistical information.

The final Model B uses:

500 die-level features

3 spatial features

32 block-derived features

535 total model features

Block-level features capture local statistical characteristics of
groups/segments of dies and provide information that is not available
from an individual die alone.

Model Performance

Model A vs Final Model B

Metric                 Model A   Final Model B

Accuracy              0.816983    0.970704
AUC-PR                0.525737    0.579702
Failure Precision     0.151520    0.777053
Failure Recall        0.722464        0.431884
Failure F1            0.250503    0.555193

The addition of block-level information substantially improves failure
precision and failure F1.

The final Model B uses a decision threshold of 0.91, selected using
validation data.

Threshold Optimization

Because the problem is imbalanced, the default classification threshold
is not necessarily optimal.

A threshold search was performed to maximize the failure-class F1
score.

Best threshold: 0.91

Validation performance:

Accuracy : 0.970483
Failure Precision : 0.769784
Failure Recall    : 0.439606
Failure F1        : 0.559623
AUC-PR            : 0.573923

The final Model B was then retrained using all available training data
and evaluated on the test set.

Final Model B Results

Threshold         : 0.91
Accuracy          : 0.970704
AUC-PR            : 0.579702
Failure Precision : 0.777053
Failure Recall    : 0.431884
Failure F1        : 0.555193

Confusion Matrix

[[31047   171]
 [  784   596]]

Interpretation:

Correctly predicted passes: 31,047

False positives: 171

False negatives: 784

Correctly predicted failures: 596

Feature Analysis

Model B contains 535 features.

Most influential features

block_segment_std
block_mean
block_segment_mean
block_std_difference
block_high_fraction_115
radial_distance
block_segment_max
block_low_fraction_75
block_segment_min
block_q01

This indicates that block-level statistical characteristics and spatial
position provide important predictive information in addition to
individual die measurements.

Important die-level features

feature_460
feature_85
feature_8
feature_243
feature_282
feature_171
feature_246
feature_33
feature_444
feature_309

Positive coefficients push the prediction toward failure, while negative
coefficients push the prediction toward pass.

Wafer-Level Analysis

The project generates wafer maps for:

Actual failure locations

Model A predicted failures

Model B predicted failures

Model B predicted failure probabilities

A representative wafer used for visualization was:

W_F_0009

For this wafer:

Eligible dies             : 1863
Actual failures           : 49
Model A predicted failures: 353
Model B predicted failures: 31

Generated visualization files:

analysis_output/
├── actual_failure_map.png
├── model_a_prediction_map.png
├── model_b_prediction_map.png
├── model_b_probability_map.png
└── wafer_summary.csv

Project Structure

Die-Yield-Prediction/
│
├── README.md
├── requirements.txt
├── config.yaml
├── generate_data.py
│
├── model_a.py
├── model_b.py
├── optimize_model_b.py
├── final_model_b.py
├── feature_importance.py
├── create_analysis.py
├── wafer_analysis.py
│
├── input/
│   ├── train.csv
│   ├── test.csv
│   └── validation.csv
│
├── model_a_output/
├── model_b_output/
├── final_model_b_output/
└── analysis_output/

Large generated datasets and the raw LSWMD.pkl file should be excluded
from Git using .gitignore.

Installation

Windows

python -m venv venv
venv\Scripts\activate

Install dependencies:

pip install -r requirements.txt

Running the Pipeline

Run the scripts from the project root.

1. Generate datasets

python generate_data.py

2. Train Model A

python model_a.py

Outputs are saved in:

model_a_output/

3. Train Model B

python model_b.py

Outputs are saved in:

model_b_output/

4. Optimize Model B threshold

python optimize_model_b.py

Outputs include:

model_b_output/threshold_results.csv
model_b_output/best_threshold.txt

5. Train and evaluate final Model B

python final_model_b.py

Outputs are saved in:

final_model_b_output/

6. Analyze feature importance

python feature_importance.py

This reports:

Top overall features

Top die-level features

Spatial feature importance

Block-level feature importance

Features pushing predictions toward failure

Features pushing predictions toward pass

7. Create comparative analysis

python create_analysis.py

8. Generate wafer-level visualizations

python wafer_analysis.py

Outputs are saved in:

analysis_output/

Prediction Output

The final prediction file is:

final_model_b_output/final_model_b_test_predictions.csv

It contains:

wafer_id
die_row
die_col
predicted_probability
predicted_label
actual_label

predicted_probability is the estimated probability of die failure.

predicted_label is generated using the optimized threshold of
0.91.

Key Findings

Individual die measurements contain useful predictive information.

Spatial location contributes additional predictive information.

Block-level statistical features provide additional contextual
information.

Model B performs better than Model A on the selected evaluation
metrics.

Threshold optimization is important because the dataset is
imbalanced.

The final threshold of 0.91 substantially improves failure
precision.

Wafer-level probability maps provide a useful way to visualize
predicted failure risk.

Limitations

The reported results are based on the supplied train/test split and the
implemented feature-processing pipeline.

The final test-set performance should therefore be interpreted as
performance on this specific evaluation split.

The raw dataset is large and is intentionally excluded from the Git
repository.

Reproducibility

For reproducibility:

Use the supplied requirements.txt.

Keep the same configuration in config.yaml.

Run scripts from the project root.

Do not modify generated input datasets between pipeline stages.

Keep the same threshold-selection procedure.

Final Submission Summary

Final model

Model B --- Die + Spatial + Block Level

Final operating threshold

0.91

Final test AUC-PR

0.579702

Final test failure F1

0.555193

License

Created for the SanDisk Cerebrum 2026 hackathon/project submission.
