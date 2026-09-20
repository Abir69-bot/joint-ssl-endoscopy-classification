# SSL-GI: Dual Self-Supervised Learning for Gastrointestinal Image Classification

SSL-GI combines masked image reconstruction and multi-view contrastive learning for gastrointestinal endoscopy image classification. The framework uses a shared ImageNet-pretrained ViT-Small encoder, followed by partial fine-tuning for 23-class classification on Hyper-Kvasir.

## Overview

Limited labeled images and class imbalance make endoscopic image classification challenging. SSL-GI addresses these challenges through two stages:

1. **Self-supervised pretraining:** Jointly learn reconstruction and contrastive objectives using unlabeled images.
2. **Supervised fine-tuning:** Adapt the pretrained encoder to gastrointestinal image classification using labeled images.

## Key Features

- **Shared backbone:** ImageNet-pretrained ViT-Small encoder.
- **Dual objectives:** Masked Autoencoder (MAE) reconstruction and multi-view InfoNCE.
- **Multi-view learning:** Four augmented views per image.
- **Adaptive loss balancing:** Uncertainty-based weighting of pretraining objectives.
- **Partial fine-tuning:** Update the last six Transformer blocks and final normalization layer.
- **Feature fusion:** Add the CLS-token representation to the mean patch-token representation.
- **Class imbalance handling:** Focal loss during downstream classification.
- **Discriminative learning rates:** Train the classification head with a learning rate 10 times that of the unfrozen encoder.

## Architecture

### Stage 1: Dual Self-Supervised Pretraining

Unlabeled endoscopic images are processed through reconstruction and contrastive branches sharing the same encoder.

#### Reconstruction Branch

1. Apply random masking to the input image.
2. Extract representations using the ViT-Small encoder.
3. Reconstruct masked image content using an MAE decoder.
4. Compute the reconstruction loss.

#### Contrastive Branch

1. Generate four augmented views of each image.
2. Extract representations using the shared encoder.
3. Process representations through a projection head.
4. Compute the multi-view InfoNCE loss.

#### Joint Optimization

The architecture diagram summarizes the combined objective as:

**L_total = L_rec + λ × L_InfoNCE**

Where:

- **L_total:** Combined pretraining loss.
- **L_rec:** Reconstruction loss.
- **L_InfoNCE:** Multi-view contrastive loss.
- **λ:** Relative weighting of the contrastive objective in this schematic expression.

The framework uses adaptive uncertainty-based weighting. The exact implemented objective may include additional weighting and regularization terms beyond this schematic.

### Stage 2: Supervised Fine-Tuning

The pretrained encoder is adapted to the downstream classification task:

1. Freeze the earlier Transformer blocks.
2. Fine-tune the last six Transformer blocks and final normalization layer.
3. Combine the CLS-token vector with the average patch-token vector.
4. Pass the combined representation to an MLP classification head.
5. Train using focal loss and discriminative learning rates.

#### Feature Fusion

**z_fused = z_CLS + (z₁ + z₂ + … + z_N) / N**

Where:

- **z_fused:** Combined image representation.
- **z_CLS:** CLS-token representation.
- **z₁ … z_N:** Patch-token representations.
- **N:** Number of patch tokens.

#### Learning-Rate Strategy

**Head learning rate = 10 × Encoder learning rate**

Here, the encoder learning rate applies to the unfrozen encoder parameters.

## Dataset and Experimental Setup

| Setting | Configuration |
|---|---|
| Dataset | Hyper-Kvasir |
| Task | Gastrointestinal endoscopy image classification |
| Number of classes | 23 |
| Training split | 70% |
| Validation split | 15% |
| Test split | 15% |
| Split strategy | Stratified |
| Backbone | ImageNet-pretrained ViT-Small |
| Self-supervised pretraining | 30 epochs |
| Pretraining objectives | MAE reconstruction + multi-view InfoNCE |
| Loss balancing | Adaptive uncertainty-based weighting |
| Fine-tuned encoder components | Last six Transformer blocks and final normalization layer |
| Feature fusion | CLS token + mean patch tokens |
| Classification head | MLP |
| Classification loss | Focal loss |
| Head-to-encoder learning-rate ratio | 10:1 |

## Reported Results

The following results are reported in the project abstract:

| Metric | Value |
|---|---:|
| Accuracy | **88.00%** |
| Macro F1-score | **58.30%** |
| Matthews correlation coefficient (MCC) | **0.8699** |
| Self-supervised pretraining epochs | **30** |

Accuracy and macro F1 should be considered together when assessing performance on the imbalanced classification task.

### Pretraining Efficiency

The abstract compares the proposed 30-epoch pretraining schedule with a 400-epoch single-objective MAE baseline and reports an approximately four-times-smaller backbone.

The proposed schedule uses approximately 13.3 times fewer pretraining epochs. This ratio alone does not establish an equivalent runtime or computational speedup. Such comparisons require measured runtime, hardware details, and per-epoch computational costs.

## Reproducibility

For complete reproduction, the implementation should document:

- Dataset version, class mapping, and split seed.
- Unlabeled pretraining data and separation from held-out evaluation data.
- Input resolution, masking ratio, and augmentation settings.
- Exact uncertainty-weighted loss formulation.
- Batch size, optimizer, learning rates, and scheduler.
- Fine-tuning duration and checkpoint selection.
- Dependency versions, hardware, and random seeds.
- Evaluation commands and per-class results.

Installation and execution commands are not included because the repository code and environment configuration have not been provided.

## Citation and License

Add the verified paper citation and repository license when available. Document dataset usage terms separately.
