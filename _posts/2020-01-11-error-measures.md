---
layout: post
title: "Error measures for time series with examples from meteorology"
date: 2020-01-11
---

Any computational model -- no matter if this is the machine learning model or numerical solution of differential equations -- must go through the **validation** procedure. It is needed to determine the extent to which model's outcomes fit the real process and determine the range of validity of the model and its capability to reproduce a physical process of interest with an acceptable accuracy. 

In \[1\], seven properties are proposed validation metrics ideally should have. These properties form a general framework for the development of new metrics depending on the subject area and modeling goals. In particular, any metric must possess the metric properties in the mathematical sense as a measure of the residual: 
\\[d(x, y) \geq 0 \\]
\\[d(x, y)=d(y, x)\\]
\\[d(x, y)+d(y, z) \geq d(x, z)\\]
\\[d(x, y)=0 \Leftrightarrow x=y\\]

This post reviews the most common error measures and metrics, discusses their benefits and limitations. Having a set of error measures at hand, the next step in the development of validation metrics is to use them to evaluate the predictive (descriptive) ability of the model in the presence of **experimental uncertainties**. The Bayesian approach described in can be used for these purposes \[2\]. 

A distinction is made between “measure” and “metric”. A **error measure** is a numerical value associated with a difference w.r.t. a particular property of the compared time series. The **error metric** is a generalized numerical characteristic of the compared time series inconsistency and can include a single error measure or combination of several measures. Often, a single measure of error does not give a complete description of the difference between time series in to be used as a metric. 

# 1. Vector norms

In cases  where observations and simulation results are discrete, i.e. have a finite dimension, vector norms are the frequently used as measures.
Let \\(S\left(x_{i}\right)\\) means the modelling outcome as a function of some parameter \\(x_i\\) (e.g. time in dynamic systems) and \\(\mathcal{E}\left(x_{i}\right)\\) are the corresponding experimental observations. 

Vector norms such as \\(L_1\\) -- **Manhattan distance**, \\(\ L_2\\) -- **Euclidean distance**, or  \\(L_{\infty}\\) -- **Infinity (maximum) norm** can be used under condition that the vectors \\(S\left(x_{i}\right)\\) and \\(\mathcal{E}\left(x_{i}\right)\\) have the same dimensionaliny \\(N\\):

\\[ \|\|S\left(x_{i}\right) - \mathcal{E}\left(x_{i}\right)\|\|^p = \left(\sum_{i=1}^{N} \|S\left(x_{i}\right)-\mathcal{E}\left(x_{i}\right)\|^{p}\right)^{1/p}, \\]

where: \\( p = 1,2, \infty \\).

The main limitation when using vector norms as error measures is that **they do not distinguish errors due to the difference in amplitude from the errors due to the time delay** when the shape of the measured time series is satisfactorily reproduced. However, norms are the foundation for other measures.

# 2. Measures based on averaging

A simple approach is to compare the averaged characteristics of how the model outcome deviates from the experiment.

For the case of \\(N\\) pairs of modelled values, the difference between the observed \\(y_i\\) and modelled \\(\hat{y}_i\\) values is used in the calculation of the **Mean Absolute Error (MAE)**:

\\[E_{\operatorname{MAE}}=\frac{1}{N} \sum_{i=1}^{N}\|y_{i}-\hat{y}_{i}\|\\]

**Mean Squared Error (MSE)**:
\\[E_{\operatorname{MSE}}=\frac{1}{N} \sum_{i=1}^{N}\left(y_{i}-\hat{y}_{i}\right)^2\\] 

**Root Mean Squared Error (RMSE)** (std. deviation of the residuals):
\\[E_{\operatorname{RMSE}}= \sqrt{ \frac{1}{N} \sum_{i=1}^{N}\left(y_{i}-\hat{y}_{i}\right)^2 }\\] 

In many cases, the weighting can be applied so that e.g. errors occurring in individual time intervals make a greater or lesser contribution to the overall estimate. In particular, the **Weighted RMSE**:

