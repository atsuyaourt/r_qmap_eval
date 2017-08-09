-   [Load the libraries](#load-the-libraries)
-   [Load test data](#load-test-data)
-   [Bias Correction using
    *QmapQuant*](#bias-correction-using-qmapquant)

This evaluates the capability of the R package
[*qmap*](https://cran.r-project.org/web/packages/qmap/index.html) to do
empirical adjustments to the output of a regional climate model
simulation.

### Load the libraries

    library(qmap)

    ## Loading required package: fitdistrplus

    ## Loading required package: MASS

    ## Loading required package: survival

    library(reshape2)
    library(ggplot2) # for plotting
    library(cowplot)

    ## 
    ## Attaching package: 'cowplot'

    ## The following object is masked from 'package:ggplot2':
    ## 
    ##     ggsave

### Load test data

The observed data:

    obs <- read.csv("input/obs_ts.csv")

The output of the model simulation:

    mod <- read.csv("input/mod_ts.csv")

<table>
<caption><strong>Table 1.</strong> Summary of values</caption>
<thead>
<tr class="header">
<th></th>
<th align="right">Observed</th>
<th align="right">Modelled</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Min.</td>
<td align="right">23.82540</td>
<td align="right">26.59500</td>
</tr>
<tr class="even">
<td>1st Qu.</td>
<td align="right">27.07358</td>
<td align="right">28.70000</td>
</tr>
<tr class="odd">
<td>Median</td>
<td align="right">27.65350</td>
<td align="right">29.34700</td>
</tr>
<tr class="even">
<td>Mean</td>
<td align="right">27.59976</td>
<td align="right">29.18911</td>
</tr>
<tr class="odd">
<td>3rd Qu.</td>
<td align="right">28.18658</td>
<td align="right">29.79600</td>
</tr>
<tr class="even">
<td>Max.</td>
<td align="right">31.57410</td>
<td align="right">31.43100</td>
</tr>
</tbody>
</table>

We see from Table 1 that the modelled result distribution is different
from that of the observed.

### Bias Correction using *QmapQuant*

Non-parametric quantile mapping using empirical quantiles can be done
using *fitQmapQUANt* and *doQmapQUANT*. *fitQmapQUANT* estimates the
[empirical cumulative distribution
function](https://en.wikipedia.org/wiki/Empirical_distribution_function)
(ECDF) for both the observed and modelled time series for the specified
quantiles. This will then be used by *doQmapQUANT* for bias-correction.

Figure 1 illustrates how the mapping is done. To obtain the
bias-corected value:  
1. Compute the quantiles of the modelled data set (*modq*)  
2. Compute the quantiles of the observed data set (*fitq*)  
3. Determine the new value by matching the quantile ranks of *modq* with
*fitq*

As an example, the bias-corrected value of 28.7**<sup>∘</sup>C is
27.07**<sup>∘</sup>C because they have the same quantile rank.

    fit <- fitQmapQUANT(obs$val, mod$val)
    bc_mod <- doQmapQUANT(mod$val, fit)

![Figure 1. QQ
plot](r_qmap_eval_files/figure-markdown_strict/plot_quant-1.png)

Table 2 confirms that the result after bias correction has the same
distribution as the observed data set.

<table>
<caption><strong>Table 2.</strong> Summary of values</caption>
<thead>
<tr class="header">
<th></th>
<th align="right">Observed</th>
<th align="right">Modelled</th>
<th align="right">BiasCorrected</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Min.</td>
<td align="right">23.82540</td>
<td align="right">26.59500</td>
<td align="right">23.82540</td>
</tr>
<tr class="even">
<td>1st Qu.</td>
<td align="right">27.07358</td>
<td align="right">28.70000</td>
<td align="right">27.07350</td>
</tr>
<tr class="odd">
<td>Median</td>
<td align="right">27.65350</td>
<td align="right">29.34700</td>
<td align="right">27.65350</td>
</tr>
<tr class="even">
<td>Mean</td>
<td align="right">27.59976</td>
<td align="right">29.18911</td>
<td align="right">27.60388</td>
</tr>
<tr class="odd">
<td>3rd Qu.</td>
<td align="right">28.18658</td>
<td align="right">29.79600</td>
<td align="right">28.18673</td>
</tr>
<tr class="even">
<td>Max.</td>
<td align="right">31.57410</td>
<td align="right">31.43100</td>
<td align="right">31.57410</td>
</tr>
</tbody>
</table>
