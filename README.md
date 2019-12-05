# Fashion Generation from Structured Style Attributes

## Data 

### DeepFashion 

The [DeepFashion] dataset contains 289,222 clothing images, annotated with with 1,000 clothing attributes across five attribute categories, and 3 clothing categories. The images are grouped 

### MNIST Fashion 

The baseline dataset that we used is called "Fashion MNIST" (https://github.com/zalandoresearch/fashion-mnist). It is a publicly available dataset used for benchmarking purposes and contains 60,000 images of clothing items in 10 different categories. All of the images are 28x28 and are grayscale. Preprocessing was generally not required for this dataset except for pixel normalization. 

### Fashion Product Images 

We also trained several models on the Fashion Product Images dataset (https://www.kaggle.com/paramaggarwal/fashion-product-images-dataset) which contains 44,000 images of fashion products scraped from various e-commerce websites. 

The images are labeled by features such as gender, category, base color, season, usage, etc. We utilized two variations of the dataset, with one containing low-resolution images (60px by 80px) and another contained higher-resolution images (1800px x 2400px).

Simple image preprocessing was performed on the dataset, including resizing all the images into squares (64px x 64px for the smaller variation and 256px x 256px for the larger images), and normalizing all the images between the range 0 and 1.

![enter image description here](https://i.imgur.com/Kq4stnk.png)
*"High-resolution" image from Fashion Product Image dataset (256x256)*

![enter image description here](https://i.imgur.com/kr08sRP.png)
*"Low-resolution" image from Fashion Product Image dataset (64 x 64)*
We also limited the dataset in two different ways, one utilizing just apparel images and the other all types of fashion. For sake of simplicity in training the conditional GAN, we limited the apparel dataset to the top six subcategories of apparel and the top six base colors. For data utilizing all types of fashion, we limited the categories to top-wear, bottom-wear, and shoes, and the top three colors in the data.

## Models

### Conditional GAN

Generative Adversarial Networks (GAN) are a popular deep learning architecture for training deep generative models. GANs, there is a discriminator network and a generator network. The generator is responsible for generating new samples that are ideally indistinguishable from real samples in the dataset. The discriminator is responsible for distinguishing between real samples and samples created by the generator. The discriminator and generator are trained at the same time in an adversarial manner: improvements to the discriminator will hurt the performance of the generator and vice versa. One drawback of vanilla GANs is that it will generate random images from the domain. In tasks where we have class or attribute labels available, such as ours, it is desirable to be able to control what the GAN generates. Thus, we utilize the Conditional Generative Adversarial Network (CGAN), which was introduced in 2014 ([https://arxiv.org/abs/1411.1784). The CGAN allows one to introduce labels to the discriminator and generator such that one can generate samples conditioned on specific class-labels. Although there are several methods to condition GANs on class labels, the most often-used method by  CGANs is to introduce an embedding layer on the class labels, followed by a fully connected layer which scales the embedding layer to the size of the input image, such that it can be concatenated as an additional channel. CGANs can of course be used with any architecture, such as deep convolutional GANs (DCGANs 

### Conditional VAE 

Variational autoencoders (VAE) are powerful generative autoencoders that learn a latent representation for the input data. Utilizing variational inference and regularization techniques, VAEs learn latent representations with desirable properties, which allow for generating new
auddata points. Vanilla VAEs suffer from the same drawback as vanilla GANs--random images are generated without any knowledge of class-information. This is where Conditional VAEs (CVAE) come into play. The implementation behind CVAEs is very simple: simply concatenate the class labels to the input, and run the encoder-decoder architecture as normal. During training time, the encoder and decoder both have the extra input (the class labels). To generate an image belonging to a particular class, simply feed that class label into the decoder along with the random point in latent space sampled from a normal distribution.

### Multi-Conditional GAN 
Conditional GANs (cGAN) uses a traditional GAN architecture except with an extra class label for both the generator and the discriminator in order to "condition" the GAN to conform to that label. This is usually done through taking the class label as an extra input and concatenating it with the latent vector of the GAN.

In our multi-conditional variation of the cGAN, we incorporated attribute conditioning in two main ways. The Fashion Product Images dataset provided attributes in the form of multiple classes, so for that model we added extra layers corresponding to each category of attributes in the GAN, and concatenated them all together with the latent dimension so that the model could take in multiple attributes as conditioning while training. We also attempted encoding the label into an embedding layer and multiplying the input with the latent dimension but produced neglible differences. After preprocessing the DeepFashion dataset, we were able to one-hot-encode the attributes instead, and used that for attribute conditioning the cGAN.

Sampling the model's performance was done through giving the model every combination of attributes to generate.

### Attribute StackGAN 
Stacked Generative Adversarial Networks (StackGAN) were first introduced (https://arxiv.org/pdf/1612.03242.pdf) as a method to generate high-resolution imagefrom natural text. The approach is analogous to the drawing procedure of a human intartist, where the artist first sketches a rough outline of an image before creating a refined, high-quality result. The architecture employs two stages of GANs: the first stage GAN generates low resolution images conditioned on the input text embeddings. The second GAN then takes the output of the first stage's generated image and the original text embeddings and uses them to generate a high-quality image.

In the fashion domain, being able to clearly see the details of a product whether for purchasing or virtual try-on purposes is essential. Thus, generating high-resolution images is an important part of the problem we are trying to tackle. 

We modified the StackGAN, which originally used text embeddings to condition the image, to use a one-hot-encoded attribute layer as input instead. Stage 1 GAN would utilize the attribute layer to generate low resolution fashion images, which we would then feed into the Stage 2 GAN.

### Conditional Progressively Growing GAN

### Conditional Disentangled Progressively Growing GAN

## Evaluation

### InceptionV3 Model


## Results

### Training

### Testing

## Generated Images

### Fashion MNIST 

CGAN Class-Conditional Generation Results

![CGAN Class-Conditional Generation Results](https://raw.githubusercontent.com/hg1722/fashionista/master/pics/CGAN.png?token=ABFA6BMN5FQJQR4UFRKEMTK56IDMO)

CVAE Class-Conditional Generation Results

![CVAE Class-Conditional Generation Results](https://raw.githubusercontent.com/hg1722/fashionista/master/pics/CVAE.png?token=ABFA6BK4JA776OWUEODYXDK56IDUM)

In both grids above, each column represents a specific class label. Clearly, both the CGAN and CVAE are successfully able to generate class-specific images when conditioned on a particular class. The CGAN results are a bit better than the CVAE since the generated images are crisper/less blurry. VAEs are known to generate blurry images in general. Overall, the Fashion MNIST dataset served as a proof-of-concept, showing the effectiveness of class-conditional generative modBoth the CGAN and CVAE are successfully able to generate class-specific images as shown in the grids above. In both grids, each column represe the neion of this dataset is that only one class is conditioned on at a time. In real world fashion datasents a specific class labels. However, one limitation of this dataset is that, each clothing item o has multiple attributes, which motivates e one liitatfor Multi-Conditional GANs.

### Multi-Cconditional GAN Generated Images

![enter image description here](https://i.imgur.com/7RJfm0S.png)
*Sample images of multi-conditional GAN trained after 80 epochs*

![enter image description here](https://i.imgur.com/Tr7OTMj.png)
*Images generated from one sample of multi-conditional GAN after training for 30 epochs. The GAN was conditioned on color and  type attributes. The GAN seems to be fairly good at differentiating color, but struggles with clothing type.*

![enter image description here](https://i.imgur.com/O70Szk7.png)
*Images generated by multi-conditional GAN trained for 300 epochs, conditioned on color and fashionclothing type, aggregated from different samples*
<!--stackedit_data:
eyJwcm9wZXJ0aWVzIjoiZXh0ZW5zaW9uczpcbiAgcHJlc2V0Oi
BnZm1cbiAga2F0ZXg6XG4gICAgZW5hYmxlZDogdHJ1ZVxuIiwi
aGlzdG9yeSI6WzkxNjEwOTUxNyw4NzE4OTQ1MTYsNDM1MzE4Nz
I4LC01MTU1NzUwMDUsLTc4NTc2ODI1MywxNjUyMzExMjQ5LDE4
ODYxNDQyMjAsMjMzNDQ1MDE0LC02MzM4Mjc5NTQsLTIwNzczMD
k4MDUsMTYwMDA4MTI2LDE5MzQxMTA2MjYsMTMzNjI3OTQyOSw0
MzAxNzMzNDUsLTE0OTc0MTEyMjIsMTQ1NTAxMTQzNCw3MTUwMD
UxMzcsNDAyMDAwMjUxLDE3OTYxMjY0MDgsMTY1MjkxNDI3OV19

-->