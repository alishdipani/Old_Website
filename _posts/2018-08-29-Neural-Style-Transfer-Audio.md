---
layout:     post
title:      Neural Style Transfer on Audio Signals
date:       2018-08-29 21:32:18
summary:    Generation of new music using Neural Style Transfer Algorithm.
categories: Signal_Processing
thumbnail: music
tags:
 - Project
---

# <ins>Introduction</ins>

"Style Transfer" on images has recently become very popular and an active research topic, this shows how Convolutional Neural Networks(CNNs) power to adapt to a great variety of tasks. To further explore the adaptability of CNNs which work on non-sequential data like images, CNNs can be used for sequential data considering them as non-sequential for example, using CNNs on audio signals. So, further extending Style Transfer to audio signals, we test how CNNs can be used on sequential data.

# <ins>Neural Style Transfer on Images</ins>

"Neural Style Transfer" was originally made for images, the idea is to use a CNN model for extracting the style of an image called style image and content of another image called content image and generating a new image having the style of the style image and content of the content image. This is done by encoding the two images using a CNN Model and then taking a white noise image and minimizing the loss between this image and content and style images so that it has the content same as the content image and style as style image. <br><br>
Let $$ \vec{p} $$ be the content image,  $$ \vec{a} $$ be the style image and $$ \vec{x} $$ be the white noise image (i.e. the generated image) which will be the final image.  
So, total loss is sum of content loss and style loss.  
$$ L_{total}(\vec{p},\vec{a},\vec{x}) = \alpha L_{content}(\vec{p},\vec{x}) + \beta L_{style}(\vec{a},\vec{x})$$

### CNN Model

A deep CNN model is chosen to extract the features of images. Deep CNN models provide proper encoding for the features of images. A Model like VGG-19 is chosen having a large number of convolutional layers. Pre-trained model is used as they provide proper encoding.

![alt text](https://www.pyimagesearch.com/wp-content/uploads/2018/08/neural_style_transfer_gatys.jpg)

### Content Loss

<!---
derivative?
--->
The content loss is the Mean squared error between the encoding of the white noise image and the content image.  
For a layer $$ l $$ and the input image $$ \vec{x} $$, let the number of filters be $$ N_{l} $$ and so the output( or encoded) image will have $$ N_{l} $$ feature maps, each of size $$ M_{l} $$, where $$ M_{l} $$ is the area i.e. height times width. So, the encoded image of layer can be stored in a matrix $$ F_{l}  \epsilon  R^{ N_{l}xM_{l} } $$. Where $$ F^{l}_{ij} $$ is the activation of $$ i^{th} $$ filter at position $$ j $$ in layer $$ l $$.  
$$ L_{content}(\vec{p},\vec{x},l) = \frac{1}{2} \sum_{i,j}(F^{l}_{ij} - P^{l}_{ij})^{2}$$ 

### Style Loss 

<!---
Add style loss formula, derivative ?
--->

For capturing the style a style representation is used which computer the correlations between the different filter responses, where the expectation is taken over the spatial extend of the input image. These feature correlations are given by Gram Matrix $$ G^{l} \epsilon R^{ N_{l}xN_{l} } $$, where $$ G^{l}_{ij} $$ is the inner product between the feature maps $$ i $$ adn $$ j $$ represented by vectors in layer $$ l $$.  
$$ G^{l}_{ij}= \sum_{k}F^{l}_{ik}F^{l}_{jk} $$.  
And so the Style loss is the Mean squared error between the gram matrices of Style image and the white noise image.  
Let $$ \vec{a} $$ be the style image and $$ \vec{x} $$ be the white noise image. Let $$ A^{l} $$ and $$ X^{l} $$ be the style representations of of style image and white noise image in layer $$ l $$. So, Total style loss of a layer $$ l $$ is $$ E_{l} $$.  
$$ E_{l} = \frac{\sum_{i,j}(X^{l}_{ij}-A^{l}_{ij})^{2}}{4N^{2}_{l}M^{2}_{l}} $$  
So, the total style is  
$$ L_{style}(\vec{a},\vec{x}) = \sum^{L}_{l=0}w_{l}E_{l} $$  
where $$ w_{l} $$ are the weighting factor of each layer.

### Hyperparameter tuning

To calculate $$ L_{total} $$, $$ L_{content} $$ is weighted by $$ \alpha $$ and $$ L_{style} $$ is weighted by $$ \beta $$.  
The ratio of $$ \frac{\alpha}{\beta} $$ is generally kept $$ 10^{-3} $$ or $$ 10^{-4} $$, this prevents the style from dominating and therefore preventing the loss of content.

# <ins>Neural Style Transfer on Audio Signals</ins>

The base idea for Neural Style algorithm for audio signals is same as for images, the extracted style of te style audio is to be applied to the generated audio. Here, the content audio is directly used for generation instead of noise audio as this prevents calculation of content loss and eliminates the noise from the generated audio.

### Model Selection
For Audio signals 1 Dimensional Convolutions are used as they have different spatial features than images. So, models having 1-D CNN layers is used.  
It is observed that shallow models perform better than deep models and so a shallow model having only one layer but high filters is used.

### Pre-processing
An audio signal has to be converted to frequency domain from time domain beacuse the frequencies have the spatial features of audio signals.  
So, Short time fourier transform is used on the audios to convert them to frequency domain.

### Post-processing
After generation of audio phase reconstruction is done so as to convert the audio back to time domain from frequency domain.
Also, instead of using white noise to generate the final audio, the content audio is used which also prevents calculations as content loss is no longer needed, only style loss is used which is similar to images.

![alt text](https://ai2-s2-public.s3.amazonaws.com/figures/2017-08-08/7c592e7e00422dc7a76ead5932e34eafb5bef704/2-Figure2-1.png)

<!---
# <ins>Implementation</ins>
--->

# <ins>Technology Overview</ins>

Python 2.7 is used for implementation and the model is implemented using Deep Learning library PyTorch. Librosa is used for audio analysis. The model is executed on Intel® AI DevCloud which is 3x to 4x faster than the workstation being used, Intel® AI DevCloud runs the models and processes on high-performance and effecient Intel® Xeon® processors.

<!---
# <ins>Conclusion</ins>

Convolutional Neural Networks 

--->
# <ins>Future Work</ins>

With the coming of new generative models like Generative Adverserial Networks the neural style transfer algorithm can be modified and can be used for better results.

# <ins>References</ins>

### Papers :

1. Gatys, Leon A., Alexander S. Ecker, and Matthias Bethge. "Image style transfer using convolutional neural networks." Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition. 2016.
2. Grinstein, Eric, et al. "Audio style transfer." arXiv preprint arXiv:1710.11385 (2017).

### Blogs : 

1. [Audio texture synthesis and style transfer by Dmitry Ulyanov and Vadim Lebedev](https://dmitryulyanov.github.io/audio-texture-synthesis-and-style-transfer/)

### Code Implementations :

1. [Advanced Pytorch Tutorial for Neural Style Transfer](http://pytorch.org/tutorials/advanced/neural_style_tutorial.html#sphx-glr-advanced-neural-style-tutorial-py)

### Technical Components :

1. [Librosa](https://librosa.github.io/librosa/)
2. [Pytorch](https://pytorch.org/)
3. [Intel AI DevCloud](https://software.intel.com/en-us/ai-academy/devcloud)

### Project Resources :

1. [Intel DevMesh Project](https://devmesh.intel.com/projects/neural-style-transfer-on-audio-signals)
2. [Github Repository](https://github.com/alishdipani/Neural-Style-Transfer-Audio)
