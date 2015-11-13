---
layout: post
title: "Bayesian inference and model fitting"
date: 2015-11-11
---
<blockquote>FYI (but you can totally ignore this), today is what Chinese people would call <a href="https://en.wikipedia.org/wiki/Singles%27_Day">the Singles' Day</a>. This thing has a Wikipedia page now! Surprise surprise!</blockquote>
Bayesian interpretation of probability is that it represents uncertainty. Bayesian data analysis is about modeling, and we would like to address and quantify the uncertainty that surrounds the appropriate choice for the model parameters. From a Bayesian perspective, we can use the machinery of probability theory to describe the uncertainty in model parameters, or indeed in the choice of model itself. The basic formula for Bayesian inference is Prior + data = posterior. More specifically, Bayes' theorem is used to convert a prior probability, which formalizes our pressumption on a model parameter w, into a posterior probability by incorporating the evidence provided by the observed data. The effect of the observed data D = {d1, . . . , dN} is expressed through the conditional probability p(D|w). Bayes’ theorem, which takes the form p(w|D) = p(D|w)p(w)/p(D) then allows us to evaluate the uncertainty in w after we have observed D in the form of the posterior probability p(w|D). There is no difficulty from a Bayesian perspective in employing models for which the number of parameters greatly exceeds the number of data points. Indeed, in a Bayesian model the effective number of parameters adapts automatically to the size of the data set. (Dr. Jeff Tilson, another member of our group and a senior scentist at RENCI, is working with Kirk on predictive genomics. They are building predictive models with the number of parameters at the same magnitude of order as human genome. In constrast, the number of samples they have, or the size of the training set is much fewer. To solve this problem they have been applying a Bayesian method.)<br/>
The quantity p(D|w) on the right-hand side of Bayes’ theorem is evaluated for the observed data set D and can be viewed as a function of the parameter vector w, in which case it is called the likelihood function. It expresses how probable the observed data set is for different settings of the parameter vector w. Note that the likelihood is not a probability distribution over w, and its integral with respect to w does not (necessarily) equal one. The denominator p(D) in the right side of the above formula is the normalization constant, which ensures that the posterior distribution on the left-hand side is a valid probability density and integrates to one. Indeed, integrating both sides of the formula with respect to w, we can express the denominator in Bayes’ theorem in terms of the prior distribution and the likelihood function p(D) = (integral sign) p(D|w)p(w)d(w). In both the Bayesian and frequentist paradigms, the likelihood function p(D|w) plays a central role. However, the manner in which it is used is fundamentally different in the two approaches. In a frequentist setting, w is considered to be a fixed parameter whose value is to be estimated, and the error bar on this estimate is obtained by considering the distribution of possible data sets D. By contrast, from the Bayesian viewpoint there is only a single data set D (namely the one that is actually observed), and the uncertainty in the parameters is expressed through a probability distribution over w.<br/>
A widely used frequentist estimator is maximum likelihood, in which w is set to the value that maximizes the likelihood function p(D|w). This corresponds to choosing the value of w for which the probability of the observed data set is maximized. In the machine learning literature, the negative log of the likelihood function is called an error function. Because the negative logarithm is a monotonically decreasing function, maximizing the likelihood is equivalent to minimizing the error. One approach to determining frequentist error bars is the bootstrap, in which multiple data sets are created as follows. Suppose our original data set consists of N data points X = {x1, . . . , xN}. We can create a new data set XB by drawing N points at random from X, with replacement, so that some points in X may be replicated in XB, whereas other points in X may be absent from XB. This process can be repeated L times to generate L data sets each of size N and each obtained by sampling from the original data set X. The statistical accuracy of parameter estimates can then be evaluated by looking at the variability of predictions between the different bootstrap data sets.

<h2>Gamma distribution as a conjugate prior distribution</h2>
the gamma distribution is a two-parameter family of continuous probability distributions. The common exponential distribution and chi-squared distribution are special cases of the gamma distribution. There are three different parametrizations in common use:

With a shape parameter k and a scale parameter θ.
With a shape parameter α = k and an inverse scale parameter β = 1/θ, called a rate parameter.
With a shape parameter k and a mean parameter μ = k/β.
In each of these three forms, both parameters are positive real numbers.

The parameterization with α and β is more common in Bayesian statistics, where the gamma distribution is used as a conjugate prior distribution for various types of inverse scale (aka rate) parameters, such as the λ of an exponential distribution or a Poisson distribution[3] – or for that matter, the β of the gamma distribution itself. (The closely related inverse gamma distribution is used as a conjugate prior for scale parameters, such as the variance of a normal distribution.)

A random variable X that is gamma-distributed with shape α and rate β is denoted
<img src="http://mathurl.com/render.cgi?X%20%5Csim%20%5CGamma%28%5Calpha%2C%20%5Cbeta%29%20%5Cequiv%20%5Ctextrm%7BGamma%7D%28%5Calpha%2C%5Cbeta%29%5Cnocache" />
X \sim \Gamma(\alpha, \beta) \equiv \textrm{Gamma}(\alpha,\beta)
Probability density function[edit]
The corresponding density function in the shape-rate parametrization is

g(x;\alpha,\beta) = \frac{\beta^{\alpha} x^{\alpha-1} e^{-x\beta}}{\Gamma(\alpha)} \quad \text{ for } x \geq 0 \text{ and } \alpha, \beta > 0
Both parametrizations are common because either can be more convenient depending on the situation.

Cumulative distribution function[edit]
The cumulative distribution function is the regularized gamma function:

 F(x;\alpha,\beta) = \int_0^x f(u;\alpha,\beta)\,du= \frac{\gamma(\alpha, \beta x)}{\Gamma(\alpha)}
where γ(α, βx) is the lower incomplete gamma function.

If α is a positive integer (i.e., the distribution is an Erlang distribution), the cumulative distribution function has the following series expansion:[5]

F(x;\alpha,\beta) = 1-\sum_{i=0}^{\alpha-1} \frac{(\beta x)^i}{i!} e^{-\beta x} = e^{-\beta x} \sum_{i=\alpha}^{\infty} \frac{(\beta x)^i}{i!}

Set up and compute model (Summarizes uncertainties using probability)
Inference for quantities of interest (Inference using iterative simulation (Gibbs sampler)
Model checking
 Do inferences make sense?
 Compare replicated to actual data, cross-validation
 Dispersed model validation (“beta-testing”)





