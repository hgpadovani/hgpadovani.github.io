---
layout: post
title:  "Likelihood Ratio and Bayes Factors"
date:   2020-07-15
excerpt: "Likelihood Ratio and Bayes Factors: estimating parameters."
tag:
- Hypothesis test 
- likelihood
- statistics
- bayesian statistics
feature: "/assets/img/hyptest/norm_dist.JPG"
comments: true
---

# Likelihood Ratios and Bayes Factors

In the [previous post](https://hgpadovani.github.io/hypothesis-test/) we talked about hypothesis test, how to do it right and how to interpret its results. Now, we are going to see two new concepts on measuring effects, the first one, called __Likelihood Ratios__, where we define a likelihood function, and on top of that we try to estimate which is the best parameter that describes the distribution given our sample data; and the second one called __Bayes Factors__, where we leverage the Bayes Theorem on probability for the same purpose.

In this post, I'll show how to apply those concepts in order to best describe a gerative process (which is a random variable), and how to make a good guess on its parameters.

## Why is this relevante?

There is a lot of phenomenons in nature that can be modeled as a gerative process, and for that, it is important to estimate its parameters to fully understand them. The techniques described in this post and the previous one are broadly used in science to test several hypothesis.


# Likelihood Ratios


## First things first: some definitions

The likelihood function gives you a function of a parameter given some data. For instance, image that you flip a coin 10 times, and got 6 heads in 4 tails. You could say that the coin is biased towards heads (since you got 6 heads out of 10 flips, this is called the __frequentist approach__), or you could say (and this is called the __bayesian approach__) that, until now, the data leds us to __believe__ that the coin is biased so far, and therefore we need more data to update our belief.

That's the main difference between frequentists and bayesians, one tries to infer things based on how frequent things are happening, and the other update their believes once new data come to hand.

But returning to our coin flip example. A coin flip can be described by 2 outcomes (heads or tails) with a success/failure rate depending on which one you put your money on.

So, in terms of parameters, we have:

- __n_total__ (total number of flips);
- __n_success__ (number of success);
- __p__ (probability of getting a single success).

Now, there is a type of distribution that can be formulated in terms of those parameters: [__the binomial distribution!__](https://en.wikipedia.org/wiki/Binomial_distribution). So, a discrete random variable X can be defined as:

$$ X \sim B(n, p) \tag{1}$$

where:
- n (total number of trials, [n=1, 2, 3, ...]);
- p (parameter of the distribution, [0,1]).

and its probability mass function (which is a function that gives the probability that a discrete random variable is exactly equal to some value).


$$f(k) = \binom{n}{k} p^k (1-p)^{n-k} \tag{2}$$

where

$${\binom {n}{k}}={\frac {n!}{k!(n-k)!}} \tag{3}$$

is the binomial coefficient, and __k__ is the total number of success.


Now that we've seen some basics, let's get our hands dirty.

### Some imports

{% highlight python %}
import numpy as np
from scipy import stats
import seaborn as sns
import matplotlib.pyplot as plt
plt.style.use('bmh')

%matplotlib inline
{% endhighlight %}

## Now, let's define our coin flip setting:

{% highlight python %}
# Defining the shape of the likelihood function

# Total trials
n_total = 10 

# Number of successes
n_sucess = 6

# probability of a single sucess
p = np.linspace(0,1,100) 
{% endhighlight %}

## And now, our discrete random variable with its pmf.

{% highlight python %}
# Creating a binomial random variable
X = stats.binom(n = n_total, p = p)

# Sampling 
likelihood = X.pmf(k = n_sucess)
{% endhighlight %}

<img src="/assets/img/likbayes/likelihood.PNG" width="200" height="200">

Cool! We can see that it resembles a normal distribution, but it has a skewness towards the right! We can see on the y-axis the values that the probability mass function takes for each one of the 100 values of p that we served as input to the function. With that, we can see the shape of the distribution, and with it make some inferences.

For this particular case, the maximum value of the likelihood function seems to be close to 0.6 (this process of finding the maximum value of the likelihood function is called Maximum Likelihood Estimation, [MLE](https://en.wikipedia.org/wiki/Maximum_likelihood_estimation)).

Suppose now that we want to use this likelihood function to estimate the p parameter of the generative process that generated this function, or in other words, given the set of data that we've collected (the coin flips and success rates), we want to test the hypothesis that this isn't a fair coin. Let's use __Likelihood Ratios__.

## Playing with ratios

First, let's formulate our problem in terms of hypothesis:

- H0 -> a fair coin ($$p = 0.5$$)
- H1 -> not a fair coin ($$p \neq 0.5$$). Here I'm going to define $$p = 0.3$$

{% highlight python %}
# H0
p_h0 = 0.5

# Creating a binomial random variable
X_h0 = stats.binom(n = n_total, p = p_h0)

# Likelihood for H0 
likelihood_h0 = X_h0.pmf(k = n_sucess)

# H1
p_h1 = 0.3

# Creating a binomial random variable
X_h1 = stats.binom(n = n_total, p = p_h1)

# Likelihood for H0 
likelihood_h1 = X_h1.pmf(k = n_sucess)

# Caluculating Ratio
ratio = likelihood_h0/likelihood_h1
{% endhighlight %}

<img src="/assets/img/likbayes/likelihood_ratio.PNG" width="200" height="200">

So our data is 5.57 more likely to be fair with $$p = 0.5$$ then not fair with $$p = 0.3$$. Prety cool, right?

This process can be used when we have some hypothesis about some sample data and this data is generated by a known generative process but you don't know its parameter. Let's see the second technique: __Bayes Factors__.

# Bayes Factors

First of all, let's remember __Bayes Rule__.

$$P(A/B) = \frac{P(B/A) . P(A)}{P(B)} \tag{4}$$

where:
- P(B/A): likelihood
- P(A): prior probability
- P(B): evidence
- P(A/B): posterior probability

This equation simply tells us that ir order to obtain a posterior probability, we must have some __belief__ (prior probability) about our distribution parameter and __update it when new evidence__ (you can read it as data) comes at hand.
Note that in Bayesian statistics there is __uncertanty over parameters__, and hence they are quantified as probability distributions.

Cool! Now let's put it in terms of our problem: we want to estimate the best parameter to describe our distribution. For the null hypothesis, we have:

$$P(H0/D) = \frac{P(D/H0) . P(H0)}{P(D)} \tag{5}$$ 

and for the alternative hypothesis:

$$P(H1/D) = \frac{P(D/H1) . P(H1)}{P(D)} \tag{6}$$

Dividing the (6) by (5), we have:

$$\frac{P(H1/D)}{P(H0/D)} = \frac{P(D/H1) . P(H1)}{P(D/H0) . P(H0)} \tag{7}$$ 

And here we can define:

- $$\frac{P(H1/D)}{P(H0/D)}$$: posterior probability;
- $$\frac{P(D/H1)}{P(D/H0)}$$: Bayes Factor;
- $$\frac{P(H1)}{P(H0)}$$: prior probability;

That's it! That's how Bayes Factors are defined. But how can we use it? Let's get back to our previous example, the coin flip.

## Coin flips all over again

Remember:

- H0 -> a fair coin ($$p = 0.5$$);
- H1 -> not a fair coin ($$p \neq 0.5$$). Here I'm going to define $$p = 0.8$$;
- n_total = 10 -> total number of coin flips;
- n_sucess = 6 -> total number of sucesses (given heads or tails, doesn't matter).


## Defining our problem in terms of Bayesian statistics

Now that we have our problem, let's understand each term of equation 7. Let's start with priors.

### Prior distributions

When we talk about prior distributions in Bayesian statistics, we want to put our belief into the problem. We know that a binomial distribution (equation 2) can sucessfully describes our data, and the distribution lies between 0 and 1. Cool, this brings up the question: how can we define a good prior distribution? The answer for that is not trivial, but it can be simplified based on [conjugate priors](https://en.wikipedia.org/wiki/Conjugate_prior) (we can also choose a non-informative prior or a probability distribution that has the same domain of our likelihood, which can yield good results, but if there is a conjugate for our problem, let's use it!).

For this particular case, the conjugate prior for binomial distribution is the [__beta distribution__](https://en.wikipedia.org/wiki/Beta_distribution)! It can be describe as follows:

$$ Beta(p, \alpha, \beta) =  \frac{1}{B(\alpha, \beta)} . p^{\alpha-1} (1 - p)^{\beta-1} \tag{8}$$

where:
- $$\alpha$$ and $$\beta$$ -> parameters for the function (can be read as the number of 1 + sucesses and 1 + failures, respectively;
- $$B(\alpha, \beta)$$ -> the [beta function](https://en.wikipedia.org/wiki/Beta_function);

Let's take a look at the beta distribution!

### Some beta distributions

{% highlight python %}
# examples 
parameters = [
    (0,0),
    (3,0),
    (2,6),
    (12,7),
    (20,32),
    (63,4)
]
# Opening figure
fig, axes = plt.subplots(figsize=(14, 6), dpi=150, nrows=2, ncols=3)

# loop for each 
for i, params in enumerate(parameters):
    beta_dist = stats.beta(1 + params[0], 1 + params[1])
    curve = beta_dist.pdf(p)
    
    axes[int(i/3)][(i % 3)].plot(p, curve)
    axes[int(i/3)][(i % 3)].set_title('Sucesses: {} | Failures: {}'.format(params[0],params[1]), fontsize=14)

plt.tight_layout()
plt.show()
{% endhighlight %}

<img src="/assets/img/likbayes/betas.PNG" width="200" height="200">

It is clear that the greater the parameter, the narrower the distribution. Another way to look at it is the greater the parameter, the lesser uncertanty regarding the distribution.

Cool! Now that we have established the concept of prior distribution, let's apply Bayes Rule to get a feeling of what is going to happen to our posterior distribution given our data and prior.

### Calculating the posterior distribution

For this particular example I'm going to calculate the likelihood as a beta distribution as well. But don't worry, the results are equal to a likelihood function as a binomial distribution. The reason why I'm going to do this like that is because we can define $$\alpha$$ and $$\beta$$ of our posterior in terms of our prior distributions and likelihood like this:

- $$\alpha_{likelihood}$$ = n_sucess + 1

- $$\beta_{likelihood}$$ = n_total - n_sucess + 1

- $$\alpha_{posterior} = \alpha_{likelihood} + \alpha_{prior}$$ - 1

- $$\beta_{posterior} = \beta_{likelihood} + \beta_{prior}$$ - 1

Let's get to it!

{% highlight python %}
# Defining our prior beta parameters
a_prior = 1
b_prior = 1

# Defining our likelihood in terms of our data so far
a_likelihood = n_sucess + 1
b_likelihood = n_total - n_sucess + 1

# Defining our posterior beta parameters in terms of prior and likelihood parameters
a_posterior = a_prior + a_likelihood - 1
b_posterior = b_prior + b_likelihood - 1

# Getting curves for each distributions
prior_curve = stats.beta(a_prior, b_prior).pdf(p)
likelihood_curve = stats.beta(a_likelihood, b_likelihood).pdf(p)
posterior_curve = stats.beta(a_posterior, b_posterior).pdf(p)
{% endhighlight %}

<img src="/assets/img/likbayes/dists.PNG" width="200" height="200">

With the prior being Beta(1,1), there is no information inputted, so the posterior is basically equal to the likelihood. Let's see what happens if the prior is a little biased to 1.

{% highlight python %}
# Defining our prior beta parameters
a_prior = 5
b_prior = 1

# Defining our likelihood in terms of our data so far
a_likelihood = n_sucess + 1
b_likelihood = n_total - n_sucess + 1

# Defining our posterior beta parameters in terms of prior and likelihood parameters
a_posterior = a_prior + a_likelihood - 1
b_posterior = b_prior + b_likelihood - 1

# Getting curves for each distributions
prior_curve = stats.beta(a_prior, b_prior).pdf(p)
likelihood_curve = stats.beta(a_likelihood, b_likelihood).pdf(p)
posterior_curve = stats.beta(a_posterior, b_posterior).pdf(p)
{% endhighlight %}

Look how our prior has the power to drag the posterior to the right! We can "bias" the posterior distribution towards the belief of what the parameter might be. So if we have a good feeling of what value the parameter can be, we can input this information into our prior and therefore influence our posterior. Pretty cool, right?

Now let's take a look at the Bayes Factor of this problem.

Remember from equation 7 that the Bayes Factor is a proportionality constant between prior and posterior distribution. So that's exactly what we are going to calculate below.

## Bayes Factor at last!

Remember the hypothesis:

{% highlight python %}
# Hypothesis
H0 = 0.5
H1 = 0.8

# Bayes Factor for H0
prior_H0 = stats.beta(a_prior, b_prior).pdf(H0)
posterior_H0 = stats.beta(a_posterior, b_posterior).pdf(H0)
bays_factor_H0 = round(posterior_H0/prior_H0,2)

# Bayes Factor for H1
prior_H1 = stats.beta(a_prior, b_prior).pdf(H1)
posterior_H1 = stats.beta(a_posterior, b_posterior).pdf(H1)
bays_factor_H1 = round(posterior_H1/prior_H1,2)
{% endhighlight %}

<img src="/assets/img/likbayes/bayesfactor.PNG" width="200" height="200">

And there you have it! We've calculated Bayes Factor for both hypothesis and with that we can draw conclusions based on it. BFH0 is greater than BFH1, which implies that $$p=0.5$$ is a better parameter than $$p=0.8$$ for this particular case!

# Conclusion

In this post we've seem 2 different ways of hypothesis test, the first one based on the likelihood function over a grid of the parameter we are trying to estimate; and the second one based on bayesian statistics and the use of likelihood and prior distribution to estimate the posterior distribution over the parameter at hand. This sums up to my series of articles on hypothesis test! 

Next post I'll talk about Bayesian Inference and some cool applications! Stay tunned!

# References

This post was based in the [amazing course](https://www.coursera.org/learn/statistical-inferences/) of Daniel Lakens, from Coursera. I higly recommend you to to check it out!
