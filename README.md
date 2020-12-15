# Z-Hist: A Temporal Abstraction of Multivariate  Histogram Snapshots

Hi! This is a GitHub repository for Z-Hist which is submitted to IDA 2021.

This repository contains our algorithm (ZHist) and the frequent arrangement mining algorithm called Z-Miner. For Z-Miner's usage, we would like to refer to [the corresponding repository](https://github.com/zedshape/zminer) and [paper](https://dl.acm.org/doi/abs/10.1145/3394486.3403095).) 

## Files

**ZHist.py**: This file contains our Z-Hist algorithm.
**ZMiner.py**: Original Z-Miner algorithm
**ZMinerD.py**: Z-Miner algorithm modified to perform the disproportionality analysis with the arrangement occurred in one set.
**utils.py**: Utility functions.

**datasets/**: A directory with six synthetic datasets sampled by a normal distribution and one synthetic dataset of turbo failure dataset used in the paper. The explanation of each dataset is in synthetic data analysis section.

## Synthetic data analysis
It is common for histogram snapshots to have a similar shape to normal distribution. Distribution changes over time can affect disproprotionality values between two different sets. To verify the relation, we have created a synthetic dataset generator which samples multivariate time series  from the normal distribution and create histogram snapshots by summarizing a specific range of time with different labels. Synthetic dataset generator is in the jupyter file (IDA2021_Synthetic_generator_and_testing.ipynb). The generator receives 11 input parameters as follows:
 - n_variables: number of histogram variables
- $n$: number of variables. 
- $m$: a list of possible number of bins for each variable. %The number is randomly selected for each variable from the list.
- $\textit{\textbf{t}}$: a list of possible snapshot durations for each snapshot. The number is randomly selected for each snapshot from the list.
- $\textit{\textbf{s}}$: a list of possible number of snapshots for each data record. The number is randomly selected for each data record from the list.
-  $\{|\mathcal{S}_{A}|, |\mathcal{S}_{B}|\}$: size of each subset in the multivariate histogram snapshot dataset. A total size of the dataset is $|\mathcal{S}_{A}|+|\mathcal{S_{B}}|$
- $\{p_{\mu}, p_{\sigma}\}$: the transition probability of mean and standard deviation. When generating each data record, the distribution can change its mean and standard deviation with the probability $p_{\mu}, p_{\sigma}$. 
- $\{w_{\mu}, w_{\sigma}\}$, growth rate of mean and standard deviation. With $p_{\mu}, p_{\sigma}$, the current normal distribution $\mathcal{N}(\mu, \sigma)$ can change to $\mathcal{N}(\mu\times w_{\mu} , \sigma\times w_{\sigma})$.
- $\{\mu_0, \sigma_0\}$, initial mean and standard deviation for the normal distribution  $\mathcal{N}(\mu , \sigma)$.

 We have generated six synthetic datasets with different change weights of mean $w_{\mu}$ and standard deviation $w_{\sigma}$. as described in the table below. Some variables are fixed for all datasets: $\{n:5, m:[10, 12, 14], \textit{\textbf{t}}:[360, 720, 1080], \textit{\textbf{s}}:[10, 15, 20], \mu_0: 1, \sigma_0: 0, |\mathcal{S}_{A}|: 1000, |\mathcal{S}_{B}|: 500\}$. Each dataset has two labels (normal, abnormal) and distribution changes are only applied to abnormal sets to check how distribution changes affect disproportionality values.

| **Dataset** | $p_{\mu}$ | $p_{\sigma}$ | $w_{\mu}$ | $w_{\sigma}$ | $avg(D)$ | $var(D)$ | $\|\mathcal{A}_A\|$ | $\|\mathcal{A}_A - \mathcal{A}_B\|$ |
|------------------|:------------------:|:---------------------:|:------------------:|:---------------------:|:--------:|:--------:|:-------------------:|:-----------------------------------:|
| MEAN\_1 (SYNTHETIC1)         |         0.5        |           0           |        0.01        |           0           |   1.02   |   0.03   |        8,134        |                  0                  |
| MEAN\_5 (SYNTHETIC2)         |         0.5        |           0           |        0.05        |           0           |   1.03   |   0.04   |        8,132        |                  0                  |
| MEAN\_10 (SYNTHETIC3)        |         0.5        |           0           |        0.10        |           0           |   1.02   |   0.03   |        8,075        |                  0                  |
| STDEV\_1 (SYNTHETIC4)        |          0         |          0.5          |          0         |          0.01         |   28,64  | 2908.565 |        21,888       |                6,184                |
| STDEV\_5 (SYNTHETIC5)        |          0         |          0.5          |          0         |          0.05         |   27.32  |  2621.82 |        24,188       |                6,416                |
| STDEV\_10 (SYNTHETIC6)       |          0         |          0.5          |          0         |          0.10         |   19.00  |  892.87  |        23,023       |                2,824                |

Three datasets are for testing the movement of mean, and the other three are for the movement of standard deviation. One highlight is that varying the mean of histogram snapshots does not show any meaningful differences in the mean and the variance of the disproportionality values. This suggests that the disproportionality values are robust to similar distributions, even if the central points are different. On the other hand, increasing standard deviation significantly changes the variance of disproportionality values, generating the arrangements only occurring in one set, which is what we have seen in the real-world experiment.

## Library usage

### Dependencies
Our algorithm requires numpy >= 1.19, pandas >= 1.1.3, and python >= 3.7.

### Parameters
Z-hist receives the following parameters:

- data: multivariate histogram snapshot dataset. For the shape of the dataset, please refer to our example datasets. 
- drop_cols: the columns we do not need when calculate (all columns which are not a histogram snapshot such as variable name, class label ...)
- distance: distance function (default = "chi2")
- minTrend: minumum time period that we allow the trend (continuous trend of the sign(-/+) of the slope without interruption) (default = 300). Trend value is not used in the paper.
- normal_term: the period used for PAA process (default = 300).
- n_bins: the number of bins used for SAX process (default = 3).
- minimal_trend_gradient: central value to define the trend sign (default = 0)
- matrix_cols: A list of attribute names which contains 2D matrix histograms.
- removeMatrix: Option to remove matrix histograms when applying the algorithm (default = False)
- removeTrend: Option to skip trend calculation when applying the algorithm (default = True)

### Manual
For general step-by-step manual, please refer to IDA2021_Synthetic_turbo_failure_testing.ipynb.
