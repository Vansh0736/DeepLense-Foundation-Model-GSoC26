

# DeepLense-Foundation-Model-GSoC26

**Author:** Vansh Jain
**Organization:** Machine Learning for Science (ML4Sci)
**Project:** Foundation Models for DeepLense: Self-Supervised and Physics-Informed Learning

### Tasks & Results

**Evaluation Metrics:** * **Classification Tasks:** Area Under the ROC Curve (AUC) and total loss were used to measure substructure classification performance. 
* **Reconstruction Tasks:** Peak Signal-to-Noise Ratio (PSNR), Structural Similarity Index (SSIM), and Mean Squared Error (MSE) were used to evaluate image fidelity.

---

### Common Task 1: Morphological Classification & Optimization

**Objective:** Implement a baseline classification model for dark matter substructures and engineer a highly optimized custom architecture to maximize parameter efficiency.
**What I Did:**
* **Baseline Implementation:** Trained a standard ResNet-18 architecture to establish a performance benchmark.
* **Custom CNN V3 Design:** * Replaced dense layers with Global Average Pooling (GAP) to prevent feature bottlenecks.
  * Optimized channel capacities to capture complex morphological variance in strong lensing data.
* **Outcome:** Successfully built a model that achieves comparable classification accuracy to ResNet-18 while being heavily optimized for edge deployment.

**Results:** **6.6x Smaller Architecture** (1.68M parameters vs ResNet-18's 11.17M parameters).
**Key Takeaways:** * Strategic capacity scaling allows for massive parameter reductions without losing spatial feature extraction capabilities.

---

### Specific Task VII: Physics-Informed Neural Networks (PINN)

**Objective:** Integrate the physical laws of General Relativity—specifically the gravitational lensing equation ($\vec{\beta} = \vec{\theta} - \vec{\alpha}(\vec{\theta})$)—directly into the neural network to classify dark matter substructures.
**What I Did:**
* **The Y-Architecture Manifold:** * Implemented a `MultiscaleAlphaExtractor` with dilated convolutions to capture multi-scale lensing signals.
  * Designed a Differentiable Inverse-Mapping Layer using spatial transformer grids.
* **Physical Constraints:** Enforced a **Curl-Free Constraint** ($\vec{\nabla} \times \vec{\alpha} = 0$) on the predicted deflection fields to ensure mathematical validity.
* **Gradient Conflict Diagnosis:** * Identified a severe loss plateau at 1.098 ($\ln(3)$) caused by conflicting gradients between the physics loss and classification loss.
  * Implemented a **Numerical Warm-up Protocol** with an annealed $\lambda_{phys}$ weighting factor to stabilize the physical manifold before classification.

**Results:** **0.2844 Converged Total Loss** | **0.987 AUC (No-Sub)** | **0.962 AUC (Sphere)** | **0.976 AUC (Vortex)**.
**Key Takeaways:** * Forcing the model to learn representations subject to physical constraints vastly improves classification robustness.
* The Numerical Warm-up Protocol effectively breaks the characteristic symmetry stall found in standard PINNs.

Update post-submission: The warm-up protocol and curl-free constraint are fully implemented and benchmarked. Training logs confirm convergence from the ln(3) plateau (1.10) to a final best val loss of 0.2844 at epoch 30.
---

### Specific Task IX.A: Foundation Modeling (MAE)

**Objective:** Utilize Self-Supervised Learning (SSL) to extract robust representations from uncurated, highly noisy simulated lensing data.
**What I Did:**
* **Masked Autoencoder (MAE) Implementation:** * Built a Vision Transformer (ViT) backbone.
  * Applied an aggressive **75% masking ratio** to input patches.
* **Self-Supervised Pre-Training:** * Forced the model to reconstruct the missing pixels of the lensing arcs, forcing the encoder to learn deep, underlying morphological structures rather than overfitting to superficial noise.
* **Supervised Fine-Tuning:** Extracted the pre-trained foundation encoder and attached a classification head for substructure identification.

**Results:** Successfully extracted foundation weights capable of generalizing across diverse lensing topologies.
**Key Takeaways:** * High-ratio masking is highly effective for astrophysical data, forcing the network to understand the physics of the lensing arc rather than interpolating background noise.

---

### Specific Task IX.B: Super-Resolution Transfer

**Objective:** Transfer the learned representations from the Foundation Model to a downstream image reconstruction task, upscaling low-resolution lensing images.
**What I Did:**
* **Encoder Integration:** Loaded the pre-trained `deeplense_vit_encoder_brain.pth` to serve as the feature extractor.
* **Decoder Design:** Built an upsampling decoder network utilizing PixelShuffle layers to accurately reconstruct high-frequency spatial details from the latent space.
* **Evaluation:** Benchmarked the reconstructed high-resolution images against ground-truth data using strict image-fidelity metrics.

**Results:** **43.10 dB PSNR**
**Key Takeaways:** * The representations learned via the MAE pre-training (Task IX.A) transfer exceptionally well to pixel-level reconstruction tasks, preserving critical subhalo perturbations in the upscaled images.

---

### Model Performance Summary

| Task / Model | Approach / Architecture | Key Metric / Result |
| :--- | :--- | :--- |
| **Task 1: Baseline** | ResNet-18 Classification | 11.17M Parameters |
| **Task 1: Optimized** | Custom CNN V3 | **1.68M Parameters (6.6x Smaller)** |
| **Task VII: PINN** | Y-Manifold + Curl-Free Constraint | **0.284 Loss \| ~0.97+ Avg AUC** |
| **Task IX.A: MAE** | Vision Transformer (75% Masking) | Foundation Encoder Extracted |
| **Task IX.B: Super-Res**| Transfer Learning + PixelShuffle | **43.10 dB PSNR** |

