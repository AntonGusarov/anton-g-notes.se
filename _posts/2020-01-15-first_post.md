---
layout: post
title: "Error measures and metrics over time-series data"
date: 2020-01-15
---

Any computational model -- no matter if this is the machine learning model or numerical solution of differential equations -- must go through the **validation** procedure. It is needed to determine the extent to which model's outcomes fit the real process and determine the range of validity of the model and its capability to reproduce a physical process of interest with an acceptable accuracy. 

In \[1\], seven properties are proposed validation metrics ideally should have. These properties form a general framework for the development of new metrics depending on the subject area and modeling goals. In particular, any metric must possess the metric properties in the mathematical sense as a measure of the residual: 
\\[d(x, y) \geq 0 \\]
\\[d(x, y)=d(y, x)\\]
\\[d(x, y)+d(y, z) \geq d(x, z)\\]
\\[d(x, y)=0 \Leftrightarrow x=y\\]

This post reviews the most common error measures and metrics, discusses their benefits and limitations. Having a set of error measures at hand, the next step in the development of validation metrics is to use them to evaluate the predictive (descriptive) ability of the model in the presence of **experimental uncertainties**. The Bayesian approach described in can be used for these purposes \[2\]. 

This post describes some of the measures and metrics evaluating the deviation of modelled time-series from an experimental sample. In this context, a distinction is made between the concepts of “measure of error” and “metric of error”. A measure of error is a numerical value associated with a difference with respect to a particular property of the compared time series. The error metric is a generalized numerical characteristic of the inconsistency of the compared time series and can be of a single error measure or their combination. Often, single measure of error does not give a complete description of the difference between time series in order to be used as a metric. 

# 1. Vector norms

In cases  where observations and simulation results are discrete, i.e. have a finite dimension, vector norms are the frequently used measures.
Let \\(S\left(x_{i}\right)\\) means the modelling outcome as a function of some parameter \\(x_i\\) (e.g. time in dynamic systems) and \\(\mathcal{E}\left(x_{i}\right)\\) are the corresponding experimental observations. 

Vector norms such as \\(L_1\\) -- **Manhattan distance**, \\(\ L_2\\) -- **Euclidean distance**, or  \\(L_{\infty}\\) can be used under condition that the vectors \\(S\left(x_{i}\right)\\) and \\(\mathcal{E}\left(x_{i}\right)\\) have the same dimensionaliny \\(N\\):

\\[ \|\|S\left(x_{i}\right) - \mathcal{E}\left(x_{i}\right)\|\|^p = \left(\frac{1}{N} \sum_{i=1}^{N} \|S\left(x_{i}\right)-\mathcal{E}\left(x_{i}\right)\|^{p}\right)^{1/p}, \\]

where: \\( p = 1,2, \infty \\).

The main limitation when using vector norms as error measures is that **they do not distinguish errors due to the difference in amplitude from errors due to the time delay** when the shape of the measured time series is satisfactorily reproduced. However, norms are the foundation for other measures.

# 2. Measures based on averaging

A simple approach is to compare the averaged characteristics of the deviations of the modelled data from the experiment.

For the case of \\(N\\) pairs of modelled values, the difference between the observed \\(y_i\\) and simulated \\(\hat{y}_i\\) values is used in the calculation of the **Mean Absolute Error (MAE)**:

\\[E_{\operatorname{MAE}}=\frac{1}{N} \sum_{i=1}^{N}\|y_{i}-\hat{y}_{i}\|\\]

**Mean Squared Error (MSE)**:
\\[E_{\operatorname{MSE}}=\frac{1}{N} \sum_{i=1}^{N}\left(y_{i}-\hat{y}_{i}\right)^2\\] 

**Root Mean Squared Error (RMSE)** (std. deviation of the residuals):
\\[E_{\operatorname{RMSE}}= \sqrt{ \frac{1}{N} \sum_{i=1}^{N}\left(y_{i}-\hat{y}_{i}\right)^2 }\\] 

In many cases, the weighting can be applied so that errors occurring in individual time intervals make a greater or lesser contribution to the overall estimate. In particular, the **Weighted RMSE**:

