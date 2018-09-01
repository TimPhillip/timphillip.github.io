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

----

Recently I read a [paper by Malhotra et. al.](https://arxiv.org/abs/1608.06154) about how to use __LSTM Autoencoders__ to predict the **R**emaining **U**seful **L**ife (RUL) of a machine. The basic idea is to use an __Autoencoder__ ,which is trained on healthy state, as a measure of anomaly for an arbitray sequence of sensor readings.

----

#### Turbofan Sensor Data from Nasa

A [turbofan](https://en.wikipedia.org/wiki/Turbofan) is a kind a of jet engine that is widely used in aviation. It's critical to avoid failure and keep maintenance costs low at the same time. So predicting the remaining uselful life of an engine would be useful.


![NASA](https://www.nasa.gov/sites/all/themes/custom/nasatwo/images/nasa-logo.svg){: .align-right}
The algorithm is trained on the [NASA Turbofan Degradation Dataset](https://c3.nasa.gov/dashlink/resources/139/).
In this dataset engine degradation is carried out until failure while recording many sensor readings.
The Task is to predict the RUL of an unseen engine's sensor readings.


#### Derived Sensor Readings

The autoencoder in the paper is not directly trained on sensor readings. For training they use so called **derived sensors**, which are basically the (first 3) principle components of the entire sensor reading data.
These sensors are determined by performing a [Principle Component Analysis (PCA)](https://en.wikipedia.org/wiki/Principal_component_analysis) on the centered sensor readings. With this the dimensionalty of the data is reduced and (linear) correlation between sensors are removed.

#### LSTM Autoencoder
Train an LSTM Autoencoder to predict these dericed sensor readings of a machine in healthy state. It is assumed that the instance is in healthy condition during the first steps, so using cropped sequences from the start of the training data does the job.
Once trained, let the autoencoder reconstruct the entire series and record the reconstruction error.

#### Health Index (HI)
See that the reconstruction error is correlated with the machine's health -> definition of health index HI

$$ e^{i\pi} + 1 = 0 $$

The actual RUL is estimated by function matching with all the HI curves in the training set. (This part is not in my implementation for now)

Checkout my **Tensorflow** implementation on [Github](#).

Just run
```shell
python -m rul_estimate.turbofan_rul --train_ae
```
to train the autoencoder part.

Afterwards estimate the Health Index for the training instances with
```shell
python -m rul_estimate.turbofan_rul --target_hi
```