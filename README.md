# Fashion Generation from Structured Style Attributes

## Data 

### DeepFashion 

### MNIST Fashion  *Rahul*

The baseline dataset that we used is called "Fashion MNIST" (https://github.com/zalandoresearch/fashion-mnist). It is a publicly available dataset used for benchmarking purposes and contains 60,000 images of clothing items in 10 different categories 

### Fashion Product Images *Henry*

We also trained several models on the Fashion Product Images dataset (https://www.kaggle.com/paramaggarwal/fashion-product-images-dataset) which contains 44,000 images of fashion products scraped from various e-commerce websites. The images are labeled by features such as gender, category, base color, season, usage, etc. We utilized two variations of the dataset, with one containing low-resolution images (60px by 80px) and another contained higher-resolution images (1800px x 2400px).

Simple image preprocessing was performed on the dataset, including resizing all the images into squares (64px x 64px for the smaller variation and 256px x 256px for the larger images), and normalizing all the images between the range 0 and 1.

We also limited the dataset in two different ways, one utilizing just apparel images and the other all types of fashion. For sake of simplicity in training the conditional GAN, we limited the apparel dataset to the top six subcategories of apparel and the top six base colors. For data utilizing all types of fashion, we limited the categories to topwear, bottomwear, and shoes, and the top three colors in the data.

## Models

### Conditional GAN *Rahul*

### Conditional VAE *Rahul*

### Multi-Conditional GAN *Henry*

The conditional GAN  uses a traditional GAN architecture(cGAN) except with an extra class label in order to "condition" the GAN to conform to that label. This is usually done through taking the class label as an extra input and concatenating it with the latent vector of the GAN.

In ourconcatenation of 

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
aGlzdG9yeSI6Wy04Njc0MjEwNzUsMTY1MTEyOTUxNiw5Njg3OD
kyMjAsMTY4Mjg5NDUxNSwtMTE0NTQ2OTgwMSwxMTM5NTY3NTc3
LDUzNzAxMzY0N119
-->