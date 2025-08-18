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

### Figure 1: Original Images
<img width="812" height="248" alt="image" src="https://github.com/user-attachments/assets/77211d5b-bc49-42d4-91ff-da1e47e246e9" />


### Figure 2: Random Color Map Generation (Approach 1)

![Figure 2: Color maps generated using the first approach](<img width="870" height="465" alt="image" src="https://github.com/user-attachments/assets/2f27f90e-e5af-4bda-955f-6b9ba83bfd6b" />)

### Figure 3: Compressed Color Block Generation (Approach 2)

![Figure 3: Color maps generated using the second approach](<img width="909" height="461" alt="image" src="https://github.com/user-attachments/assets/e43ebce4-103c-4a9e-a42e-9b15f490d853" />)


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

![Figure 3: Color maps generated using the first approach for Image 1 (top row) and Image 2 (bottom row).](https://private-us-east-1.manuscdn.com/sessionFile/kUutG5AieJj8Fr6ue2UVnh/sandbox/yRp5WJ9tu5FdJBfrpOquyk-images_1755527103508_na1fn_L3RtcC9wZGZfaW1hZ2VzL1Jlc2VhcmNoX1Byb2plY3RfNV8yX19fQ29sb3JmdWxfR2VuZXJhdGlvbi8wMTA.webp?Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly9wcml2YXRlLXVzLWVhc3QtMS5tYW51c2Nkbi5jb20vc2Vzc2lvbkZpbGUva1V1dEc1QWllSmo4RnI2dWUyVVZuaC9zYW5kYm94L3lScDVXSjl0dTVGZEpCZnJwT3F1eWstaW1hZ2VzXzE3NTU1MjcxMDM1MDhfbmExZm5fTDNSdGNDOXdaR1pmYVcxaFoyVnpMMUpsYzJWaGNtTm9YMUJ5YjJwbFkzUmZOVjh5WDE5ZlEyOXNiM0ptZFd4ZlIyVnVaWEpoZEdsdmJpOHdNVEEud2VicCIsIkNvbmRpdGlvbiI6eyJEYXRlTGVzc1RoYW4iOnsiQVdTOkVwb2NoVGltZSI6MTc5ODc2MTYwMH19fV19&Key-Pair-Id=K2HSFNDJXOU9YS&Signature=tdpmy2t3ezhMpHVzWolro-gCYB3gwKcryzjV3Fa2bOIVv1e0LMZ~OcYFcd8zoS0SZF9chMaOkgP~SEjDCYiNO8XtHe2ZnK~aZyjSICHySOLRXK6~BDYwXA6ISKMUsYo9ykqTw9vce-Jm3HbQ2bDCrInFAb~hxsdOdsQi3N4SsfFRlrCbTeISfT~fG9qvrLa1lnPHJxTWvzoz38HsN~7a~yGdCtLO7gaqZaivt-b9IHE-oXtttG3UECtbREfq10co6KO4TxgRsjPKNbvMR8y~dXEENP~ES3DVRDJuXAsvDlk9BkOibkeVyKFSn16EyNzknba2dcpj34s6FxfzVnvMmw__)

### Figure 4: Variants Generated with Compressed Color Blocks (Approach 2)

![Figure 4: Color maps generated using the second approach (compressed 2x3 grid with delta variations) for Image 1 (top row) and Image 2 (bottom row).](https://private-us-east-1.manuscdn.com/sessionFile/kUutG5AieJj8Fr6ue2UVnh/sandbox/yRp5WJ9tu5FdJBfrpOquyk-images_1755527103508_na1fn_L3RtcC9wZGZfaW1hZ2VzL1Jlc2VhcmNoX1Byb2plY3RfNV8yX19fQ29sb3JmdWxfR2VuZXJhdGlvbi8wMTA.webp?Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly9wcml2YXRlLXVzLWVhc3QtMS5tYW51c2Nkbi5jb20vc2Vzc2lvbkZpbGUva1V1dEc1QWllSmo4RnI2dWUyVVZuaC9zYW5kYm94L3lScDVXSjl0dTVGZEpCZnJwT3F1eWstaW1hZ2VzXzE3NTU1MjcxMDM1MDhfbmExZm5fTDNSdGNDOXdaR1pmYVcxaFoyVnpMMUpsYzJWaGNtTm9YMUJ5YjJwbFkzUmZOVjh5WDE5ZlEyOXNiM0ptZFd4ZlIyVnVaWEpoZEdsdmJpOHdNVEEud2VicCIsIkNvbmRpdGlvbiI6eyJEYXRlTGVzc1RoYW4iOnsiQVdTOkVwb2NoVGltZSI6MTc5ODc2MTYwMH19fV19&Key-Pair-Id=K2HSFNDJXOU9YS&Signature=tdpmy2t3ezhMpHVzWolro-gCYB3gwKcryzjV3Fa2bOIVv1e0LMZ~OcYFcd8zoS0SZF9chMaOkgP~SEjDCYiNO8XtHe2ZnK~aZyjSICHySOLRXK6~BDYwXA6ISKMUsYo9ykqTw9vce-Jm3HbQ2bDCrInFAb~hxsdOdsQi3N4SsfFRlrCbTeISfT~fG9qvrLa1lnPHJxTWvzoz38HsN~7a~yGdCtLO7gaqZaivt-b9IHE-oXtttG3UECtbREfq10co6KO4TxgRsjPKNbvMR8y~dXEENP~ES3DVRDJuXAsvDlk9BkOibkeVyKFSn16EyNzknba2dcpj34s6FxfzVnvMmw__)

### Figure 5: Original Images

![Figure 5: Original images used for generating color maps and variants](https://private-us-east-1.manuscdn.com/sessionFile/kUutG5AieJj8Fr6ue2UVnh/sandbox/yRp5WJ9tu5FdJBfrpOquyk-images_1755527103509_na1fn_L3RtcC9wZGZfaW1hZ2VzL1Jlc2VhcmNoX1Byb2plY3RfNV8yX19fQ29sb3JmdWxfR2VuZXJhdGlvbi8wMTE.webp?Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly9wcml2YXRlLXVzLWVhc3QtMS5tYW51c2Nkbi5jb20vc2Vzc2lvbkZpbGUva1V1dEc1QWllSmo4RnI2dWUyVVZuaC9zYW5kYm94L3lScDVXSjl0dTVGZEpCZnJwT3F1eWstaW1hZ2VzXzE3NTU1MjcxMDM1MDlfbmExZm5fTDNSdGNDOXdaR1pmYVcxaFoyVnpMMUpsYzJWaGNtTm9YMUJ5YjJwbFkzUmZOVjh5WDE5ZlEyOXNiM0ptZFd4ZlIyVnVaWEpoZEdsdmJpOHdNVEUud2VicCIsIkNvbmRpdGlvbiI6eyJEYXRlTGVzc1RoYW4iOnsiQVdTOkVwb2NoVGltZSI6MTc5ODc2MTYwMH19fV19&Key-Pair-Id=K2HSFNDJXOU9YS&Signature=WS0L~zTcwYJieOOBtXm69qwpNu9IwhngPVCQbm3yvT~fhyxMszZsMhiCS~K5yE-exW3xG~QY-bjmvYA56el~g29iUx1-OzAQ5UmHlyBTNwQpSIJezYX~a9wPuESd6i5QQTTOpRM8qBEFjs8Xc87u3HSJzL2c6iZsOcQoEFsCnZOzldJaP2seZQSwDGQ4VlrdPo~drr6Anh4saGXfwRM5RvgKK767lyBi6RuEd0bQif8c~aUWEfqHCpBgTU-17sOiMRlxmhMsjGCxbzOg3CXFOKCU895RpreaDdKgyHFQImrZv8xk0bVP-o~NnpJsGiBcJIJT2BLq~fDD--YFQcymlw__)

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



