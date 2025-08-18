#**ColorEdge**

## Introduction

This project, **ColorEdge**, introduces a novel framework for systematically evaluating the robustness of image classification models against controlled color and structural perturbations. In real-world applications, image classification models often face challenges due to subtle variations in input data, such as changes in lighting, camera settings, or environmental factors. Traditional robustness evaluation methods, like adversarial attacks or simple data augmentations, often fall short in capturing these nuanced, naturally occurring variations. ColorEdge addresses this gap by leveraging advanced generative models to create interpretable and controlled image variants, providing deeper insights into model vulnerabilities.

## Problem Statement

Deep learning models, despite their high performance in image classification, are susceptible to minor alterations in input data, leading to misclassifications. This lack of robustness is a significant concern, especially in safety-critical domains. Our research aims to provide a systematic and reproducible method for understanding and improving the resilience of image classification systems against subtle yet impactful visual changes. We focus on generating diverse image variants by manipulating their color and edge information, and then assessing the performance of pre-trained classification models on these synthetically generated images.

## Methodology

ColorEdge utilizes a **Color-Canny-ControlNet diffusion model** to generate diverse image variants. This model allows for precise manipulation of visual features by fusing color information with Canny edge detections. These controlled variants are then fed into target classification models (ResNet18 and VGG19), and their predicted labels are compared against the original image’s label. We explored two primary approaches for generating color-conditioned variants:

### Approach 1: Random Color Map Conditioning

This approach generates color maps that are entirely disconnected from the original image's color distribution. It involves:

1.  **Edge Extraction:** Using Canny edge detection.
2.  **Random Color Condition Creation:** Arbitrary color blocks are generated.
3.  **Fusion:** Blending of edge and random color information.
4.  **Variant Generation:** Using ControlNet with the fused information and a guiding text prompt.

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/23c8e508-2390-4d99-9f7a-5869a94eff2e" />


This method creates visually jarring and semantically inconsistent variants, serving as a stress test for the models.

### Approach 2: Compressed Color Block Conditioning

This approach compresses the image into a low-resolution representation to extract dominant color blocks, preserving spatial structure. It involves:

1.  **Edge Extraction:** Using Canny edge detection.
2.  **Block-based Color Condition Creation:** Compressed color blocks are expanded to match the original image resolution.
3.  **Fusion:** Blending of edge and block-based color information.
4.  **Variant Generation:** Using ControlNet with the fused information and a guiding text prompt.

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/f593aba6-dc36-4296-8763-923568e0bc5c" />


This method generates visually coherent and plausible variants that are more representative of realistic, naturally occurring color variations.


## Visualizing Color Map Generation

Here are examples of color maps generated using both approaches:



### Figure 1: Random Color Map Generation (Approach 1)
<img width="868" height="456" alt="image" src="https://github.com/user-attachments/assets/6ea823db-af1c-4cc3-8b5e-f430d2a56f55" />


### Figure 2: Compressed Color Block Generation (Approach 2)

<img width="899" height="459" alt="image" src="https://github.com/user-attachments/assets/b147cbe7-c9eb-48d5-be3f-a4731bce81a1" />


## Results and Discussion

Our experimental evaluation revealed significant vulnerabilities in both ResNet18 and VGG19 models when confronted with color-conditioned perturbations. We used a subset of the ImageNet dataset, selecting three random images from each class.

### Model Performance Metrics

| Model      | Experiment                                      | Accuracy | Precision | Recall | F1-Score |
|------------|-------------------------------------------------|----------|-----------|--------|----------|
| ResNet18   | First Approach (Random Color Map)               | 0.2748   | 0.3536    | 0.2944 | 0.3214   |
| ResNet18   | Second Approach (Compressed Image Color Map)    | 0.3173   | 0.4197    | 0.3173 | 0.3616   |
| VGG19      | First Approach (Random Color Map)               | 0.2537   | 0.3489    | 0.2537 | 0.2934   |
| VGG19      | Second Approach (Compressed Image Color Map)    | 0.2802   | 0.4075    | 0.2694 | 0.3242   |

**Key Findings:**

*   **Impact of Color Map Generation:** The second approach (compressed image color maps with delta variations) generated visually coherent and plausible variants, more representative of naturally occurring color variations. Both ResNet18 and VGG19 performed better on these variants, indicating better resilience to realistic, subtle color shifts.
*   **Stress Testing with Random Color Maps:** The first approach (random color map generation) produced visually jarring and semantically inconsistent variants, serving as a stress test. Models showed consistently lower performance on these variants, confirming that extreme, arbitrary color changes pose a greater challenge.
*   **Model Comparison:** ResNet18 demonstrated slightly better resilience to both types of perturbations compared to VGG19, suggesting potential architectural advantages in handling color-based variations.
*   **Persistent Vulnerability:** Despite differences between approaches, both models consistently showed low accuracy (well below 0.85 threshold), highlighting that even subtle, systematic color shifts can significantly challenge their performance and indicating a general lack of robustness to color-edge conditioned perturbations.

## Visualizing Generated Variants

Here are examples of the generated image variants:

### Figure 3: Variants Generated with Random Color Maps (Approach 1)

<img width="870" height="465" alt="image" src="https://github.com/user-attachments/assets/2f27f90e-e5af-4bda-955f-6b9ba83bfd6b" />


### Figure 4: Variants Generated with Compressed Color Blocks (Approach 2)

<img width="909" height="461" alt="image" src="https://github.com/user-attachments/assets/e43ebce4-103c-4a9e-a42e-9b15f490d853" />

### Figure 5: Original Images

<img width="812" height="248" alt="image" src="https://github.com/user-attachments/assets/77211d5b-bc49-42d4-91ff-da1e47e246e9" />

## Conclusion and Future Work

This research presents a novel framework for evaluating the robustness of image classification models through color-edge conditioned image generation using diffusion models. Our methodology successfully demonstrates the effectiveness of leveraging Color-Canny-ControlNet diffusion models to generate controlled image variants that systematically test model vulnerabilities to color perturbations while preserving structural integrity.

Future work could involve expanding the evaluation to include modern transformer-based vision models, incorporating domain-specific datasets, and developing adaptive color map generation strategies. Methodological improvements could also include exploring alternative conditioning mechanisms beyond Canny edges and extending the framework to support temporal robustness evaluation for video classification models.

## Getting Started

To replicate the experiments or explore the code, please refer to the Jupyter notebooks in this repository:

*   `vgg_first_approach.ipynb`: First Approach (VGG Evaluation)
*   `vgg_second_approach.ipynb`: Second Approach (VGG version)

You Can modify the model you wish to experiment.

## Authors

*   Ahmed Hussein
*   Noureldin Hamedo



