---
layout: post
title: "Once again about the bias-variance tradeoff"
date: 2020-01-22
---

Today consider the standard concepts in machine learning: **overfitting** and **underfitting**, its relation to the **model complexity** that are all integrated in the **bias-variace tradeoff**. Also, I will provide the *full* derivation of the bias-varince tradeoff formula which is often ignored in the literature and usually shown reduced just up to the final result.

The first is  [**overfitting**](https://en.wikipedia.org/wiki/Overfitting) -- phenomenon when the performance on the test sample is noticeably worse than the that on the train sample. This is the major problem of machine learning: if there was no such an effect i.e. the error on the test almost coincided with the error on the training, then all the training would be just reduced to minimizing the error on the test data (so-called [empirical risk minimization](https://en.wikipedia.org/wiki/Empirical_risk_minimization)) which doesn't apparently happen.

The second is  [**underfitting**](https://en.wikipedia.org/wiki/Overfitting) -- phenomenon where the error in the training sample is excessively large. Such a situation might be explained by the fact that underfitting can be observed  during the training process often when too few iterations of the train algorithm (e.g. back propagation) has been made i.e. "we did not have time to learn."

Third, **model complexity** describes how various is the ensemble of algorithms in the model class in terms of their functional properties (for example, the ability to fit the train samples). Increasing complexity (i.e. employing more complex models) solves the problem of underfitting but causes overfitting.

Now demonstrate with how the problem of complexity choice manifests itself and how appears the overfitting. To begin with, consider the **polynomial regression**. For simplicity, assume that this is a regression on a single covariate \\(x\\). The target \\(y\\) is known in a finite set of points. In figure below is shown approximations of the target but noisy functional dependency \\(y=f(x)\\) by the polynomial regression model of different orders:

<img src="\post_2_fig_1.png" style="float: center; margin-left: 270px; width:60%" class="row"/>

It can be seen that the polynomial of the order 1 is clearly not suitable for describing the target dependence -- clear **underfitting**. The model of order 2 describes it better, although there is a substantial errors on the training sample. With orders 3-5 we have even better fit with a smooth curve. The extreme model of order 20, does not at all look like a “natural function” and deviates significantly from the target dependence with too much following the noise in train data and thus affecting its validation score -- **overfitted model**.

If to plot the MSE performance of a similar polynomial model vs the model complexity, we can notice that with an increase in the order, the error in the training sample always decreases. While on the test sample it first falls then increases, indicating the optimal point of model complexity where bias-variance tradeoff is (which is not always the case though).

<img src="\post_2_fig_2.jpg" style="float: center; margin-left: 270px; width:45%" class="row"/>

Let us now figure out what is going out from the theoretical viewpoint. 

Say that exist some true \textit{deterministic} function that we want to approximate with our ML model
\\[
f=f(\mathbf{x})
\\]

We cannot observe outcomes of this unknown function directly but with some additive noisy component, say that \\(\operatorname{E} ( \varepsilon ) = 0\\)
\\[
t = f + \varepsilon
\\]

Consider a dataset:
\\[
\mathbf{D}=\left[ \left(t_{i}, \mathbf{x}_{i}\right), \ i=1 \ldots N \right]
\\]

Having this dataset \\(\mathbf{D}\\), we train a parametric model \\(g\\) (e.g. neural network or linear OLS regression) to approximate the function of interest $f$ based on the 'noised' observations \\(t\\):
\\[
y_i=g(\mathbf{x}, \mathbf{w})
\\]

Assume that the measure of quality of our model is MSE:
\\[
\operatorname{MSE} = E\left\[\left(t_i-y_i\right)^{2}\right\]
\\]

Do some math over this expectation:

\\[
E \left[ \left(t_i-y_i\right)^{2} \right] = \text{/add and substract hypothetical true values then associate/} = E\left[\left(\left(t_{i}-f_{i} \right) + \left(f_{i} -y_{i} \right) \right)^{2}\right] 
\\]

\\[
= E\left[ \left(t_{i}-f_{i}\right)^{2}\right]+E\left[\left(f_{i}-y_{i}\right)^{2} \right]+2 E\left[ \left(f_{i}-y_{i}\right)\left(t_{i}-f_{i}\right)\right] 
\\]

\\[
= E\left[\varepsilon^{2} \right]+E\left[\left(f_{i}-y_{i}\right)^{2} \right]+2\left(E\left[f_{i} t_{i}\right]-E\left[ f_{i}^{2}\right]-E\left[y_{i} t_{i}\right]+E\left[y_{i} f_{i}\right]\right) = E\left[ \varepsilon^{2} \right]+E\left[\left(f_{i}-y_{i}\right)^{2} \right] + 0
\\]

The last term goes to zero because:
* \\( E\left[ f_{i} t_{i} \right] = f_i^2 \Leftarrow E\left[ f_{i} (f_{i} + \varepsilon) \right] = E \left[ f_i^2 + f_i\varepsilon \right] = / f_i \ \text{is determenistic} / = E\left[ f_i^2 \right] + 0 = f_i^2; \\)

* \\( E\left[ y_{i} t_{i} \right] = E\left[y_{i}\left(f_{i}+\varepsilon\right) \right] = E\left[ y_{i} f_{i}+y_{i} \varepsilon\right] = E\left[ y_{i} f_{i} \right] + E\left[ y_{i} \right] E\left[ \varepsilon \right] = E\left[ y_{i} f_{i} \right]. \\)

So now we have
\\[
E\left[ \left(t_i-y_i\right)^{2} \right] = \sigma_{\varepsilon}^2 + E\left[ \left( f_{i}-y_{i} \right)^{2} \right]
\\]

where we can apply the same trick as before to the second term but adding and subtracting the mean of the model outcomes:
\\[
E\left[ \left(f_{i}-y_{i}\right)^{2} \right] = E\left[ \left(f_{i} - \bar{y_{i}}  + \bar{y_{i}} - y_{i}\right)^{2} \right] 
\\]

\\[
= E\left[ \left(f_{i}-\bar{y_{i}}\right)^{2} \right] + E\left[ \left(\bar{y_{i}}-y_{i}\right)^{2}\right] + 2E\left[ \left(\bar{y_{i}}-y_{i}\right)\left(f_{i}-\bar{y_{i}}\right) \right]
\\]

\\[
= E\left[ \left(f_{i}-\bar{y_{i}}\right)^{2} \right] + E\left[\left(\bar{y_{i}}-y_{i}\right)^{2} \right] + 2\left(E\left[f_{i} \bar{y_{i}}\right]-E\left[\bar{y_{i}}^{2}\right] - E\left[ y_{i} f_i \right] + E\left[y_{i} \bar{y_{i}}\right]\right)
\\]

The last term also cancels to zero because:
* \\( E\left[ f_{i} \bar{y_{i}} \right] = / f_i \ \text{is determenistic} / = f_{i}E \left[ \bar{y_{i}} \right] = f_{i} \bar{y_{i}} \\)

* \\( E\left[ \bar{y_{i}}^{2} \right] = / \text{mean is determenistic} / = \bar{y_{i}}^{2} \\)

* \\( E\left[ y_{i} f_i \right] = f_i\bar{y_{i}} \\)

* \\( E\left[ y_{i} \bar{y_{i}}\right] =\bar{y_{i}}^{2} \\)

Then, if to sum up we have the following:

\\[
\boxed{ \operatorname{MSE} = E\left[ \left(t_i-y_i\right)^{2} \right] = E\left[ \left(f_{i}-\bar{y_{i}}\right)^{2}\right] + E\left[ \left(\bar{y_{i}}-y_{i}\right)^{2}\right] + \sigma^2_\varepsilon}
\\]

\\[
\boxed{ \operatorname{MSE} = \operatorname{Bias}\left[ y \right] + \operatorname{Var} \left[ y \right] + \sigma_\varepsilon^2 }
\\]

Thus to minimize the MSE of the model, we have to minimize both the bias of the model as well its variance although it is not trivial!

The following is commonly true about the components of the bias-variance equation:

* **A high bias model** has a relatively large error, mostly due to *wrong assumptions about the data features*;

* **A high variance model** isquite *sensitive to small variations in the train data*;

* **Irreducible error** is due to the intrinsic *noisiness of the data itself*.