\\[E_{\operatorname{WMSE}}=\sqrt{\frac{1}{N} \sum_{i=1}^{N}\left(y_{i}-\hat{y_i}\right)^{T} w_{i}\left(y_{i}-\hat{y}_{i}\right)}\\]

The obvious drawback of measures above is their **dependence on the physical units** of quantities to which they are applied. This leads to the fact that such measures, defined for different output variables of a computational model and accordingly of a simulated system cannot be compared directly. 

One approach to this problem is the use of **relative errors**, in particular, the **mean absolute percentage error (MAPE)**: 

\\[E_{\operatorname{MAPE}}=\frac{100}{N} \sum_{i=1}^{N} \frac{\|y_{i}-\hat{y_i}\|}{\|y_{i}\|} \%\\]

Although this measure avoids problems with different units, the relative errors of this kind are impractical if any of the observed values is zero or the measured values are generally small.

Aslo we can use popular in the regression analysis, the **estimated std. deviation of the residual time series**:

\\[S_{N-1} = \sqrt{\frac{\sum_{i=1}^{N}\left(R_{i}-\bar{R}\right)}{N-1}}\\]

where \\(R_{i}=y_{i}-\hat{y}_{i}\\)

*An obvious drawback of the error measures based on avearging is their **sensitivity to the extreme values and outliers**. Another difficulty is that we have a single number that describes the overall quality of the model “on average” without considering possible time delays while having satisfactory reproduced shape of the measured time series. Thus, **such simple numerical metrics should not be used in isolation for time series**.*

# 3. Theil index
A convenient measure that can be used for model validation is the **Theil index** \[1\]:
\\[ E_{\text {Theil}}=\frac{\sqrt{\frac{1}{N} \sum_{i=1}^{N}\left(y_{i}-\hat{y_i}\right)^{2}}}{\sqrt{\frac{1}{N} \sum_{i=1}^{N} y_{i}^{2}}+\sqrt{\frac{1}{N} \sum_{i=1}^{N} \hat{y}_{i}^{2}}} \\] 

This measure gives values \[0,1\] which is an advantage over the measures described above in terrms of the interpretability. A value of the Theil index of zero means that the model perfectly reproduces the measured real values. The value of the index equal to unity means the lack of correspondence between the measured and obtained as a result of modeling values. A value of 0.2 -- 0.3 is considered as a satisfactory fit [1]. The Tail Index can be expanded to cover \\(N_p\\) independent series of output.

# 4. Correlation and cross-correlation
**Correlation coefficient** is a frequently used tool. It varies from -1 to +1, where a value of +1 means an ideal positive relationship between the sets of measurements, which means their identity in shape. A value of –1 indicates an ideal negative relationship or, in other words, that two time series are (scaled) mirror images of each other:

\\[ \rho = \frac{\sum_{i=1}^{N}(y_i - \bar{y_i})(\hat{y_i} - \bar{\hat{y_i}})}{\sqrt{\sum_{i=1}^{N} (y_i - \bar{y_i})^2 (\hat{y_i} - \bar{\hat{y_i}})^2 }} \\]

The development of the idea of correlation as a measure of error is **cross-correlation**. It is sometimes called a *rolling scalar product* in pattern recognition. It can be used to **estimate the phase shift between two time series**. 

# 5. Geer metric

In \[3\] T. Geer proposed a measure for comparing time series that have errors due to the phase shift and amplitude errors. These errors are calculated using expressions (13) and (14), respectively. The combined error (metric) (15) is then calculated as a generalized measure of the error between two time series.

\\[ A_{\operatorname{Geer}}=\sqrt{\frac{\Psi_{y y}}{\Psi_{g g}}}-1 \\]

\\[ P_{\operatorname{Geer}}=\frac{1}{\pi} \arccos \left(\frac{\Psi_{y \hat{y}}}{\sqrt{\Psi_{y y} \Psi_{\hat{y} \hat{y}}}}\right) \\]

\\[ E_{\operatorname{Geer}}=\sqrt{A_{\\operatorname{Geer}}^{2}+P_{\operatorname{Geer}}^{2}} \\]

