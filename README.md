# Fashion Generation from Structured Style Attributes

With the explosion of eCommerce, there is a growing need for personalized online shopping experiences. Specifically, in the fashion industry, there is a growing number of styles and clothing-types, along with a growing number of people interested in buying clothes online. It is crucial for the fashion industry to provide a personalized experience for each shopper, such that each shopper's personal tastes are accounted for. Concretely, each shopper should be able to describe their preferences and the styles they are looking for, and a list of matching clothing items should be returned. The only way to achieve this goal at scale is through the help of machine learning. In this project, we describe and tackle the novel task of going from fashion styles and attributes to newly generated images. The goal is to synthesize new clothing items based on the user's inputted style and attributes, which can serve multiple purposes downstream: 1) users can experiment with different styles and attributes and see visually what is most appealing to them 2) the newly synthesized images can be used for image retrieval from an eCommerce store's internal database, to return the most visually similar products. While there is existing literature which goes from input images to predicted attributes, we are proposing to build an architecture that goes in the opposite direction: we are attempting to synthesize images from given attributes; this a challenging, novel task which requires the use of generative deep learning models as opposed to classification models. 

## Data 

### DeepFashion 

The [DeepFashion](http://mmlab.ie.cuhk.edu.hk/projects/DeepFashion/AttributePrediction.html) dataset contains 289,222 clothing images, annotated with with 1,000 clothing attributes across five attribute and three clothing categories. The text data attributes are modified, removing semantically equivalent attributes, expanding the number of clothing categories into more specific descriptors, and removing the **style** attribute type (see [this code](https://github.com/hg1722/fashionista/blob/master/datasets/deepfashion/text_attribute_preprocess.ipynb) for more details). The final attribute embeddings consist of 486 distinct identifiers, separated into **category**, **shape**, **texture**, **fabric**, and **part** bins.  The structure is outlined below, where each field, with the exception of **category**, can have multiple associated attribute identifiers. 
![enter image description here](https://github.com/hg1722/fashionista/blob/master/pics/embedding.png) 

 The images are grouped broadly into directories based on 2-3 shared attributes to facilitate the CPGGAN LSE loss. These high-level classes are formed from the clothing category and primary common attributes; while all images in a given class will have the shared attributes, there are additional modifying attributes that further distinguish the clothing but are not prevalent enough to form distinct classes. We experiment with forming attribute embeddings comprised solely of the features captured in the class labels, and use this as a baseline when introducing further Generator loss penalties in the CDPGGAN model. 
 
The raw images vary in quality, level of noise, and obfuscation. The GrabCut algorithm is used to standardize the images - first creating a mask based on the bounding box, then honing the model with the convex polygon created by the landmark annotations (implemented [here](https://github.com/hg1722/fashionista/blob/master/datasets/deepfashion/image_preprocess.ipynb)). 
![image before and after](https://github.com/hg1722/fashionista/blob/master/pics/bwaaaAAAaaah_inception.png) The final data consists of 108,822 focused images, which are normalized and scaled for input into the stages of the CPGGAN.

### Fashion MNIST

The baseline dataset that we used is called ["Fashion MNIST"](https://github.com/zalandoresearch/fashion-mnist). It is a publicly available dataset used for benchmarking purposes and contains 60,000 images of clothing items in 10 different categories. All of the images are 28x28 and are grayscale. Preprocessing was generally not required for this dataset except for pixel normalization. 

### Fashion Product Images 

We also trained several models on the [Fashion Product Images dataset ](https://www.kaggle.com/paramaggarwal/fashion-product-images-dataset) which contains 44,000 images of fashion products scraped from various e-commerce websites. 

The images are labeled by features such as gender, category, base color, season, usage, etc. We utilized two variations of the dataset, with one containing low-resolution images (60px by 80px) and another contained higher-resolution images (1800px x 2400px). Below is a breakdown of the dataset, based on master category of fashion item, subcategory, and base color of the item.

![enter image description here](https://i.imgur.com/NrmA5YN.png)

For preprocessing, all images were resized to squares, 64x64 px for the low-resolution images and 256x256 px for the high-resolutions, and normalized.

![enter image description here](https://i.imgur.com/NpJAfF7.png)
We also ran experiments with training on only apparel images ofand the dataset as well as the full dataset with all fashion types. When training the conditional GAN models on only the apparel d sub, we limited the apparel dataset to the top six subcategories of apparel and the top six base colors. For experiments utilizing all types of fashion data, we limited the categories to top-wear, bottom-wear, and shoes, and the top three colors in the data.

## Models

### Conditional GAN

Generative Adversarial Networks (GAN) are a popular deep learning architecture for training deep generative models. GANs, there is a discriminator network and a generator network. The generator is responsible for generating new samples that are ideally indistinguishable from real samples in the dataset. The discriminator is responsible for distinguishing between real samples and samples created by the generator. The discriminator and generator are trained at the same time in an adversarial manner: improvements to the discriminator will hurt the performance of the generator and vice versa. One drawback of vanilla GANs is that it will generate random images from the domain. In tasks where we have class or attribute labels available, such as ours, it is desirable to be able to control what the GAN generates. Thus, we utilize the Conditional Generative Adversarial Network (CGAN), which was introduced in 2014 ([https://arxiv.org/abs/1411.1784). The CGAN allows one to introduce labels to the discriminator and generator such that one can generate samples conditioned on specific class-labels. Although there are several methods to condition GANs on class labels, the most often-used method by  CGANs is to introduce an embedding layer on the class labels, followed by a fully connected layer which scales the embedding layer to the size of the input image, such that it can be concatenated as an additional channel. CGANs can of course be used with any architecture, such as deep convolutional GANs (DCGANs 

![enter image description here](https://i.imgur.com/W4n9Iav.png)
### Conditional VAE 

Variational autoencoders (VAE) are powerful generative autoencoders that learn a latent representation for the input data. Utilizing variational inference and regularization techniques, VAEs learn latent representations with desirable properties, which allow for generating new
data points. Vanilla VAEs suffer from the same drawback as vanilla GANs--random images are generated without any knowledge of class-information. This is where Conditional VAEs (CVAE) come into play. The implementation behind CVAEs is very simple: simply concatenate the class labels to the input, and run the encoder-decoder architecture as normal. During training time, the encoder and decoder both have the extra input (the class labels). To generate an image belonging to a particular class, simply feed that class label into the decoder along with the random point in latent space sampled from a normal distribution.

![enter image description here](https://i.imgur.com/VLBQT8n.png)

### Multi-Conditional GAN 
Conditional GANs (cGAN) uses a traditional GAN architecture except with an extra class label for both the generator and the discriminator in order to "condition" the GAN to conform to that label. This is usually done through taking the class label as an extra input and concatenating it with the latent vector of the GAN.

In our multi-conditional variation of the cGAN, we incorporated attribute conditioning in two main ways. The Fashion Product Images dataset provided attributes in the form of multiple classes, so we added extra layers corresponding to each category of attributes in the GAN, and concatenated them all together with the latent dimension so that the model could take in multiple attributes as conditioning while training. 

![enter image description here](https://i.imgur.com/QS1ZR0J.png)

We also attempted encoding the label into an embedding layer and multiplying the input with the latent dimension but produced negligible differences. After preprocessing the DeepFashion dataset, we were able to one-hot-encode the attributes instead, and used that for attribute conditioning the cGAN. Sampling the model's performance was done through giving the model every combination of attributes to generate.


### Conditional Progressively Growing GAN

PGGANs increase image resolution through subsequent layers during training, allowing the network to begin by learning a fuzzy concept of the input, and progress to focusing on more specific image features. This is ideal for the DeepFashion dataset, given the high variability of the supplied images, and the goal of controlling across the five attribute fields in generating images. 

We define the goal of the Generator: given a noise vector conditioned on attribute embeddings $e$ that corresponds to image class $c$, create images $x$ that appear real, and will be classified with label $c$. The Discriminator $D(x,e)$ then needs to critique if the image $x$ is real and in the correct class, in the wrong class, or is fake.

![](https://latex.codecogs.com/svg.latex?%5Cinline%20%5Clarge%20L_D%20%3D%20E_%7B%28X%2CE%29%20%5Csim%20Pr-cc%7D%5B%28D%28x%2C%20e%29%20-1%29%5E2%20%5D%20&plus;%20E_%7B%28X%2CE%29%5Csim%20Pge%7D%20%5B%28D%28x%2C%20e%29%20&plus;%201%29%20%5E2%20%5D%20&plus;%20E_%7B%28X%2CE%29%5CsimPr%u2212wc%7D%20%5B%28D%28x%2C%20e%29%20&plus;1%29%20%5E2%20%5D)

![](https://latex.codecogs.com/svg.latex?%5Cinline%20%5Clarge%20L_G%20%3D%20E_%7B%28X%2CE%29%20%5Csim%20Pge%7D%20%5BD%28x%2C%20e%29%5E2%5D)

The Generator attempts to  push $D\rarr0$ over the generated images & attribute embeddings, while the Discriminator learns toward $D\rarr-1$. The Discriminator loss function gives further penalty for moving away from $D\rarr1$ on the real & correctly classified images, and rewards recognizing incorrectly classified images equal to finding fake images. For more detail on model parameters and architecture, see [cpggan.py](https://github.com/hg1722/fashionista/blob/master/models/cpggan/cpggan.py).

### Conditional Disentangled Progressively Growing GAN

In this model, the goal of the Discriminator stays the same, while the Generator is given additional feedback targeted toward specific fields. The general idea for disentangling loss:

![](https://latex.codecogs.com/svg.latex?%5Clarge%20L_G%20%3D%20E_%7B%28X%2CE%29%20%5Csim%20Pge%7D%20%5BD%28x%2C%20e%29%20%5E2%5D%20&plus;L_%7Bi%7D&plus;%5Cdots%20&plus;L_%7Bj%7D%24%20for%20distinct%20%24i%2Cj%24%20in%20%24%5Bcategory%2C%20shape%2C%20texture%2C%20fabric%2C%20part%5D)

We start by baselining on attribute embeddings that only contain the features encompassed in the class label. An additional consistency check for clothing **category** is added in the form:


![L_{category} = E_{c∼p(c)}[E_{x_1, x_2∼p^c_g}[|x_1-x_2|]]](https://latex.codecogs.com/svg.latex?%5Clarge%20L_%7Bcategory%7D%20%3D%20E_%7Bc%5Csim%20p%28c%29%7D%5BE_%7Bx_1%2C%20x_2%5Csim%20p%5Ec_g%7D%5B%7Cx_1-x_2%7C%5D%5D)


Where $c$ is the high-level clothing category formed from three possible category values in the raw dataset.

## Evaluation

### InceptionV3 Model

The Inception network examines clothing images, *X,* produced by the Generator during testing, in relation to the image class test labels, ![$Y$]. The goal is to optimize the score:

$$I(G) = E_{X∼P_G} [D_{KL}(P_{Y |X}(y|x) || P_Y (y))]$$

with respect to the two random variables. $D_{KL}$ measures the deviation of the distribution $P_{Y |X}$ - the probability of labeling an image with a given class - with respect to the reference distribution $P_Y (y)$ - the probability of a given class label. The class labels are diverse - high entropy - forcing  the entropy of $P_{Y |X}$ to be minimized in order to increase the KL divergence. As the entropy of $P_{Y |X}$ is minimized when the images in $X$ are labeled with high certainty, this provides a measure for Generator performance. 

The inception score is computed over 20,000 images split into five groups, the results of which are averaged.  For implementation details, see [inception.py](https://github.com/hg1722/fashionista/blob/master/models/cpggan/inception.py).

## Results

![enter image description here](https://github.com/hg1722/fashionista/blob/master/pics/bwaaaAAAaaah_inception.png)

## Generated Images

### Fashion MNIST 

CGAN Class-Conditional Generation Results

![enter image description here](https://i.imgur.com/s4vok5u.png)

CVAE Class-Conditional Generation Results

![CVAE Class-Conditional Generation Results](https://i.imgur.com/KQyL0fF.png)

In both grids above, each column represents a specific class label. Clearly, both the CGAN and CVAE are successfully able to generate class-specific images when conditioned on a particular class. The CGAN results are a bit better than the CVAE since the generated images are crisper/less blurry. VAEs are known to generate blurry images in general. Overall, the Fashion MNIST dataset served as a proof-of-concept, showing the effectiveness of class-conditional generative models. However, one limitation of this dataset is that, each image has only one class-label associated with it. In real world applications, each clothing item will have multiple attributes, which motivates the need for Multi-Conditional GANs.

### Fashion Product Images Dataset

We first trained a standard conditional GAN on the Fashion Products dataset, and generated different images of clothing conditioned on clothing subtype, such as boxers, jeans, t-shirts, etc.

![enter image description here](https://i.imgur.com/7RJfm0S.png)

*Sample images of multi-conditional GAN trained after 80 epochs*


Our next step was to test the multi-conditional aspect of the GAN. By conditioning it on attributes for color and clothing attributes instead of a single fashion type feature, we were able to train the GAN to distinguish different colors, but the modifying the subtype of clothing as well was a challenge for the model.

![enter image description here](https://i.imgur.com/Tr7OTMj.png)

*Images generated from one sample of multi-conditional GAN after training for 30 epochs. The GAN was conditioned on color and  type attributes.*


After observing the trouble that the GAN had with differentiating with different types of clothing, we experimented with replacing that condition with different fashion types instead. Our intuition was that the visual difference between features like a shirt and a shoe would be greater than simply variations of clothing styles, so the GAN would have an easier time differentiating the conditions while training. After training, our multi-conditional GAN was able to reliably generate the different color and fashion type combinations. However, there were still inconsistencies in generation between different samples.

![enter image description here](https://i.imgur.com/O70Szk7.png)

*Images generated by multi-conditional GAN trained for 300 epochs, conditioned on color and fashion type, aggregated from different samples*

# Unsuccessful Experiments



<!--stackedit_data:
eyJwcm9wZXJ0aWVzIjoiZXh0ZW5zaW9uczpcbiAgcHJlc2V0Oi
BnZm1cbiIsImhpc3RvcnkiOlstMTMxMjE0Mzk1NywtMzgzMzUy
Njc2LC04Mzg3ODYzMTIsMTc3OTQwMzQ1NiwxMjA4Mzg3ODU5LC
0xNzk2MTgyMjE4LC0xNTcyOTQ1NDAwLC04ODM3OTQwNTgsMTY0
ODk0MTAzMiwyMDUwNzM5NDc3LC02MzAwOTQ4OTIsLTQyMzM0MD
k1NiwtMTMxMDE0MDUxOSwtMjAxMjY3NDQ4MSw2ODUwNDg1Nzks
LTE1MjE5NzUxNDUsLTc4NjgyMTM5NywyNzM4Nzc1MDQsMTk3Mj
Y0NDIwNSwyMTM4MDk3Ml19
-->