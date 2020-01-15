---
layout: post
title: "Metrics in linear regression"
date: 2020-01-15
---

# 1. Optimal interpolation

Resulting correction ![equation](https://latex.codecogs.com/gif.latex?x_i%5E%7Ba%7D) can be expressed as a weighted linear combination 
of the differences between observed and modeled time series 
![equation](https://latex.codecogs.com/png.latex?%5CDelta_n%28%5Ctau%29%20%3D%20y_n%5E%7Bo%7D%28%5Ctau%29-Hx_n%5E%7Bb%7D%28%5Ctau%29) 

![equation](https://latex.codecogs.com/png.latex?%5CDelta_n%28%5Ctau%29%20%3D%20y_n%5E%7Bo%7D%28%5Ctau%29-Hx_n%5E%7Bb%7D%28%5Ctau%29) 

$$$x_i^{a}(t) = x_i^{b}(t) + \sum_{n=1}^{N} \sum_{\tau=t-T}^{t+T} w_n^{(i,t)}(\tau)\Delta_n(\tau)$$$

$$x_i^{a}(t) = x_i^{b}(t) + \sum_{n=1}^{N} \sum_{\tau=t-T}^{t+T} w_n^{(i,t)}(\tau)\Delta_n(\tau)$$

$x_i^{a}(t) = x_i^{b}(t) + \sum_{n=1}^{N} \sum_{\tau=t-T}^{t+T} w_n^{(i,t)}(\tau)\Delta_n(\tau)$

\begin{equation}x_i^{a}(t) = x_i^{b}(t) + \sum_{n=1}^{N} \sum_{\tau=t-T}^{t+T} w_n^{(i,t)}(\tau)\Delta_n(\tau)\end{equation}

![equation](https://latex.codecogs.com/png.latex?x_i%5E%7Ba%7D%28t%29%20%3D%20x_i%5E%7Bb%7D%28t%29%20&plus;%20%5Csum_%7Bn%3D1%7D%5E%7BN%7D%20%5Csum_%7B%5Ctau%3Dt-T%7D%5E%7Bt&plus;T%7D%20w_n%5E%7B%28i%2Ct%29%7D%28%5Ctau%29%5CDelta_n%28%5Ctau%29)

where:

![equation](https://latex.codecogs.com/png.latex?N) - number of observation points,

![equation](https://latex.codecogs.com/png.latex?t) - time instance of the background model, \n
![equation](https://latex.codecogs.com/png.latex?T) - maximum time lag that depends on the variability of the studied process.

The conventional OI results in the Best Linear Unbiased Estimate (BLUE) ![equation](https://latex.codecogs.com/png.latex?%5Ctextbf%7Bx%7D%5E%7Ba%7D) of the true system state column vector 
![equation](https://latex.codecogs.com/png.latex?%5Ctextbf%7Bx%7D%5E%7Bt%7D) having available vector of observations ![equation](https://latex.codecogs.com/png.latex?%5Ctextbf%7By%7D%5E%7Bo%7D), 
initial background field state vector ![equation](https://latex.codecogs.com/png.latex?%5Ctextbf%7Bx%7D%5E%7Bb%7D), and estimated error covariances:

![equation](https://latex.codecogs.com/png.latex?%5Ctextbf%7Bx%7D%5E%7Ba%7D%3D%5Ctextbf%7Bx%7D%5E%7Bb%7D&plus;%5Ctextbf%7BK%7D%28%5Ctextbf%7By%7D%5E%7Bo%7D-%5Ctextbf%7BH%7D%5Ctextbf%7Bx%7D%5E%7Bb%7D%29)

![equation](https://latex.codecogs.com/png.latex?%5Ctextbf%7BK%7D%3D%5Ctextbf%7BP%7D%5E%7Bb%7D%5Ctextbf%7BH%7D%5E%7BT%7D%28%5Ctextbf%7BH%7D%5Ctextbf%7BP%7D%5Eb%5Ctextbf%7BH%7D%5E%7BT%7D&plus;%5Ctextbf%7BR%7D%29%5E%7B-1%7D)

where:\
![equation](https://latex.codecogs.com/png.latex?%5Ctextbf%7BP%7D%5Eb%20%3D%20%5Clangle%20%28%5Ctextbf%7Bx%7D%5Eb%20-%20%5Ctextbf%7Bx%7D%5Et%29%7B%28%5Ctextbf%7Bx%7D%5Eb%20-%20%5Ctextbf%7Bx%7D%5Et%29%7D%5ET%20%5Crangle%24%20and%20%24%5Ctextbf%7BR%7D%20%3D%20%5Clangle%20%28%5Ctextbf%7By%7D%5Eo%20-%20%5Ctextbf%7Bx%7D%5Et%29%7B%28%5Ctextbf%7By%7D%5Eo%20-%20%5Ctextbf%7Bx%7D%5Et%29%7D%5ET%20%5Crangle) are the matrices of estimated covariances of the background field and observation errors, respectively;\
![equation](https://latex.codecogs.com/png.latex?%5Ctextbf%7BH%7D) - the representation matrix of mapping observations onto the corresponding model grid;\
![equation](https://latex.codecogs.com/png.latex?%5Cmathbf%7BK%7D) - the weight matrix referred to as the Kalman gains, since the OI corresponds to the analysis stage of the Kalman filter. 
