SSL-GI: Dual Self-Supervised Learning for Gastrointestinal Image Classification

SSL-GI combines masked image reconstruction and multi-view contrastive learning to learn representations for gastrointestinal endoscopy image classification. The framework uses a shared, ImageNet-pretrained ViT-Small encoder and adapts it to a 23-class classification task on Hyper-Kvasir.

Overview

Limited labeled data and class imbalance make endoscopic image classification challenging. SSL-GI uses unlabeled endoscopic images during self-supervised pretraining, followed by supervised fine-tuning on labeled images.

The approach jointly optimizes two complementary objectives:

Masked reconstruction: learn image structure by reconstructing masked image content with a Masked Autoencoder (MAE) decoder.

Multi-view contrastive learning: learn representations across augmented views using an InfoNCE objective and a projection head.

Adaptive uncertainty-based weighting balances the objectives during pretraining. Downstream classification uses partial fine-tuning, token feature fusion, focal loss, and discriminative learning rates.

Architecture
<img width="1051" height="520" alt="image" src="https://github.com/user-attachments/assets/a5efe38b-b52d-4d55-b35a-4f681a77bb0a" />

Stage 1: Dual self-supervised pretraining

Prepare randomly masked inputs and four augmented views of each unlabeled image.

Process the inputs through a shared ImageNet-pretrained ViT-Small encoder.

Use an MAE decoder for the reconstruction objective.

Use a projection head for multi-view InfoNCE learning.

Jointly optimize the encoder using adaptive uncertainty-based loss weighting.

The supplied architecture diagram summarizes the joint objective as:

\mathcal{L}_{\mathrm{total}} = \mathcal{L}_{\mathrm{rec}} + \lambda\mathcal{L}_{\mathrm{InfoNCE}}

This is a schematic expression. The abstract specifies learned uncertainty-based weights; the exact parameterization and any additional loss terms must be documented with the implementation.

Stage 2: Supervised downstream classification

Transfer the pretrained encoder to the labeled classification task.

Fine-tune the last six Transformer blocks and the final normalization layer, keeping earlier encoder blocks frozen.

Add the CLS-token representation to the mean patch-token representation:

\mathbf{z}_{\mathrm{fused}} = \mathbf{z}_{\mathrm{CLS}} + \frac{1}{N}\sum_{i=1}^{N}\mathbf{z}_{i}

Pass the fused representation to an MLP classification head for 23-class prediction.

Use focal loss to address class imbalance.

Set the classification head learning rate to 10 times the learning rate of the unfrozen encoder parameters.

Dataset and Experimental Setup

Setting

Description

Dataset

Hyper-Kvasir

Downstream task

Gastrointestinal endoscopy image classification

Number of classes

23

Data split

Stratified 70% training / 15% validation / 15% test

Backbone

ImageNet-pretrained ViT-Small

Additional self-supervised pretraining

30 epochs

Pretraining objectives

Masked reconstruction and multi-view InfoNCE

Loss balancing

Adaptive uncertainty-based weighting

Fine-tuned encoder components

Last six Transformer blocks and final normalization layer

Feature fusion

CLS token + mean patch tokens

Classification head

MLP

Classification loss

Focal loss

Head-to-encoder learning-rate ratio

10:1

The exact dataset version, class mapping, image counts, pretraining image pool, and split seed are not specified in the supplied abstract. These details should accompany the implementation for reproducibility.

Reported Results

The following values are reported in the supplied abstract and have not been independently reproduced for this README.

Metric

Reported value

Accuracy

88.00%

Macro F1

58.30%

Matthews correlation coefficient (MCC)

0.8699

Self-supervised pretraining epochs

30

The abstract compares the 30-epoch pretraining schedule with a single-objective MAE baseline trained for 400 epochs and reports a backbone approximately four times smaller.

Interpretation of efficiency: 400 / 30 is approximately 13.3, indicating approximately 13.3 times fewer pretraining epochs. An equivalent wall-clock or computational speedup cannot be established from epoch counts alone; it requires matched hardware, per-epoch costs, and measured runtime or FLOPs. The backbone-size comparison also requires the baseline configuration and parameter counts.

The difference between accuracy and macro F1 indicates that aggregate accuracy alone does not fully describe performance across classes. Per-class precision, recall, F1, and a confusion matrix would provide additional context.

Key Features

Joint reconstruction and contrastive pretraining with a shared encoder.

Four augmented views for multi-view representation learning.

Adaptive weighting of the pretraining objectives.

Partial encoder fine-tuning for downstream adaptation.

CLS and mean patch-token feature fusion.

Focal loss and separate learning rates for the head and encoder.

Reproducibility and Usage

This README documents the supplied abstract and architecture figure. Source code, dependency files, checkpoints, and executable commands were not supplied, so installation and training commands are not yet documented.

To make the experiments reproducible, include:

Environment requirements and dependency versions.

Dataset source, terms of use, class mapping, and split manifests.

The source of unlabeled pretraining images and how held-out data are excluded.

Patient or video grouping information, where available, to assess split leakage.

Input resolution, normalization, masking ratio, and augmentation settings.

The precise uncertainty-weighted objective and InfoNCE formulation.

Optimizer, learning rates, scheduler, batch size, and fine-tuning duration.

Random seeds, checkpoint selection rules, and evaluation scripts.

Hardware, runtime, and parameter counts for efficiency comparisons.

Ablations comparing reconstruction-only, contrastive-only, and joint training under matched conditions.
