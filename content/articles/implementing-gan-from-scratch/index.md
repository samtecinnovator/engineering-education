---
layout: engineering-education
status: publish
published: true
url: /implementing-gan-from-scratch/
title: Implementing GANs from Scratch
description: In this article we will learn the basics of Generative Adversarial Networks and implement it from scratch in PyTorch. 
author: deewakar-chakraborty
date: 2021-10-21T00:00:00-17:00
topics: [Machine Learning]
excerpt_separator: <!--more-->
images:

  - url: /engineering-education/implementing-gan-from-scratch/hero.jpg
    alt: Implementing GANs example image
---
Machine learning algorithms work great when recognizing patterns in existing data. Then that insight can be used for classification and regression purposes. However, machines have struggled when asked to generate new data; this all changed in 2014 when Ian Goodfellow introduced the idea of [Generative Adversarial Networks (GANs)](/engineering-education/introduction-to-generative-adversarial-networks/) to the world of machine learning.
<!--more-->
The power of generative modeling makes GANs extend the capabilities of neural networks. This technique enabled machines to generate realistic data that plausibly come from an existing distribution of samples. 

This blog will look through the basics of GANs and implement one using the PyTorch library.

### Table of contents
- [Prerequisites](#prerequisites)
- [Key takeaways](#key-takeaways)
- [Introduction](#introduction)
  - [What is Generative Modeling](#what-is-generative-modeling)
  - [Introduction to GANs](#introduction-to-gans)
  - [GAN Architecture](#gan-architecture)
  - [Training a GAN](#training-a-gan)
- [Implementation](#implementation)
  - [Generator Network](#generator-network)
  - [Discriminator Network](#discriminator-network)
  - [Loading the Data](#loading-the-data)
  - [Training Phase](#training-phase)
  - [Generating fake data](#generating-fake-data)
- [Conclusion](#conclusion)

### Prerequisites
Although I have tried to make this article as self-contained as possible, basic prior knowledge in Machine Learning is still required. Also, readers are requested to refer to the documentation of the PyTorch library. PyTorch is Python based open-source Machine Learning library that we will use to implement the GAN. 

### Key takeaways
By the end of this blog, you will understand the following concepts:
- Introduction to generative modeling.
- What are GANs?
- How do GANs work?
- GAN Architecture.
- How to train a GAN.
- Implementing a GAN from scratch using PyTorch.

### Introduction
> **"Generative Adversarial Network is the most interesting idea in the last 10 years in ML"**
> 
> Yann LeCun, the Chief AI Scientist at Facebook

The word generative in GAN indicates its sole purpose, that is, generating new data. The data generated by the GANs depends on which dataset we have trained our GAN on. 

On the other hand, the word adversarial refers to the game-like competition between Generator and Discriminator about which we will learn in the latter part of the blog. 

Before beginning the discussion about GANs, it is essential to understand generative modeling as it is the foundation of the concept of a GAN.

#### What is Generative Modeling
To explain Generative Modeling, I will take an example of a scenario where we have been given a Machine Learning model that classifies a given image into a cat or dog image.

Now, what if we want to reverse this process, that is, we have been given features about a dog, and we want our model to generate an image of a dog by using the given features? That is what a Generative Model does in the simplest form.

To explain this mathematically, let's assume that we have a dataset with $x$ instances and corresponding $y$ labels. There are mainly two types of modeling in Machine Learning:
1. **Discriminative Modeling**: The model tries to capture the conditional probability, $p(y|x)$.
2. **Generative Modeling**: The model tries to capture the joint probability $p(x,y)$ if it is supervised learning, otherwise $p(x)$.

In Generative Modeling, the distribution $p(x)$ is learned and represented in a latent space $z$. The latent space is a hidden representation of $x$. Further, the Generative model uses this latent space as a source of inspiration to generate $x'$. To explain why the $z$ is called the source of inspiration, I will take an example of a human mind. 

A human mind does something similar when we use it as a generative model. For example, let us consider a scenario where we are trying to explain what a dog is to a person who has never seen a dog in his entire life. We will describe how a dog looks to a person by giving all necessary features of a dog $(x)$. A person's brain will then take this information $(p(x))$ and store it as encoded information in their mind $(z)$. 

Now using $(z)$ as a source of inspiration human brain will try to recreate how a dog would look like $(x')$. Note, the person's brain doesn't need to generate an exact image of how a dog looks in their brain. It will entirely depend on the information you gave $(x)$ and its modeling.

#### Introduction to GANs
GANs are the class of ML techniques that consist of two simultaneously trained neural networks. One is a generator used to generate the fake data, and the other is a discriminator, which is used to classify whether input given to it is real or fake. 

Imagine a scenario where a thief wants to steal a painting from a museum and replace it with a fake one, and there is a curator who has a job to detect whether the painting is real or fake. There will be a competition between the thief and curator, both of them will have an adversarial relationship. The thief is **Generator**, and the curator is **Discriminator** in our case.

#### GAN architecture

![architecture](/engineering-education/implementing-gan-from-scratch/architecture.JPG)

[Image source](https://livebook.manning.com/book/gans-in-action/chapter-3/v-3/10)

The architecture of GAN consists of the following components:
1. **Training Dataset$(x)$**: The real data sample we train our Discriminator network to distinguish between real and fake data. The type of data you want to generate depends on your training dataset. 
2. **Random Noise$(z)$**: It acts as a starting point (raw input) to our Generator network. This noise gets transformed into fake data with the help of the Generator.
3. **Generator Network**: It is a neural network that takes Random Noise$(z)$ as input and produces fake data$(x')$ as an output. Its goal is to generate the data which can not be detected as fake data by the Discriminator network.
4. **Discriminator Network**: It is also a neural network that takes data from the training dataset$(x)$ and fake data$(x')$ as input and classifies them as real or fake.
5. **Iterative Training**: As a GAN consists of two neural networks, the training phase simultaneously trains the Generator and Discriminator. We will see this in detail in the next part.

#### Training a GAN
As discussed before, GAN consists of two different neural networks, Discriminator and Generator, which are simultaneously trained. The Discriminator is trained directly on real and generated data, whereas the Generator is trained indirectly. It is trained via the Discriminator network. Let us see how this happens.

#### Training the Generator
- Take a random data sample $x$ from our training dataset, labeled as **real data**.
- Again, take a fake data sample $x'$ generated by the Generator network, labeled as **fake data**.

Both of the inputs are passed through the Discriminator neural network, which computes the classification errors.
Next, backpropagate the total error to update the weights and biases of the Discriminator network, seeking to **minimize** the classification errors.

#### Training the Discriminator
- Take a random noise vector $z$ as an input and generate a fake sample $x'$ using the Generator network.
- Pass $x'$ as an input to the Discriminator network to compute classification error.
- Backpropogate this classification error to update the weights and biases of the Generator network, seeking to **maximize** the Discriminator's error.

##### When does the training converge?
To explain how GANs are trained, first, I will discuss a setup in the game theory known as a **zero-sum game**, a two-player game in which one player improves by a certain amount and the other player worsens by the same amount. All zero-sum games have an equilibrium point at which neither player can improve their situation. 

This point is known as **nash equilibrium**. We have something similar here with the case of GAN. In GAN, a Discriminator competes with a Generator same as two players of zero-sum games. Similarly, the training of the GANs converges when it achieves nash equilibrium. 

Nash equilibrium is achieved in GAN when:
- The Discriminator classifies whether a particular data is real or fake with the probability of $0.5$.
- The Generator generates fake data that is indistinguishable from the real data.
  
In practice, it is nearly impossible to achieve nash equilibrium while training a GAN due to its training complexity which means training of GAN never converges practically. However, without even converging, GANs can produce satisfactory results if trained carefully.

### Implementation
To implement a GAN, we will be using the standard **MNIST dataset**. After training the GAN on this dataset we will try to generate new data based on the MNIST dataset.

First of all, install the PyTorch library if you are working on your local machine. If you are working in Google Colab, you don't need to worry about this as PyTorch is already installed.

```python
#importing essential libraries
import torch
import torch.optim as optim
import torch.nn as nn
from torch.utils import data
import torchvision

import matplotlib.pyplot as plt
import numpy as np
```

If you have a GPU present in your machine, the device will automatically be selected as **Cuda**; otherwise, it will be **CPU**. We are also setting up the hyperparameters required for training in the cell below.

```python
#setting up the hyperparameters
learning_rate = 2e-4
noise_dim = 32
image_dim = 28 * 28 * 1 
batch_size = 32
num_epochs = 25
```


#### Generator network
In the cell below, we have defined our Generator class which inherits from **nn.Module** class. We have defined our Generator model as a neural net consisting of two hidden layers only. 

The dimensions of the random noise $z$ and image are passed as arguments to the constructor of our class. Then, it takes the random noise as an input to the network.

```python
class Generator(nn.Module):
    def __init__(self, noise_dim, image_dim):
        super(Generator,self).__init__()
        self.linear1 = nn.Linear(noise_dim, 128)
        self.relu = nn.LeakyReLU(0.01)
        self.linear2 = nn.Linear(128, image_dim)
        self.tanh = nn.Tanh()

    def forward(self, x):
        out = self.linear1(x)
        out = self.relu(out)
        out = self.linear2(out)
        out = self.tanh(out)
        return out
```

#### Discriminator network
In the cell below, we define the Discriminator network as a class that inherits from `nn.Module class` from PyTorch. It is again a simple two-layered network like the Generator. First, it takes the image as an input. 

Notice that the activation function in the final layer is **sigmoid** which means the output of the network is between 0 and 1. 1 for the real image from the MNIST dataset and 0 for the fake image generated by the Generator network.

```python
class Discriminator(nn.Module):
    def __init__(self, in_image):
        super(Discriminator,self).__init__()
        self.linear1 = nn.Linear(in_image, 64)
        self.relu = nn.LeakyReLU(0.01)
        self.linear2 = nn.Linear(64, 1)
        self.sigmoid = nn.Sigmoid()

    def forward(self, x):
        out = self.linear1(x)
        out = self.relu(out)
        out = self.linear2(out)
        out = self.sigmoid(out)
        return out
```

#### Loading the data
We don't have to download the MNIST dataset explicitly, PyTorch library's **datasets** class will download and transform the MNIST dataset for us. 

In transformation, we need to normalize the images before feeding them to the neural network and converting them into a tensor. Now we will load data to feed to the neural net using the **DataLoader** class.

```python
discriminator = Discriminator(image_dim)
generator = Generator(noise_dim, image_dim)
noise = torch.randn((batch_size, noise_dim))
```

```python
tf = torchvision.transforms.Compose(
    [torchvision.transforms.ToTensor(),torchvision.transforms.Normalize((0.5,), (0.5,)),]
)
ds = torchvision.datasets.MNIST(root="dataset/", transform=tf, download=True)
loader = data.DataLoader(ds, batch_size=batch_size, shuffle=True)
```

```python
#Visualizing real data
real_sample = iter(loader).next()[0]
img_grid_real = torchvision.utils.make_grid(real_sample, normalize=True)
npgrid = img_grid_real.cpu().numpy()
plt.imshow(np.transpose(npgrid, (1, 2, 0)), interpolation='nearest')
plt.axis('off')
```

![snapshot](/engineering-education/implementing-gan-from-scratch/snapshot-1.png)

```python
#setting up the optimizers
opt_discriminator = optim.Adam(discriminator.parameters(), lr=learning_rate)
opt_generator = optim.Adam(generator.parameters(), lr=learning_rate)
criterion = nn.BCELoss()
```

#### Training phase
As we have discussed in the [training of GANs](#training-a-gan) earlier, the same principle is implemented programmatically with the help of the PyTorch library. 

Notice we are backpropagating **lossD**, which is the average of **lossD_real** and **lossD_fake** to update the weights and biases of the Discriminator network.

```python
for epoch in range(num_epochs):
    for id, (training_sample, _) in enumerate(loader):
        training_sample = training_sample.view(-1, 784)
        batch_size = training_sample.shape[0]

        ### Training the Discriminator
        noise = torch.randn(batch_size, noise_dim)
        fake_sample = generator(noise)
        disc_realSample = discriminator(training_sample).view(-1)
        lossD_realSample = criterion(disc_realSample, torch.ones_like(disc_realSample))
        disc_fakeSample = discriminator(fake_sample).view(-1)
        lossD_fakeSample = criterion(disc_fakeSample, torch.zeros_like(disc_fakeSample)) 
        lossD = (lossD_realSample + lossD_fakeSample) / 2
        discriminator.zero_grad()
        #we are trying to minimize the total classification error
        lossD.backward(retain_graph=True)
        opt_discriminator.step()

        ### Training the Generator
        lossD_fakeSample = discriminator(fake_sample).view(-1)
        lossG = criterion(lossD_fakeSample, torch.ones_like(lossD_fakeSample))#we are trying to maximize the error of classification of fake image by the discriminator
        generator.zero_grad()
        lossG.backward()
        opt_generator.step()

        if id == 0:
            print( "Epoch: {epoch} \t Discriminator Loss: {lossD} Generator Loss: {lossG}".format( epoch=epoch, lossD=lossD, lossG=lossG))
```


#### Generating fake data
Now that our training is complete, let us try to generate some new data. We are using fixed_noise to generate new data, thereby passing it to our generator network. 

As our batch size is $32$, there will be $32$ images returned by the Generator network. We are using **make_grid** of torchvision.utils to display all images together.

```python
fake = gen(fixed_noise).reshape(-1, 1, 28, 28)
img_grid_fake = torchvision.utils.make_grid(fake, normalize=True)
npgrid = img_grid_fake.cpu().numpy()
lt.imshow(np.transpose(npgrid, (1, 2, 0)), interpolation='nearest')
plt.axis('off')
```

![snapshot](/engineering-education/implementing-gan-from-scratch/snapshot-2.png)

### Conclusion
We have implemented a GAN in this blog, generating new data based on the MNIST dataset using random noise. A point to be noted is that we had to deal with MNIST data.

Here, the distribution is quite simple and modeled by the Generator, but what if our data was highly complex and had multi-model distribution. This is still an issue with the GANs and it restricts the ability of GANs to produce diverse results.

Happy coding!

---
Peer Review Contributions by: [Lalithnarayan C](/engineering-education/authors/lalithnarayan-c/)
