---
title: 'COSE: A Consistency-Sensitivity Metric for Saliency on Image Classification'
# date: 2023-09-20
permalink: /projects/cose
tags:
  - projects
  - saliency maps
  - metrics
  - image classification
  - model explanations
---
[Rangel Daroya*](https://rangeldaroya.github.io/), [Aaron Sun*](https://aaronsun1030.github.io/), [Subhransu Maji](https://people.cs.umass.edu/~smaji/index.html)
_University of Massachusetts Amherst_

![Saliency Evaluation Across Datasets](https://rangeldaroya.github.io/files/cose_saliency-methods-dataset-transforms.png)
*Figure 1: Saliency maps extracted from a ResNet50 on CUB200 and CIFAR10 images. The colors indicate pixel importance predicted by different methods (blue=low; red=high). We show two sample data transformations: FlipLR (geometric transformation) and Equalize (photometric transformation). GradCAM is consistently focuses on the chest of the bird, despite the left-right flip. Even with image equalization, GradCAM emphasizes the nose of the dog. While other methods appear to do well on FlipLR, they struggle with Equalize. The proposed COnsistency-SEnsitivity (COSE) metric quantifies the equivariant and invariant properties of visual model explanations using simple data augmentations.*

# Overview
We present a set of metrics that utilize vision priors to effectively assess the performance of saliency methods on image classification tasks. To understand behavior in deep learning models, many methods provide visual saliency maps emphasizing image regions that most contribute to a model prediction. We propose the metric **COnsistency-SEnsitivity (COSE)** that quantifies the equivariant and invariant properties of visual model explanations using simple data augmentations. Through our metrics, we show that although saliency methods are thought to be architecture-independent, most methods could better explain transformer-based models over convolutional-based models. In addition, GradCAM was found to outperform other methods in terms of COSE but was shown to have limitations such as lack of variability for fine-grained datasets. The duality between consistency and sensitivity allow the analysis of saliency methods from different angles. Ultimately, we find that it is important to balance these two metrics for a saliency map to faithfully show model behavior.

# Key Idea
We propose consistency and sensitivity metrics that measure two complementary properties of saliency maps. 
**Consistency** refers to the property that saliency maps should remain _unchanged_ when an input is transformed in a way that the model predictions don't change. For example, when an input is reflected or translated by a small amount, the corresponding saliency maps should also undergo the same geometric transformation, as we do not expect the class predictions to change. Similarly, when the input undergoes a photometric transformation (e.g., change in pixel intensities or blurring), we expect saliency maps to remain identical.  In short, consistency captures the degree to which saliency maps are equivariant and invariant to transformations that don't affect model predictions. **Sensitivity** refers to the property that saliency maps _should change_ when the model produces a different output. This difference in model output could be a result of changes in the model parameters (e.g., during the process of training the model) or sufficiently large changes in input. Thus, the model's explanations must change for it to produce a different output. Prior knowledge was used to identify transformations that should result in equivariance and invariance for various computer vision tasks. Figure 2 illustrates these properties where GradCAM is shown to do well in terms of both consistency and sensitivity.

![GradCAM consistency and sensitivity to data transformations](https://rangeldaroya.github.io/files/cose_data_transform.png)
*Figure 2: GradCAM consistency and sensitivity to data transformations on ResNet50/Caltech101: the top row shows the input images, and the bottom row shows the corresponding saliency maps. In the reference image (a), the model correctly classifies the original image as a flamingo. (b) displays GradCAM’s consistency as the model correctly classifies the transformed image with a similar saliency map as (a). (c) displays GradCAM’s sensitivity where the model incorrectly classified the transformed image as ibis and the saliency map emphasizes differently from (a)*

# Results
Below we discuss the key insights from analyzing with COSE across eight models, five datasets, and seven saliency methods.

### Transformers have better explanations than ConvNets

![Transformers have better explanations quantitatively](https://rangeldaroya.github.io/files/cose_transformer_convnet.png)
*Figure 3: The distributions of COSE for ConvNets and Transformers compared for all saliency methods, shown as a violin plot. Within each violin, the thin line shows the 1.5x interquartile range, the thick line shows the interquartile range, and the white dot shows the median. The shape of the violin shows how data points are distributed. Transformers outperform ConvNets on average for all methods.*

![Transformers have better explanations](https://rangeldaroya.github.io/files/cose_saliency-methods-models.png)
*Figure 4: Results of saliency methods on ViT-B/16 (a transformer model), ResNet50 (a convolutional model), and Oxford102/Caltech101. We qualitatively observe the explanations for ViT-B/16 to be similar (Guided IG, SmoothGrad) or better (BlurIG, IG, LIME, GradCAM, GradCAM++). In general, we find transformer model explanations are more coherent than convolutional model explanations.*

### GradCAM is a reliable saliency method
![GradCAM has highest consistency](https://rangeldaroya.github.io/files/cose_saliency-consistency.png)
*Figure 5: GradCAM has the highest average consistency. We can also observe the distribution of consistency across different samples, with GradCAM having more samples with high consistency.*


### Existing saliency methods can be improved by balancing consistency and sensitivity

GradCAM is shown to outperform other saliency methods on several angles. However, with a COSE of 64.66%, GradCAM still has areas for improvement. Figure 1 shows that GradCAM tends to predict general areas, which limits its sensitivity to model changes. Due to the large salient area presented, it's more difficult to isolate differences due to model changes. SmoothGrad and BlurIG show more specific areas, but they tend to be unstable to input perturbations. Future work on saliency methods should aim to balance performance on both - being robust while maintaining good sensitivity.

At the same time, most methods struggle explaining changes related to photometric transformations more than geometric transformations. While COSE gives an overview of overall performance across all transform magnitudes, we recommend saliency method developers consider photometric changes and geometric changes as separate problems while trying to achieve consistency in both.

![Photometric and Geometric Transformations](https://rangeldaroya.github.io/files/cose_transform_mag_ttype.png)
*Figure 6: Average performance for all methods on all transformations and separately for geometric and photometric transformations. While photometric transformations have decreasing sensitivity and increasing fidelity as transformation magnitudes increase, geometric transformations seem to have approximately the same performance regardless of transformation magnitude.*

# Code
The code is available at [https://github.com/cvl-umass/COSE](https://github.com/cvl-umass/COSE)

<!-- # BibTeX -->


# Acknowledgements
The project was funded in part by NSF grant #1749833 to Subhransu Maji. The experiments were performed on the University of Massachusetts GPU cluster funded by the Mass. Technology Collaborative.
