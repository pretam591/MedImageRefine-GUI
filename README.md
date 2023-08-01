# MedImageRefine-GUI
This project focuses on medical image sharpening and the creation of a front-end GUI. I completed this as a part of my Research Internship at Jadavpur University.


## 1. Abstract

Deep learning has achieved great success in the field of artificial intelligence, and many deep learning models have been developed. Generative Adversarial Networks (GAN) is one of the deep learning models, which was proposed based on zero-sum game theory and has become a new research hotspot.  The significance of the model variation is to obtain the data distribution through unsupervised learning and to generate more realistic/actual data. Currently, GANs have been widely studied due to the enormous application prospect, including image and vision computing, video and language processing, etc.

In this research, we use convolutional neural networks to register microscopic pictures at various magnification levels and to deliver an improved digital magnification. It has been observed that there are just a few snapshots from each magnification level of a particular slide available in the majority of real-world medical datasets. Hence, it is crucial that we compare where the higher level magnifications are located to where their lower level counterparts are located. Secondly, a decent digital magnification method is desirable because not all regions have access to enlarged versions. In order to meet these requirements, we began by collecting some medical data of cancerous cells and made a Dataset out of them after cleaning the data. Here, we use SR-GAN to generate the enlarged versions thus improving digital magnification of microscopic images.

Additionally, we have covered the key distinctions between photos generated with SR-GAN and those sharpened using a filter in some editing programmes. A mathematical analysis of the entire dataset using Mean Squared Error between filter-based sharp images and generated images is shown.
Thus, the usage of neural networks will process the photos with some essential information required for the disease categorization, as we will see after going through the work. However, editing software would be less effective and would overlook important subtleties.

## 2. Introduction

Like privacy, health research has high value to society. It can provide important information about disease trends and risk factors, outcomes of treatment or public health interventions, functional abilities, patterns of care, and health care costs and use. The different approaches to research provide complementary insights. Clinical trials can provide important information about the efficacy and adverse effects of medical interventions by controlling the variables that could impact the results of the study, but feedback from real-world clinical experience is also crucial for comparing and improving the use of drugs, vaccines, medical devices, and diagnostics.

Medical image analysis is an important application of deep learning, which is expected to greatly reduce the workload of doctors, contributing to more sustainable health systems. However, most current AI methods for medical image analysis are based on supervised learning, which requires a lot of annotated data. The number of medical images available is usually small and the acquisition of medical image annotations is an expensive process.

GAN opens some exciting new ways for medical image generation, by approximate real data generation, expanding the number of medical images available for deep learning methods. Generated data can solve the problem of insufficient data or imbalanced data categories.



## 3. Dataset Description

### Table 1:- Details of Magnification of Dataset Images

| Total Images | 4x Images | 10x Images | 40x Images |
| :---:        |     :---:      |          :---: | :---: |
| 44   | 4     | 8    | 32 |

### Table 2:- Details of Dimension Change 

| Initial Dimension | Cropped Dimension | Resized Dimension | Final Dimension |
| :---:        |     :---:      |          :---: | :---:|
| 3584 * 2748   | 3584 * 2560     | 1792 * 1280    | 256*256 |


### Table 3:- Final Image Sample Division
| Total Dataset Images | Trianing Images | Validation Images | Testing Images |
| :---:         |     :---:      |          :---: | :--:
| 1400   | 560     | 240    | 560 |

## 4. Objectives
- Automatically Register Microscopic Images across different magnification levels.

- Improving digital magnification of microscopic images using SR-GAN. (i.e. - to generate  a 40x image given a 4x image)

## 5. Methodology

### 5.1 Automatic Image Registration across different magnification levels of microscopic images

In this proposed approach we are attempting to locate a microscopic image captured at 10x and 40x zoom and align with the appropriate region of the 4x equivalent image. Brute force implementations can be carried out by iterating a reduced 10x or 40x image over the 4x image and computing the difference. For a perfect alignment the difference would be zero. Since there is a higher probability of the matched region to occur near the center of the image, we can start by sliding the alignment window in a spiral path starting from the center and moving to the periphery. This process has been described as follows:

#### 5.1.1 Brute Force Registration

We begin by generating the spiral and iterating it on a reduced 10x or 40x image over the 4x image as already mentioned above.  The steps and issues involved are mentioned in the below sections:
 
#### 5.1.1.1 Spiral Coordinate Generation

We iterate through the (512*512) image by starting to create spirals right from the middle coordinates of the image.In the figure, the red line denotes the spiral and it starts from the center denoting 1. The overlap found in the cell marked in green color is below the threshold. So, to reach there we had to traverse through 36 other cells, following the spiral.



#### 5.1.1.2	Detecting Alignment

