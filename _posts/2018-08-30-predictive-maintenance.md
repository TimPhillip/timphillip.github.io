---
layout: article
title:  "Predictive Maintenance using LSTM Autoencoder"
date:   2018-08-30 16:00:00 -0600
categories: blog ai

image:
    teaser: turbofan.gif
    feature: turbofan.png
    credit: Photo by Inspirationfeed on Unsplash

math: true
---

{% include toc.html %}

##### Predictive Maintenance
Nowadays machines are most likely maintained in fixed service intervals. This might lead to high inspection costs and even higher costs, when the machine breaks before the next routine service check. A big promise of Industry 4.0 and collecting sensor readings in terms of Big Data is to avoid such unecessary maintainance by predicting a service time interval that fits right the condition of your machine.

****

Recently I read a [paper by Malhotra et. al.](https://arxiv.org/abs/1608.06154) about how to use __LSTM Autoencoders__ to predict the **R**emaining **U**seful **L**ife (RUL) of a machine. The basic idea is to use an __Autoencoder__, which is trained on healthy state, as a measure of anomaly for an arbitray sequence of sensor readings.

----

#### Turbofan Sensor Data from Nasa

<img align= "left" src="https://upload.wikimedia.org/wikipedia/commons/4/44/Turbofan3_Unlabelled.gif" width="25%" style="margin:10px">
A [turbofan](https://en.wikipedia.org/wiki/Turbofan) is a kind a of jet engine that is widely used in aviation. It's critical to avoid failure and keep maintenance costs low at the same time. So predicting the remaining uselful life of an engine would be useful.


<img align= "right" src= "https://www.nasa.gov/sites/all/themes/custom/nasatwo/images/nasa-logo.svg">
The algorithm is trained on the [NASA Turbofan Degradation Dataset](https://c3.nasa.gov/dashlink/resources/139/).
In this dataset engine degradation is carried out until failure while recording many sensor readings.
The Task is to predict the RUL of an unseen engine's sensor readings.


#### Derived Sensor Readings
The autoencoder in the paper is not directly trained on sensor readings. For training they use so called **derived sensors** $$z_t$$, which are basically the (first 3) principle components of the entire sensor reading data.
These sensors are determined by performing a [Principle Component Analysis (PCA)](https://en.wikipedia.org/wiki/Principal_component_analysis) on the centered sensor readings. With this the dimensionalty of the data is reduced and (linear) correlation between sensors are removed.

<figure class="once" align="center">
    <img src="{{site.url}}/images/rul/example_pca.png" width="40%">
    <figcaption>Example for Principle Components derived by PCA</figcaption>
</figure>

#### LSTM Autoencoder
Train an LSTM Autoencoder to predict these derived sensor readings $$z_t$$ of a machine in healthy state. It is assumed that the instance is in healthy condition during the first steps, so using cropped sequences from the start of the training data does the job.
Once trained, let the autoencoder reconstruct the entire series and record the reconstruction error $e^{(u)}_t$.

<figure class="once" align="center">
    <img src="{{site.url}}/images/rul/autoencoder.png" width = "70%">
    <figcaption>Processing an example sequence of (derived) sensor readings $\langle z_1, z_2, z_3 \rangle$ with the autoencoder. Illustration is taken from the original paper.</figcaption>
</figure>

\\begin{equation}
e^{(u)}_t = \|| z^{(u)}_t - z'^{(u)}_t \||_2^2
\\end{equation}


#### Health Index (HI)
Based on the reconstruction error $e^{(u)}_t$ one can define a **health index (HI)**.

\\begin{equation}
h^{(u)}_t = \frac{e^{(u)}_M - e^{(u)}_t}{e^{(u)}_M - e^{(u)}_m}
\\end{equation}
with $e^{(u)}_M$ denoting the maximum error and $e^{(u)}_m$ denoting the minimum error.

See that the reconstruction error is correlated with the machine's health or lifetime respectively. The plots can be resproduced with the code provided in the repository.

<figure class="once" align="center">
    <img src="{{site.url}}/images/rul/normalized_hi.pdf" width = "80%">
    <figcaption>Reconstruction error based health index averaged over all training instances (and normalized by their lifetime). The errorbars indicate one standard deviation.</figcaption>
</figure>

The actual RUL is estimated by function matching with all the HI curves in the training set. (This part is not in my implementation for now)

Checkout my **Tensorflow** implementation on [Github](https://github.com/TimPhillip/rul_estimate).

Just run
```shell
python -m rul_estimate.turbofan_rul --train_ae
```
to train the autoencoder part.

Afterwards estimate the Health Index for the training instances with
```shell
python -m rul_estimate.turbofan_rul --target_hi
```

**Notice:** All ideas presented in this post are taken from the [paper by Malhotra et. al.](https://arxiv.org/abs/1608.06154). All implementations are reproductions based on my understanding of the paper.
{: .notice-info}