# Fashion Generation from Structured Style Attributes

## Data 

### DeepFashion 

### MNIST Fashion  *Rahul*

The baseline dataset that we used is called "Fashion MNIST" (https://github.com/zalandoresearch/fashion-mnist). It is a publicly available dataset used for benchmarking purposes and contains 60,000 images of clothing items in 10 different categories. All of the images are 28x28 and are grayscale. Preprocessing was generally not required for this dataset except for pixel normalization. 

### Fashion Product Images *Henry*

We also trained several models on the Fashion Product Images dataset (https://www.kaggle.com/paramaggarwal/fashion-product-images-dataset) which contains 44,000 images of fashion products scraped from various e-commerce websites. The images are labeled by features such as gender, category, base color, season, usage, etc. We utilized two variations of the dataset, with one containing low-resolution images (60px by 80px) and another contained higher-resolution images (1800px x 2400px).

Simple image preprocessing was performed on the dataset, including resizing all the images into squares (64px x 64px for the smaller variation and 256px x 256px for the larger images), and normalizing all the images between the range 0 and 1.

We also limited the dataset in two different ways, one utilizing just apparel images and the other all types of fashion. For sake of simplicity in training the conditional GAN, we limited the apparel dataset to the top six subcategories of apparel and the top six base colors. For data utilizing all types of fashion, we limited the categories to top-wear, bottom-wear, and shoes, and the top three colors in the data.

## Models

### Conditional GAN

Generative Adversarial Networks (GAN) are a popular deep learning architecture for training deep generative models. GANs, there is a discriminator network and a generator network. The generator is responsible for generating new samples that are ideally indistinguishable from real samples in the dataset. The discriminator is responsible for distinguishing between real samples and samples created by the generator. The discriminator and generator are trained at the same time in an adversarial manner: improvements to the discriminator will hurt the performance of the generator and vice versa. One drawback of vanilla GANs is that it will generate random images from the domain. In tasks where we have class or attribute labels available, such as ours, it is desirable to be able to control what the GAN generates. Thus, we utilize the Conditional Generative Adversarial Network (CGAN), which was introduced in 2014 ([https://arxiv.org/abs/1411.1784). The CGAN allows one to introduce labels to the discriminator and generator such that one can generate samples conditioned on specific class-labels. Although there are several methods to condition GANs on class labels, the most often-used method by  CGANs is to introduce an embedding layer on the class labels, followed by a fully connected layer which scales the embedding layer to the size of the input image, such that it can be concatenated as an additional channel. CGANs can of course be used with any architecture, such as deep convolutional GANs (DCGANs). 

### Conditional VAE *Rahul*

Variational autoencoders (VAE) are powerful generative autoencoders that learn a latent representation for the input data. Utilizing variational inference and regularization techniques, VAEs learn latent representations with desirable properties, which allow for generating new data points. Vanilla VAEs suffer from the same drawback as vanilla GANs--a 

### Multi-Conditional GAN *Henry*
Conditional GANs (cGAN) uses a traditional GAN architecture except with an extra class label for both the generator and the discriminator in order to "condition" the GAN to conform to that label. This is usually done through taking the class label as an extra input and concatenating it with the latent vector of the GAN.

In our multi-conditional variation of the GAN, we added extra layers corresponding to each category of attributes in the GAN, and concatenated them all together with the latent dimension so that the model could take in multiple attributes as conditioning while training. We also attempted encoding the label into an embedding layer and multiplying the input with the latent dimension but produced neglible differences.

 Sampling the model's performance was done through giving the model every combination of attributes to generate.

### Attribute StackGAN *Henry*
Stacked Generative Adversarial Networks (StackGAN) were first introduced (Zhang et al., 2017) as a method to generate high-resolution images (256px by 256px) from natural text. 

In the fashion domain, being able to clearly see the details of a product whether for purchasing or virtual try-on purposes is essential. Thus, generating high-resolution images is an important part of the problem we are trying to tackle. Additionally our dataset had many high-resolution images to train on. 

We modified the StackGAN, which originally used text embeddings


### Conditional Progressively Growing GAN

### Conditional Disentangled Progressively Growing GAN

## Evaluation

### InceptionV3 Model


## Results

### Training

### Testing

## Generated Images
<!--stackedit_data:
eyJwcm9wZXJ0aWVzIjoiZXh0ZW5zaW9uczpcbiAgcHJlc2V0Oi
BnZm1cbiAga2F0ZXg6XG4gICAgZW5hYmxlZDogdHJ1ZVxuIiwi
aGlzdG9yeSI6WzE0MzIwOTI0MjgsLTc5NDUzOTkzNywxNjM5OT
Q3Nzc2LDE1MDMwOTI5MTYsMzI4OTc3OTQ4LC0xNDA3MzM0OSwt
MTI3NDk4OTM2OCw5MDY4Nzk4NDMsLTE5MTg5ODY2NjEsMTY1MT
EyOTUxNiwtODY3NDIxMDc1LDk2ODc4OTIyMCwxNjgyODk0NTE1
LC0xMTQ1NDY5ODAxLDExMzk1Njc1NzcsNTM3MDEzNjQ3XX0=
-->