\\[E_{\operatorname{WMSE}}=\sqrt{\frac{1}{N} \sum_{i=1}^{N}\left(y_{i}-\hat{y_i}\right)^{T} w_{i}\left(y_{i}-\hat{y}_{i}\right)}\\]

The obvious drawback of measures above is their **dependence on the physical units** of quantities to which they are applied. This leads to the fact that such measures, defined for different output variables of a computational model and accordingly of a simulated system cannot be directly compared in view of different measuremnt units. 

One approach to this problem is the use of **relative errors**, in particular, the **average absolute percentage error**: 

\\[E_{\operatorname{APE}}=\frac{100}{N} \sum_{i=1}^{N} \frac{\|y_{i}-\hat{y_i}\|}{\|y_{i}\|} \%\\]

Although this measure avoids problems with different units, the relative errors of this kind are impractical if any of the observed values is zero or the measured values are generally small.

Aslo we can use popular in the regression analysis, the **estimated std. deviation of the residual time serie**:

\\[S_{N-1} = \sqrt{\frac{\sum_{i=1}^{N}\left(R_{i}-\bar{R}\right)}{N-1}}\\]

where \\(R_{i}=y_{i}-\hat{y}_{i}\\)

*An obvious drawback of the error measures above that are based on avearging is their **sensitivity to extreme values and outliers**. Another difficulty is that we have a single number that describes the overall quality of the model “on average” without considering possible time delay with satisfactory reproduced shape of the measured time series. Thus, **such simple numerical metrics should not be used in isolation**.*

# 3. Theil index
A convenient measure that can be used for model validation is the **Theil index** \[1\]:
\\[ E_{\text {Theil}}=\frac{\sqrt{\frac{1}{N} \sum_{i=1}^{N}\left(y_{i}-\hat{y_i}\right)^{2}}}{\sqrt{\frac{1}{N} \sum_{i=1}^{N} y_{i}^{2}}+\sqrt{\frac{1}{N} \sum_{i=1}^{N} \hat{y}_{i}^{2}}} \\] 

This measure gives values \\( \[0,1\]\\) which is an advantage over the measures described above in terrms of the interpretability. A value of the Theil index of zero means that the model perfectly reproduces the measured real values. The value of the index equal to unity means the lack of correspondence between the measured and obtained as a result of modeling values. A value of \\(0.2–0.3\\) is considered as a satisfactory fit [1]. The Tail Index can be expanded to cover \\(N_p\\) independent series of output.

# 4. Correlation and cross-correlation
**Correlation coefficient** is a frequently used tool. It varies from -1 to +1, where a value of +1 means an ideal positive relationship between the sets of measurements, which means their identity in shape. A value of –1 indicates an ideal negative relationship or, in other words, that two time series are (scaled) mirror images of each other:

\\[ \rho = \frac{\sum_{i=1}^{N}(y_i - \bar{y_i})(\hat{y_i} - \bar{\hat{y_i}})}{\sqrt{\sum_{i=1}^{N} (y_i - \bar{y_i})^2 (\hat{y_i} - \bar{\hat{y_i}})^2 }} \\]

The development of the idea of correlation as a measure of error is **cross-correlation**. It is sometimes called a *rolling scalar product* in pattern recognition. It can be used to **estimate the phase shift between two time series**. 

# 5. Geer metric

In \[3\] T. Geer proposed a measure for comparing time series that have errors due to phase shift and amplitude errors. These errors are calculated using expressions (13) and (14), respectively. The combined error (metric) (15) is then calculated as a generalized measure of the error between two time series.

\\[ A_{\operatorname{Geer}}=\sqrt{\frac{\Psi_{y y}}{\Psi_{g g}}}-1 \\]

\\[ P_{\operatorname{Geer}}=\frac{1}{\pi} \arccos \left(\frac{\Psi_{y \hat{y}}}{\sqrt{\Psi_{y y} \Psi_{\hat{y} \hat{y}}}}\right) \\]

\\[ E_{\operatorname{Geer}}=\sqrt{A_{\\operatorname{Geer}}^{2}+P_{\operatorname{Geer}}^{2}} \\]