Alignment will be detected by calculating the difference among the corresponding RGB values of the particular image. If the difference calculated for a particular cell is found to be under a certain threshold value, it is the required alignment.

#### 5.1.1.3 Time Complexity Issues 

In the Worst Case Scenario, the matched section with least difference will be at the extreme corner of the square. The complexity in this case will be:- O(D^2) where, D is the side of the image square in pixels. In the Average Case Scenario, if the size is same as D*D, the matched section with least difference will be somewhere in the middle, say at D/2 position.  The complexity in this particular case will be (D/2 * D/2) i.e. :- O(D^2/4). Now, if the corresponding position is right at the beginning, the complexity is: O(1).

As we saw that, in the worst case quadratic time complexity is reached, brute force approach cannot be taken into consideration for image registration.  To solve this issue we have proposed an optimized version of the approach, represented in the below sections. 

#### 5.1.2 Optimized Registration of Images of different magnification.
	
In this procedure, we are trying to reduce the magnification of the (512x512) image everytime by a division of 2. This is done 3-4 times, until a proper small image is found. In this small image, we are calculating the overlap, and thus upscaling the magnification to what it is originally level b. The benefit we are getting using this approach is we have to create less spirals and check for lesser areas than what it was before. So, we first down-sampled the original image of (256x256) dimension, generated the spiral and found the alignment. Now, that particular cell in it’s upper level will represent a 2*2 window, therefore checking will only be limited in that region. Once found will further go to the next level, and finally the alignment will be observed in the original image. In the figure below, we demonstrated the same concept:-