where:

\\[ \Psi_{y y}=\frac{\sum_{i=1}^{N} y_{i}^{2}}{N}, \quad \Psi_{\hat{y} \hat{y}}=\frac{\sum_{i=1}^{N} \hat{y_i}^{2}}{N}, \quad \Psi_{\hat{y} y}=\frac{\sum_{i=1}^{N} y_{i} \hat{y}_{i}}{N}  \\]

The limitation of this measure is in the fact that the expression for \\( A_{\operatorname{Geer}} \\) is assymetric w.r.t. the experimental and model arguments. Separation of the considered error into phase and amplitude is advantageous when a deeper analysis of the error sources is required. However, the measure mixes all the information within a formula for \\( E_{\operatorname{Geer}} \\). Thus, this metric does not take into account the form of the considered time-series, which can lead to erroneous conclusions.

# 6. Russel measure

In \[4\] D. Russell proposed a set of measures for phase, amplitude, and cumulative errors for a stable numerical estimate of the difference between two time series. This metric is similar to the Gear's metric with the exception of the amplitude error component. The latter is defined in such a way that it has approximately the same scale as the phase error, when there is a comparable in magnitude difference in amplitude between the signals. The phase and amplitude errors are then combined into a generalized error metric as it is in the Geer's metric. The amplitude error is defined for this composite measure as:

\\[ A_{\operatorname{Russel}} = \operatorname{sign}(\Psi_{yy} - \Psi_{\hat{y} \hat{y}}) \lg\( 1+\|\frac{\Psi_{y y}-\Psi_{\hat{y} \hat{y}}}{\sqrt{\Psi_{y y} \Psi_{g g}}}\| \)  \\]

Despite overcoming the problem with asymmetry it was in Gear measure, this metric can also lead to erroneous results in case of a small number of measurements, which, however, is unlikely in real model scenarios.

# 7. Normalized Integrated Square Error (NISE)

This error measure is used to estimate the discrepancy between two time series in the case of the repeated tests. Conceptually, it is related to cross-correlation. This measure takes into account three aspects: phase shift, amplitude error, and the difference in the shape of the compared time series.

This metric uses cross-correlation to find the  \\(n^* \\) -- the  time delay between signals as a maximum of the cross-correlation function. Then one of the rows is shifted over the second one by \\(n^*\\) time samples to compensate for the time shift. The value \\( \Psi{y \hat{y}} \\) is calculated after the compensation. The expressions for calculating the phase and amplitude errors, and distortion of the waveform, respectively, are the following:

\\[ E_{\text{phase}}=\frac{2 \Psi_{\hat{y} y}\left(n^{*}\right)-2 \Psi_{\hat{y} y}}{\Psi_{y y}+\Psi_{\hat{y} \hat{y}}} \\]

\\[ E_{\operatorname{ampl}}=\rho\(n^* \)-\frac{2 \Psi_{\hat{y} y}\left(n^{*}\right)}{\Psi_{y y}+\Psi_{\hat{y} \hat{y}}} \\]

\\[ E_{\text{shape}}=1-\rho\left(n^{*}\right) \\]

Then these measures are combined into the resulting NISE metric as:

\\[ E_{\operatorname{NISE}}=E_{\text{phase}} + E_{\operatorname{ampl}} + E_{\text{shape}}=1-\frac{2 \Psi_{\hat{y} y}}{\Psi_{y y}+\Psi_{\hat{y} \hat{y}}} \\]

# 8. Dynamic Time Warping (DTW)

The Dynamic Timeline Transformation algorithm or DTW measures the discrepancy between time series and was first used for speech recognition problems in the 1960s. In its basic version, this technique aligns in time the local maximums and minimums of signals by compressing or expanding the time dimension in accordance with some cost (distance) function.

Consider the following cost function as an example (assumed that modelled and measured vectors are of the same length):

\\[ d[i, j]=\left(y_{i}-\hat{y}_{j}\right)^{2} \\]

