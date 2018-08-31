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

Recently I read a [paper by Malhotra et. al.](https://arxiv.org/abs/1608.06154) about how to use __LSTM Autoencoders__ to predict the **R**emaining **U**seful **L**ife (RUL) of a machine.

----

* Turbofan Sensor Data from Nasa
* Reduce Number of Sensors with PCA
* Train an LSTM Autoencoder predict these sensor readings of a machine in healthy state (It is assumed that the instance is in healthy condition during the first steps)
* Let the autoencoder reconstruct the entire series
* See that the reconstruction error is correlated with the machine's health -> definition of health index HI
* estimate the RUL with function matching

Checkout my **tensorflow** implementation on [Github](#).

Just run
```shell
python -m rul_estimate.turbofan_rul --train_ae
```
to train the autoencoder part.

Afterwards estimate the Health Index for the training instances with
```shell
python -m rul_estimate.turbofan_rul --target_hi
```


$$ e^{i\pi} + 1 = 0 $$