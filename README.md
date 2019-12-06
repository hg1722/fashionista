# Fashion Generation from Structured Style Attributes

## Data 

### DeepFashion 

The [DeepFashion](http://mmlab.ie.cuhk.edu.hk/projects/DeepFashion/AttributePrediction.html) dataset contains 289,222 clothing images, annotated with with 1,000 clothing attributes across five attribute categories, and 3 clothing categories. The images are grouped broadly into directories based on 2-3 shared attributes, which improves generator stability The text data attributes were modified in this [notebook](https://github.com/hg1722/fashionista/blob/master/datasets/deepfashion/text_attribute_preprocess.ipynb), removing semantically equivalent attributes, expanding the number of clothing categories into more specific descriptors, and removing the style attribute type. The final embedding, EXAMPLE; which provide a baseline for labelings consist of 486 attributeslasses 

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

![enter image description here](https://i.imgur.com/621I6hf.png)
### Conditional VAE 

Variational autoencoders (VAE) are powerful generative autoencoders that learn a latent representation for the input data. Utilizing variational inference and regularization techniques, VAEs learn latent representations with desirable properties, which allow for generating new
data points. Vanilla VAEs suffer from the same drawback as vanilla GANs--random images are generated without any knowledge of class-information. This is where Conditional VAEs (CVAE) come into play. The implementation behind CVAEs is very simple: simply concatenate the class labels to the input, and run the encoder-decoder architecture as normal. During training time, the encoder and decoder both have the extra input (the class labels). To generate an image belonging to a particular class, simply feed that class label into the decoder along with the random point in latent space sampled from a normal distribution.

![enter image description here](https://i.imgur.com/5ZM0Ede.png)

### Multi-Conditional GAN 
Conditional GANs (cGAN) uses a traditional GAN architecture except with an extra class label for both the generator and the discriminator in order to "condition" the GAN to conform to that label. This is usually done through taking the class label as an extra input and concatenating it with the latent vector of the GAN.

In our multi-conditional variation of the cGAN, we incorporated attribute conditioning in two main ways. The Fashion Product Images dataset provided attributes in the form of multiple classes, so we added extra layers corresponding to each category of attributes in the GAN, and concatenated them all together with the latent dimension so that the model could take in multiple attributes as conditioning while training. 

![enter image description here](https://i.imgur.com/QS1ZR0J.png)

We also attempted encoding the label into an embedding layer and multiplying the input with the latent dimension but produced negligible differences. After preprocessing the DeepFashion dataset, we were able to one-hot-encode the attributes instead, and used that for attribute conditioning the cGAN. Sampling the model's performance was done through giving the model every combination of attributes to generate.


### Conditional Progressively Growing GAN

### Conditional Disentangled Progressively Growing GAN

## Evaluation

### InceptionV3 Model


## Results


## Generated Images

### Fashion MNIST 

CGAN Class-Conditional Generation Results

![enter image description here](https://i.imgur.com/s4vok5u.png)

CVAE Class-Conditional Generation Results

![CVAE Class-Conditional Generation Results](https://i.imgur.com/KQyL0fF.png)

In both grids above, each column represents a specific class label. Clearly, both the CGAN and CVAE are successfully able to generate class-specific images when conditioned on a particular class. The CGAN results are a bit better than the CVAE since the generated images are crisper/less blurry. VAEs are known to generate blurry images in general. Overall, the Fashion MNIST dataset served as a proof-of-concept, showing the effectiveness of class-conditional generative models. However, one limitation of this dataset is that, each image has only one class-label associated with it. In real world applications, each clothing item will have multiple attributes, which motivates the need for Multi-Conditional GANs.

### Fashion Product Images Dataset

We first trained a standard conditional GAN on the Fashion Products dataset, and generated different categories of clothing, such as boxers, jeans, t-shirts, etc.

![enter image description here](https://i.imgur.com/7RJfm0S.png)
*Sample images of multi-conditional GAN trained after 80 epochs*

Our next step was to
![enter image description here](https://i.imgur.com/Tr7OTMj.png)
*Images generated from one sample of multi-conditional GAN after training for 30 epochs. The GAN was conditioned on color and  type attributes.*

![enter image description here](https://i.imgur.com/O70Szk7.png)
*Images generated by multi-conditional GAN trained for 300 epochs, conditioned on color and fashion type, aggregated from different samples*

# Unsuccessful Experiments



<!--stackedit_data:
eyJwcm9wZXJ0aWVzIjoiZXh0ZW5zaW9uczpcbiAgcHJlc2V0Oi
BnZm1cbiAga2F0ZXg6XG4gICAgZW5hYmxlZDogdHJ1ZVxuIiwi
aGlzdG9yeSI6Wy0xMTg0MjAwMjI0LDY4NTA0ODU3OSwtMTUyMT
k3NTE0NSwtNzg2ODIxMzk3LDI3Mzg3NzUwNCwxOTcyNjQ0MjA1
LDIxMzgwOTcyLDIxMTk4MzgxMzIsLTk3MTg4MzcxOCwtMTM1MD
cyMzM3MiwtNzgyNDI2NjY1LDY4OTEzNzM4MywxNDU2MzI4MTM0
LC0xOTgyMTg1OTA2LC00MTcyNjA0MTQsLTUwMjgxNTM4MiwxMT
k0OTU0MzMsNzAzMzY0NTEzLC0xMTExNTQ4OTIwLC04NDU0ODM2
MjldfQ==
-->