Let \\( w_{k}=\left(i_{k}, j_{k}\right) \\) denotes the indices of ordered pairs of time samples of series \\( \mathbf{y} \\) and \\( \mathbf{\hat{y}} \\). The DWT algorithm then searches for a monotonically increasing sequence of the ordered adjacent pairs so that the cumulative cost function (the sum of the cost functions for \\( k = 1,2, ..., N \\) is minimized. In other words, the sequence \\( \left\langle w_{1}, w_{2}, \dots, w_{N}\right\rangle \\) is calculated so that the cost function, taking into account the restrictions on the sequence progressing with each time step \\( \(0 \leq i_{k} - i_{k-1} \leq 1 \text{\ and \ } 0 \leq j_{k} - j_{k-1} \leq 1, \ k=2, 3, \ldots, N+1 \)  \\). Finally, the metric is calculated as the square root of the cumulative cost function.

# Summary

The purpose of this post was to review the most known and widely used measures and metrics for estimating the error between the discrete time samples from a computational model and the corresponding experiment. The measures considered here are summarized and briefly described in the table:

<style type="text/css">
.tg  {border-collapse:collapse;border-spacing:0;margin:0px auto;}
.tg td{font-family:Arial, sans-serif;font-size:14px;padding:10px 5px;border-style:solid;border-width:1px;overflow:hidden;word-break:normal;border-color:black;}
.tg th{font-family:Arial, sans-serif;font-size:14px;font-weight:normal;padding:10px 5px;border-style:solid;border-width:1px;overflow:hidden;word-break:normal;border-color:black;}
.tg .tg-4c16{font-weight:bold;font-size:20px;font-family:"Times New Roman", Times, serif !important;;border-color:inherit;text-align:left;vertical-align:top}
.tg .tg-fljg{font-size:20px;font-family:"Times New Roman", Times, serif !important;;text-align:left;vertical-align:top}
.tg .tg-tlzb{font-size:20px;font-family:"Times New Roman", Times, serif !important;;border-color:inherit;text-align:left;vertical-align:top}
</style>
<table class="tg">
  <tr>
    <th class="tg-4c16">Measure/metric</th>
    <th class="tg-4c16">Description</th>
    <th class="tg-4c16">Drawbacks</th>
  </tr>
  <tr>
    <td class="tg-tlzb"><span style="font-weight:bold">1.</span> Vector norms </td>
    <td class="tg-tlzb">Functionality defined on the vector space of the considered time series.<br></td>
    <td class="tg-tlzb">Sensitive to the phase errors.</td>
  </tr>
  <tr>
    <td class="tg-tlzb"><span style="font-weight:bold">2.</span> MAE, MSE, RMSE</td>
    <td class="tg-tlzb">Sample characteristic of the difference vector spread.</td>
    <td class="tg-tlzb">Defined in physical units of the studied values.<br>Sensitive to extreme values and outliers.</td>
  </tr>
  <tr>
    <td class="tg-tlzb"><span style="font-weight:bold">3.</span> Std. deviation of the residual time series</td>
    <td class="tg-tlzb">Sample characteristic of the difference vector spread and position.</td>
    <td class="tg-tlzb">Positive and negative values can compensate each other. <br>Sensitive to extreme values and outliers.</td>
  </tr>
  <tr>
    <td class="tg-fljg"><span style="font-weight:bold">4.</span> Theil's index </td>
    <td class="tg-fljg">Index of time series similarity.</td>
    <td class="tg-fljg">Not-invariant w.r.t. to summation.</td>
  </tr>
  <tr>
    <td class="tg-fljg"><span style="font-weight:bold">5. </span>Correlation coefficient. Cross-correlation.<br>Coefficient of determination</td>
    <td class="tg-fljg">Statistical measure of the linear relationship between two samples.</td>
    <td class="tg-fljg">Sensitive to phase shifts between signals and does not distinguish <br>errors caused by phase shifts from the amplitude errors.</td>
  </tr>
  <tr>
    <td class="tg-fljg"><span style="font-weight:bold">6.</span> Geer metric</td>
    <td class="tg-fljg">Composite measure taking into account amplitude and phase errors.</td>
    <td class="tg-fljg">Ignores similarity in shapes of compared time series, <br>asymmetry.</td>
  </tr>
  <tr>
    <td class="tg-fljg"><span style="font-weight:bold">7.</span> Russel metric</td>
    <td class="tg-fljg">Composite measure taking into account amplitude and phase errors. <br>Analogous to the Geer metric.</td>
    <td class="tg-fljg">Ignores similarity in shapes of compared time series.</td>
  </tr>
  <tr>
    <td class="tg-fljg"><span style="font-weight:bold">8. </span>Normalized Integrated Square Error (NISE)</td>
    <td class="tg-fljg">Composite measure taking into account amplitude and phase errors. <br>Takes into account similarity in shapes of the compared time-series.</td>
    <td class="tg-fljg">The value of the amplitude error component may be negative and reducing by this the overall error value.</td>
  </tr>
  <tr>
    <td class="tg-fljg"><span style="font-weight:bold">9.</span> Dynamic Time Warping (DTW)</td>
    <td class="tg-fljg">Algorithm for finding the best fit between compared time series.</td>
    <td class="tg-fljg">Tends to a non-obvious alignment due to incorrect transformation of time axes.</td>
  </tr>
</table>

# Examples

As an example let us consider comparison of the two pairs of time series: modelled and actually observed wave heights plus modelled and observed level of the sea both expressed in meters. Note that the **relative** degree of similarity between the time series pairs are diffrent.

<img src="\01_15_n1.png" alt="Markdown Monster icon" style="float: left; margin-left: 180px; width:70%" />
<img src="\01_15_n2.png" alt="Markdown Monster icon" style="float: letf; margin-left: 180px; width:70%" />

Then we have measured the different measures/metrics to express their similarity and summarized in the table below:

<style type="text/css">
.tg  {border-collapse:collapse;border-spacing:0;margin:0px auto;}
.tg td{font-family:Arial, sans-serif;font-size:14px;padding:10px 5px;border-style:solid;border-width:1px;overflow:hidden;word-break:normal;border-color:black;}
.tg th{font-family:Arial, sans-serif;font-size:14px;font-weight:normal;padding:10px 5px;border-style:solid;border-width:1px;overflow:hidden;word-break:normal;border-color:black;}
.tg .tg-fljg{font-size:20px;font-family:"Times New Roman", Times, serif !important;;text-align:left;vertical-align:top}
.tg .tg-p7zg{font-size:20px;font-family:"Times New Roman", Times, serif !important;;text-align:left;vertical-align:middle}
</style>
<table class="tg">
  <tr>
    <th class="tg-p7zg"><span style="font-weight:bold">Measure/metric</span></th>
    <th class="tg-p7zg"><span style="font-weight:bold">Value for wave heights</span></th>
    <th class="tg-p7zg"><span style="font-weight:bold">Value for the sea level</span></th>
  </tr>
  <tr>
    <td class="tg-p7zg"><span style="font-weight:bold">1.</span> Vector norms:<br>- L1<br>- L2<br>- L inf</td>
    <td class="tg-p7zg"><br>119.56<br>6.59<br>1.09</td>
    <td class="tg-p7zg"><br>3.48e3<br>163.01 <br>39.9</td>
  </tr>
  <tr>
    <td class="tg-p7zg"><span style="font-weight:bold">2. </span>MAE</td>
    <td class="tg-p7zg">0.19</td>
    <td class="tg-p7zg">1.38</td>
  </tr>
  <tr>
    <td class="tg-p7zg"><span style="font-weight:bold">3.</span> MSE (RMSE)</td>
    <td class="tg-p7zg">0.069 (0.26)</td>
    <td class="tg-p7zg">10.59 (3.25)</td>
  </tr>
  <tr>
    <td class="tg-p7zg"><span style="font-weight:bold">4. </span>Weighted RMSE </td>
    <td class="tg-p7zg">15.31 (w = 3 for values &gt; 2 m) </td>
    <td class="tg-p7zg">Not evaluated</td>
  </tr>
  <tr>
    <td class="tg-p7zg"><span style="font-weight:bold">5.</span> Mean absolute percentage error</td>
    <td class="tg-p7zg">17,72 %</td>
    <td class="tg-p7zg">10.46 %</td>
  </tr>
  <tr>
    <td class="tg-p7zg"><span style="font-weight:bold">6.</span> Mean of the residual</td>
    <td class="tg-p7zg">- 0.12</td>
    <td class="tg-p7zg">0.03</td>
  </tr>
  <tr>
    <td class="tg-p7zg"><span style="font-weight:bold">7.</span> Std. deviation of the residual</td>
    <td class="tg-p7zg">0.23</td>
    <td class="tg-p7zg">3.25</td>
  </tr>
  <tr>
    <td class="tg-p7zg"><span style="font-weight:bold">8. </span>Theil index</td>
    <td class="tg-p7zg">0.10</td>
    <td class="tg-p7zg">0.04</td>
  </tr>
  <tr>
    <td class="tg-p7zg"><span style="font-weight:bold">9.</span> Correlation coefficient</td>
    <td class="tg-p7zg">0.93</td>
    <td class="tg-p7zg">0.99</td>
  </tr>
  <tr>
    <td class="tg-fljg"><span style="font-weight:bold">10.</span> Geer metric</td>
    <td class="tg-fljg">0.30</td>
    <td class="tg-fljg">0.49</td>
  </tr>
  <tr>
    <td class="tg-fljg"><span style="font-weight:bold">11. </span>Russel metric</td>
    <td class="tg-fljg">0.29</td>
    <td class="tg-fljg">0.49</td>
  </tr>
  <tr>
    <td class="tg-fljg"><span style="font-weight:bold">12. </span>NISE</td>
    <td class="tg-fljg">0.02</td>
    <td class="tg-fljg">0.004</td>
  </tr>
  <tr>
    <td class="tg-fljg"><span style="font-weight:bold">13.</span> DTW</td>
    <td class="tg-fljg">14.06</td>
    <td class="tg-fljg">12.33e3</td>
  </tr>
</table>


Then, do the same but move from 1D to 2D i.e. the modelled changing in time field of the ocean surface temperature and calculate the difference measures between the neighbouring points of the field:

<img src="\arc_1.png" style="float: center; margin-left: 270px; width:60%" class="row"/>
<img src="\arc_2.png" style="float: center; margin-left: 270px; width:60%"  class="row"/>
<img src="\arc_3.png" style="float: center; margin-left: 270px; width:60%"  class="row"/>
<img src="\arc_4.png" style="float: center; margin-left: 270px; width:60%"  class="row"/>
<img src="\arc_5.png" style="float: center; margin-left: 270px; width:60%"  class="row"/>
<img src="\arc_6.png" style="float: center; margin-left: 270px; width:60%"  class="row"/>
<img src="\arc_7.png" style="float: center; margin-left: 270px; width:60%"  class="row"/>
<img src="\arc_8.png" style="float: center; margin-left: 270px; width:60%"  class="row"/>
<img src="\arc_9.png" style="float: center; margin-left: 270px; width:60%"  class="row"/>
<img src="\arc_10.png" style="float: center; margin-left: 270px; width:60%"  class="row"/>
<img src="\arc_11.png" style="float: center; margin-left: 270px; width:60%"  class="row"/>
<img src="\arc_12.png" style="float: center; margin-left: 270px; width:60%"  class="row"/>

# References

\[1\] Testing and Validation of Computer Simulation Models / Murray- Smith David J. : Springer, 2015

\[2\] Model Predictive Capability Assessment Under Uncertainty / Rebba R., Mahadevan S.:AIAAJ, 2006

\[3\] Objective Error Measure for the Comparison of calculated and Measured Transient Response Histories / Geer T.: Shock and Vibration Bulletin, 1984

\[4\] Error Measures for Comparing Transient Data, / Russel D.: Proceedings of the 68th Shock and Vibration Symposium, 1997