![1](https://github.com/pretam591/MedImageRefine-GUI/assets/55977996/3403d278-266c-42ef-a22d-4a4b3f9323c5)

#### 5.1.2.1 Updated Time Complexity

If we are reducing the image upto ‘L’ levels, then the original size of the image square (say. D*D) is divided with $\2^L$ every single time. Now, when we found the match in the subsequent small image, we just have to check in (2*2) region in its upper level. So,  everytime a multiple of 4 times the level should be added to the former result. Therefore, the complexity comes to be:- 
O((D2L)2)+4L

Now, we can compare this with the time complexity as calculated in the brute force method, as shown below:-

$\ O((D2L)2)+4L  ≪  O(D2) $


Hence, we are successful in reducing the complexity quite a fold, from quadratic time to logarithmic.


#### 5.1.2.2 Implementation

As the goal of Image Registration Technique is to gather an exact image overlap from a region of 512*512 image i.e. calculated considering the least difference among corresponding RGB valued squares. For the implementation, in the first phase we use a getSpiral function in this work. It generates a spiral starting from the center of the target image. 

A complete pseudocode of the proposed scheme is presented in Algorithm 1 as follows:-

#image

### 5.2 Improving digital magnification of microscopic images using SR-GAN



We are specifically using Super-Resolution Generative Adversarial Networks (SRGAN) in this work. SRGAN is a generative adversarial network for single image super-resolution. SRGAN was proposed by researchers at Twitter. The motive of this architecture is to recover finer textures from the image when we upscale it so that it’s quality cannot be compromised. SRGANs offer a fix to the problems that are encountered due to technological constraints or any other reasons that cause these limitations. With the help of these tremendous GAN architectures, much of the low-resolution images or video content could be upscaled and can be found into high-resolution entities.

Architecture:- Similar to GAN architectures, the Super Resolution GAN also contains two parts Generator and Discriminator where generator produces some data based on the probability distribution and discriminator tries to guess weather data coming from input dataset or generator.

Given below are the steps that we followed to improve the digital magnification of microscopic images using SR-GAN:-

    1. Data Cleaning
    2. Custom Dataset Creation for our files 
    3. Iteration through DataLoader
    4. Transforms
    5. Building the Neural Network
    6. Implementation of Generator and Discriminator classes for GAN
    7. Optimizing the Model Parameters
    8. Model Training 
    9. Saving Sample Images

### 5.2.1 Discriminator Model

The discriminator in a GAN is simply a classifier. It tries to distinguish real data from the data created by the generator. The discriminator's training data comes from two sources:
Real data instances, such as real pictures of people. The discriminator uses these instances as positive examples during training.
Fake data instances created by the generator. The discriminator uses these instances as negative examples during training.

#image

### 5.2.2 Generator Model

The generator part of a GAN learns to create fake data by incorporating feedback from the discriminator. It learns to make the discriminator classify its output as real. The portion of the GAN that trains the generator includes:
random input
generator network, which transforms the random input into a data instance
discriminator network, which classifies the generated data
discriminator output
generator loss, which penalizes the generator for failing to fool the discriminator.

#image

In our proposed methodology, to build the Generator model we will first downscale the sharp image as shown in figure 3.6 to a certain extent and then upscale it, such that a blurry version of the original sharp image is generated. So, for downscaling our Generator Model is at first trained using a combination of Convolution and Maxpool 2d layers alternatively on input tensor of dimensions (3 x 256 x 256), till it reaches dimension (128 x 32 x 32). Next, from this tensor we will upscale the image via concatenation with it’s corresponding tensor of the same size (can be matched from the downscale stage) such that the features are upheld almost correctly while increasing it’s dimensions. In order to upscale the image, we are using a sequence of three layer operations namely:- Upscaling Layer, Concatenation Layer and Convolution Layer one after the other, till it reaches it’s original dimension (3 x 256 x 256).

Thus, the Generator model will work on improving this blurry image with each epoch and successively keep on generating better Generator images. The goal of the Generator Model is to generate images that have very little difference with the original sharp image in terms of picture quality. 

This completes the in-depth analysis of the operational procedures for each of our goals. We'll analyze their findings and wrap up our work in the subsequent chapters.







## 6. Results and Analysis

In this particular chapter, we will go through with the analysis of the results that both of our proposed methodology, to register microscopic images of different levels of magnification and to provide an enhanced digital magnification using convolutional neural networks (already described in sections 5.1 and 5.2) generated. The following sections illustrates them:-

### 6.1 Assessment of proposed image registration technique

We applied the proposed algorithms:- Algorithm 1 and Algorithm 2 over our dataset and saved those images which had the minimum difference in their coordinates i.e depicting the maximum alignment/overlap. Among those 2 sample images are shown below:-

#image

In the ideal case of perfect alignment, the entire output image should have been black showing exact overlap. Although in real scenarios this is not attainable. In our case the output is showing an optimal situation which crosses our threshold overlap margin of 90% .

### 6.2 Assessment of proposed digital magnification approach using SR-GAN

The Digital Magnification task was carried out on the Training Dataset. As mentioned in Section 3.2 SR-GAN architecture was used to magnify and generate super resolution images in this work. We designed the Generator and Discriminator models in accordance with Figures 3.5 and 3.6 respectively. 

#### After 30 epochs:-

#image

From the above figure we can see that our model produces fairly good results after training. For more clarity let’s further zoom a portion of the 3 images and compare among their RGB values. The comparison is shown as follows:-

#image

From the corresponding RGB values of Blur, Generator and Sharp image respectively, we can clearly see that the difference between RGB values of Generator and Sharp Image are very less when compared to Generator and Blur Image and simultaneously Blur and Sharp Image. 
Now, let’s move to the entire dataset and calculate the Mean Squared Error between filter based sharp image and sharp image and between generated Image and sharp image across the whole dataset.  
The corresponding table shows the values generated as follows :-


### Table 4:- Mean Squared Error between Filter based Sharpening and Proposed Sharpening Method across whole dataset

| Filter based Sharpening | Proposed Sharpening Method |
| :---:        |     :---:      |         
| 40463.4   | 64.15    |

We can further plot the values into a ‘bar chart’ for more clarity:-

#image
## 7. Conclusion

Throughout this research internship we have made an effort to accomplish our goals applying the methodology we proposed in chapter 5. 
- Firstly, we were successful at registering images at various magnification levels, which was our initial goal. Figure 6.1 depicts the alignment's part. 
- In our second objective, the SR-GAN model worked pretty well. We are aware that there are typically few medical images available and that acquiring medical image annotations is an expensive operation. Having said that, our SR-GAN model was successful in simulating the distribution of real data and reconstructing approximately real data as we saw with analysis shown in figures 6.3 and 6.4, respectively. 

Thus, this innovative method for creating medical photos increased the amount of images that could be used with deep learning techniques, and the generated data might also help with the issue of incomplete or unbalanced data categories. Thus, it is proved that the use of neural networks allows for the processing of images with certain crucial information necessary for disease categorization. However, the effectiveness of editing softwares is lower and it would miss key details.
## 8. Future Scopes

Throughout this internship we have attempted to solve the image registration task optimally and digitally magnify medical images using SRGAN.

As future work in next few months, we look forward to many more avenues connected to this work and explore- 

- Improve the sharpening by refining further: Increase the number of epochs to a certain extent and try to generate Sharp Images with increased accuracy.

- Retrain on the production of new images: We would try to collect new images further and retrain our model on those dataset of images, to further improve accuracy.

## 9. Acknowledgements

 - [Dr. Swarnendu Ghosh](https://www.linkedin.com/in/dr-swarnendu-ghosh-a6a042237)



## 10. Author

- [Pretam Chandra](https://www.linkedin.com/in/pretam-chandra-6a67b6150/)


## 11. License

The license to this project:-

[![MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://github.com/pretam591/MedImageRefine-GUI/blob/main/LICENSE)

