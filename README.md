# Fashion Generation from Structured Style Attributes

## Data 

### DeepFashion 

### MNIST Fashion  *Rahul*

The baseline dataset that we used is called "Fashion MNIST", a publicly available 

### Fashion Product Images *Henry*

We also trained several models on the Fashion Product Images dataset (https://www.kaggle.com/paramaggarwal/fashion-product-images-dataset) which contains 44,000 images of fashion products scraped from various e-commerce websites. The images are labeled by features such as gender, category, base color, season, usage, etc. We utilized two variations of the dataset, with one containing low-resolution images (60px by 80px) and another contained higher-resolution images (1800px x 2400px).

Simple image preprocessing was performed on the dataset, including resizing all the images into squares (64px x 64px for the smaller variation and 256px x 256px for the larger images), and normalizing all the images between the range 0 and 1.

We also limited the dataset in two different ways, one utilizing just apparel images and the other all types of fashion. For sake of simplicity in training the conditional GAN, we limited the apparel dataset to the top six subcategories of apparel and the top six base colors. For data utilizing all types of fashion, we limited the categories to topwear, bottomwear, and shoes, and the top three colors in the data.

## Models

### Conditional GAN *Rahul*

### Conditional VAE *Rahul*

### Multi-Conditional GAN *Henry*

The traditional conditional GAN (cGAN) 

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
aGlzdG9yeSI6WzE2ODI4OTQ1MTUsLTExNDU0Njk4MDEsMTEzOT
U2NzU3Nyw1MzcwMTM2NDddfQ==
-->