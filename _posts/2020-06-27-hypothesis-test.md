---
layout: post
title:  "Hypothesis test: how to do it right."
date:   2020-06-27
excerpt: "Hypothesis test and p-values from a different perspective."
tag:
- markdown 
- syntax
- sample
- test
- jekyll
comments: true
---

# Motivation

In this post, I'll try to explain the concept of p-values from a different perspective. 

I believe everyone has seen hypothesis test in any statistical discipline. We all have been presented with the concept of p-value, alpha (or significance) level, power, null hypothesis and alternative hypothesis, t-tests, ANOVA, among other fancy names for simple concepts.

We also had to perform some few hypothesis tests ourselfs, trying to grasp how to do it and how to interpret it right.

The aim of this notebook is to dumb it down with some easy-to-follow examples.

# But first, some imports

{% highlight python %}
import numpy as np
from scipy import stats
import matplotlib.pyplot as plt
import seaborn as sns
{% endhighlight %}

# Now, let's define the sample size, $$\mu$$ and $$\sigma$$ for normal distribution

First, let's define some constants. We will work with normal distributions following the equation above.

$$ f(x) = \frac{1}{\sigma\sqrt{2.\pi}} \exp \frac{-1}{2}\left(\frac{x - \mu}{\sigma^2}\right) $$

where:
- $$\mu$$: the mean of the distribution;
- $$\sigma$$: the variance;
- x: out sample vector;

With this, we can create some random variable X and sample from it.

We can write our random variable as follows:

$$ X \sim N(\mu, \sigma) $$

where N stands for Normal distribution (RLY?)

{% highlight python %}
# mu and sigma for normal distribution
mu = 100
sigma = 5

# Defining Random Variable 
X = stats.norm(mu, sigma)
{% endhighlight %}

## What if we sample a large number from X?

{% highlight python %}
# Sampling 10000 
lot_of_samples = X.rvs(10000)

plt.figure(figsize=(10,6))
sns.distplot(lot_of_samples, bins=100, kde=True)
plt.xlabel("Large sample size")
plt.ylabel("Count")
{% endhighlight %}

<img src="/assets/img/hyptest/norm_dist.JPG" width="200" height="200">

Cool! That is a fine normal distribution. Now we are ready to play around with it.

# Hypothesis test begins

Ok, now let's get to hypothesis test. Basically, we are trying to check if some hypothesis can or cannot be rejected based on some sample data. But what does that mean?

Before answering this question, let's define some basic concepts in order to understand it.

- H0: the null hypothesis. This hypothesis can be interpreted as if there is no significant effect in our data. Given a certain test, we can only reject it or fail to reject it, never to confirm something. __THIS IS VERY IMPORTANT TO REMEMBER__;
- H1: the alternative hypothesis. This hypothesis can be interpreted as if __there is__ some sort of effect observed in our data;
- $$\alpha$$: The significante level. A threshold set to verify the validity of out test (usually set to 0.05);
- $$\beta$$: The power of out test (actually, is $$1 - \beta$$);
- p-value: that's a tricky one! p-value can be interpreted as the evidence against the null hypothesis. The smaller the p-value, the stronger the evidence you have to reject the null. It can also be interpreted as a probability of your result be completely random (or presenting no effect).

Wow, a ton of new concepts! Don't worry, they will be explained shortly =)

## Sample, sample, sample

Let's first drawn some sample from our random variable X. I'll use 26 data points for this example (fell free to try different values). Remember, out mean is 100 and out variance is 5.

{% highlight python %}
# Defining sample size
sample_size = 26

# Sampling from random variable X
sample = X.rvs(sample_size)

# Plotting
plt.figure(figsize=(10,6))
sns.distplot(sample, bins=50, kde=False)
plt.xlabel("Random samples from X")
plt.ylabel("Count")
plt.title("Samples")
plt.show()
{% endhighlight %}

<img src="/assets/img/hyptest/samples.JPG" width="200" height="200">

OK, so now we have 26 data points drawn from X. Suppose that we didn't know beforehand how this data was generated and we had to "guess" what is the mean of this generating process. Hypothesis test to the rescue!

## OK, but how can we proceed? 

First, I'd like to recommend [this article](https://towardsdatascience.com/statistical-tests-when-to-use-which-704557554740) about different tests to perform in different situations and how the tests work.

After that, this case calls for an one sample t-test!

# One sample t-test

Remember that we want to test our sample and "guess" its mean. By looking at the picture above, we cannot say for sure what is its value, but we can note that it may be close to 100.

### First try: test against $$\mu = 98$$

{% highlight python %}
# Performing t-test against population mean of 98
stats.ttest_1samp(sample, popmean = 98)
{% endhighlight %}
`statistic=0.7153223258890102, pvalue=0.48103766168299766`

The test returned 2 values, the statistic value, and the p-value. We are going to look only at p-values in this post (if you want to know more about the statistic, check out the article quoted above).

Remember when I said that the smaller the p-value, the stronger the evidence against the null hypothesis? In this case we have a p-value of 0.028 (which is smaller than the significance level $$\alpha = 0.05$$) This can also be seen as a probability of 4.8% of the result been completely random (or in other words, presenting no effect).

