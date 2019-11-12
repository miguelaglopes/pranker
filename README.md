# pranker
R package for significancy assessment of the area under the precision and recall curve (AUPRC)

(requires R package Rcpp)

A common problem in information retrieval and machine learning in general is to select positive instances (P) out of a pool of both positive and non-positive instances (N). In this problem, instances are typically scored and ranked (higher scored instances are selected first). When the gold standard is available, selection rankings are commonly assessed with the area under the precision recall curve (AUPRC), plotting precision as a function of recall. This area can be approximated as the average maximum precision for all possible values of recall. This particular version of the AUPRC is adopted here. 

Ideally, positive instances are selected first, resulting in a maximum AUPRC (area under the curve) of 1. This package computes the AUPRC and assigns a p-value to it, relative to the null hypothesis of random selection. 

The null (relative to random) AUPRC distribution is useful to estimate AUPRC significancy and to compare AUPRC values obtained in different configurations of N and P (as the null distribution depends on these parameters). 

In this implementation, the variance and covariance of the null AUPRC is computed and used to fit a beta distribution approximating the true null AUPRC distribution. When the number of instances is low (P < few dozens) the approximation exhibits some bias. However, as the number of instances increases the approximation becomes more accurate. On the other hand, when the number of instances is very high (> few hundreds of thousands), the computation of the AUPRC variance may be too computationally intensive. Between these extremes, this package is a useful alternative to Monte Carlo simulations. An approximation to speed up the AUPRC covariance computation is implemented (described in detail in the documentation for the function "pranker"). 

The main function of this package is "pranker", which takes as input a vector of scores of instances (the instances with the highest scores are selected first), and a gold standard vector (non-zero elements represent positive instances). The area under the precision recall curve and the respective p-value are then computed and returned. The function "pranker" calls the functions "auprc.ap" (which computes the AUPRC) and "null.params", which computes the mean and variance of the null AUPRC distribution, for the number of instances (N) and positives (P) of the gold standard. The functions "pranker", "auprc.ap" and "null.params" are described in more detail in the associated documentation.

The methods implemented in this package are described in "On the null distribution of the precision-recall curve", Lopes and Bontempi, ECML KDD 2014. 

##### Author: 
Miguel Lopes
##### Reference: 
On the null distribution of the precision-recall curve, Lopes and Bontempi, ECML KDD 2014

### function pranker
##### Description: 

This is the main function of the package. It takes as input a vector of instance scores of an information retrieval task (higher scores are selected first) and a gold standard vector (non-zero elements represent positive instances). N represents the number of instances and P the number of positives. 

This function returns a list of elements: the first is the obtained AUPRC; the second is a p-value relative to the null hypothesis of random selection; the third is a numeric vector of the mean and variance of the null AUPRC; the fourth is the number of total and positive instances. 

The null AUPRC distribution is obtained by first computing its mean and variance, and then by fitting a beta distribution to these two parameters, additionally to the maximum and the minimum of the distribution (note that while the maximum is 1, the minimum is not). A p-value is then obtained. 

Note that if they are available, the mean and variance of the null AUPRC may be given as input (as a numeric vector). These parameters may be computed using the function "null.params" (which takes as input the number of instances and number of positives). If only the AUPRC value is necessary (and not the p-value), it can be computed using the function "auprc.ap". 

#####  Usage:
* **pranker**(scores, y, params, approx, approxN)

#####  arguments:
* **scores** - numeric vector or matrix of scores. No NA or NULL values allowed. 
* **y** - the gold standard (numeric vector or matrix, of the same size as "prediction"). Non-zero elements represent positive instances. No NA or NULL values allowed. 
* **params** - (optional) vector of two numeric elements, the mean (the first) and the variance (the second) of the null AUPRC. If these are given there is no need to compute them. These may be obtained in the function null.params. 

##### Output:
A list of elements: the obtained AUPRC; p-value; a numeric vector of the mean and variance of the null AUPRC; the number of total and positive instances. 

### function null.params
##### Description: 

This function computes the mean and variance of the null (of random selection) AUPRC (average maximum precision), for a given number of total instances (N) and number of positive instances (P). An approximation may be implemented, described above in the documentation for the function "pranker". 

#####  Usage:
**null.params**(N, P, approx, approxN)

#####  Arguments
* **N** - Integer, number of total instances.  
* **P** - Integer, number of positive instances. 

##### Output:
Vector of two numeric elements, the mean (the first) and the variance (the second) of the null AUPRC.

### function auprc.ap
##### Description: 

This function computes the AUPRC (average maximum precision) for a vector of scores and gold standard. Scores vector and gold standard must be of the same size. Elements in the score vector are incrementally selected (from the highest to lowest vector) and precision and recall points are computed. The AUPRC is computed as the average maximum precision for all points of recall (there may be multiple values of precision associated with each recall). 

#####  Usage:
**auprc.ap**(scores, y)

#####  Arguments
* **scores** - Numeric vector or matrix of scores. No NA or NULL values allowed. 
* **y** - The gold standard (numeric vector or matrix, of the same size as "scores"). Non-zero elements represent positive instances. No NA or NULL values allowed. 

##### Output:
The AUPRC (average maximum precision) of the score vector.  