In this case, H0 tells me that there is no difference between our sample mean and the probe mean from the t-test (98), and H1 tells me that there is some difference (which we can't measure so far).

So, based in the t-test, we can __reject the null hypothesis__.

### Second try: test against $$\mu = 100$$

{% highlight python %}
# Performing t-test against population mean of 98
stats.ttest_1samp(sample, popmean = 100)
{% endhighlight %}
`statistic=-1.2267067388960058, pvalue=0.23136103011689332`

In this case, the test returned a p-value of 0.23. Again, that is 23% probably that our result are random. So we __fail to reject the null hypothesis__.

## But wait... Let's take a step back.

So you are telling me that we __cannot__ confirm the alternative hypothesis, right? Yes, the test can only tell my to reject or fail to reject the null hypothesis, and because of that I can't say that the alternative hypothesis is true. 
Looking from the perspective of the p-value, there might be randomness in this result. What can I do about that? Is there a way to check if there is something wrong here? The answer is __yes__, we can check for __type-one and type-two error rates!__

# A way to quantify our wrongness: Type-one and type-two error rates

We kind of already know something about it. Remember the $$\alpha$$ and $$\beta$$ that I talked about earlier? They are the type-one and type-two error rates, respectivelly (or false positives and false negatives, if you will).

Basically, a false positive happens when my test tells me that the is a true effect, when there isn't; and a false negative happens when my test tells me that there isn't a true effect, and in fact there is.

In terms of the concepts we've seen so far, a false positive happens when the my test tells me to reject the null hypothesis (p-value < $$\alpha$$) when I shouldn't, and a false negative happens when my test tells me to accept the null hypothesis (p-value > $$\alpha$$) when I souldn't. The image below can help you get it.

<img src="/assets/img/hyptest/error_rates.JPG" width="200" height="200">
<figcaption> Error rates </figcaption>

We can also look at it in terms of sampling distributions, such as the image above.

<img src="/assets/img/hyptest/test.png" width="200" height="200">
<figcaption> Population mean comparison </figcaption>

Now it makes more sense. We can formulate our hypothesis, run some cool statistical test, measure its results (p-values, in this article), and measure error rates. 

In the examples above we've seen a hypothesis test performed by an one sample t-test of 26 samples against one mean $$\mu$$. But what if we had __26 different samples__? Will the results be the same?

## Running multiple t-tests for different samples, p-values for everyone!

First, let's run 10000 one sample t-test by sampling our random variable __X__ and storing the p-value. Something cool is about to happen and I'm going to show you.

### Ho - Samples with mean 100


{% highlight python %}
# Defining mu_test
mu_test = 100

# Number of simulations
N_SIM = 10000

# Testing different samples with mu=100 and testing against mu_test = 100
pvalues = []
for _ in range(N_SIM):
    N1 = stats.norm(mu, sigma).rvs(sample_size)
    pvalues.append(stats.ttest_1samp(N1, popmean = mu_test)[1])

# Plotting results
plt.figure(figsize=(10,6))
sns.distplot(pvalues, bins=20, kde=False)
plt.xlabel("p-values along several repetitions of t-test with different samples")
plt.ylabel("p-values count")
plt.title("Distribution of p-values for H0")
plt.show()
{% endhighlight %}

<img src="/assets/img/hyptest/h0.JPG" width="200" height="200">

Wow! We've got a kind of uniform distribution on p-values! The histogram has 20 bins, which means that there is about 500 p-values per bin. But what does that mean? 

For starters, our random variable __X__ has $$\mu = 100$$, and our samples were tested against $$\mu_{test} = 100$$. This means that there is no effect to be observed in this scenario. Hence, we would've expected p-values > $$\alpha$$ (the significance level). But remember that we have some error rates (in this case, only false positives, since there is no true effect).

And the cool thing is, if you get all p-values in the first bin (from 0 to 0.05), and divide it by the total number of simulations, we get:

{% highlight python %}
len(np.where(np.array(pvalues) < 0.05)[0])/N_SIM 
{% endhighlight %}
`0.051`

which is the type-one error rate for this experiment! Told you that this would be cool!

### H1 - Samples with mean 103

{% highlight python %}
# Defining mu_test
mu_test = 103

# Number of simulations
N_SIM = 10000

# Testing different samples with mu=100 and testing against mu_test = 103
pvalues = []
for _ in range(N_SIM):
    N2 = stats.norm(mu, sigma).rvs(sample_size)
    pvalues.append(stats.ttest_1samp(N2, popmean = mu_test)[1])

# Plotting
plt.figure(figsize=(10,6))
sns.distplot(pvalues, bins=20, kde=False)
plt.xlabel("p-values along several repetitions of t-test with different samples")
plt.ylabel("p-values count")
plt.title("Distribution of p-values for H1")
plt.show()
{% endhighlight %}

<img src="/assets/img/hyptest/h1.JPG" width="200" height="200">

Another cool thing happened! In this plot, we can see that a lot of p-values were binned in 0 to 0.05! That's expected, since $\mu = 100$, and our samples were tested against $\mu_{test} = 103$ (meaning that there is some sort of effect). In this case, we can calculate the false negatives, which is all the p-values that fell above $\alpha = 0.05$

{% highlight python %}
1 - len(np.where(np.array(pvalues) < 0.05)[0])/N_SIM 
{% endhighlight %}
`0.1669`

which is the value of $$\beta$$! The term $$1-\beta$$ is the __power__ of our test, or the number of times that the test was actually right.

And that is pretty much it! We have finished our tour on hypothesis test!
In the next posts I'll explore the concept of the Likelihood functions and Bayes Factors, and how to do hypothesis tests with it! See you there.

# Awesome science applications

Hypothesis test is the building block of the scientific method, and with it we can do a lot of cool stuff in science. For example, in particle physics the significance level is known as five-sigma level. A five-sigma level translates to one chance in 3.5 million that a random fluctuation would yield the result. 

This level of certainty was required in order to assert that a particle consistent with the [Higgs boson had been discovered](http://cms.web.cern.ch/news/observation-new-particle-mass-125-gev) in two independent experiments at CERN and this was also the significance level leading to the declaration of the [first observation of gravitational waves](https://journals.aps.org/prl/pdf/10.1103/PhysRevLett.116.061102).

Later on I'll make individual posts for each one of those discoveries. Stay tunned!
