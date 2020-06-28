---
layout: post
title:  "Teste de Hipóteses: porquê e como."
date:   2020-06-27
excerpt: "Just about everything you'll need to style in the theme: headings, paragraphs, blockquotes, tables, code blocks, and more."
tag:
- markdown 
- syntax
- sample
- test
- jekyll
comments: true
---

# Motivação

In this post, I'll try to explain the concept of p-values from a different perspective. 

I believe everyone has seen hypothesis test in any statistical discipline. We all have been presented with the concept of p-value, alpha (or significance) level, power, null hypothesis and alternativa hypothesis, t-tests, ANOVA, among other fancy names for simple concepts.

We also had to perform some few hypothesis tests ourselfs, trying to grasp how to do it and how to interpret it right.

The aim of this notebook is to dumb it down with some easy-to-follow examples.

# But first, some imports

{% highlight python %}
import numpy as np
from scipy import stats
import matplotlib.pyplot as plt
import seaborn as sns
{% endhighlight %}

# Now, let's define the sample size, $\mu$ and $\sigma$ for normal distribution

First, let's define some constants. We will work with normal distributions following the equation above.

$$ f(x) = \frac{1}{\sigma\sqrt{2.\pi}} \exp \frac{-1}{2}\left(\frac{x - \mu}{\sigma^2}\right) $$

where:
- $\mu$: the mean of the distribution;
- $\sigma$: the variance;
- x: out sample vctor;

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






























## Heading 2

### Heading 3

#### Heading 4

##### Heading 5

###### Heading 6

### Body text

Lorem ipsum dolor sit amet, test link adipiscing elit. **This is strong**. Nullam dignissim convallis est. Quisque aliquam.

![Smithsonian Image](https://mmistakes.github.io/minimal-mistakes/images/3953273590_704e3899d5_m.jpg)
{: .image-right}

*This is emphasized*. Donec faucibus. Nunc iaculis suscipit dui. 53 = 125. Water is H2O. Nam sit amet sem. Aliquam libero nisi, imperdiet at, tincidunt nec, gravida vehicula, nisl. The New York Times (That’s a citation). Underline.Maecenas ornare tortor. Donec sed tellus eget sapien fringilla nonummy. Mauris a ante. Suspendisse quam sem, consequat at, commodo vitae, feugiat in, nunc. Morbi imperdiet augue quis tellus.

HTML and CSS are our tools. Mauris a ante. Suspendisse quam sem, consequat at, commodo vitae, feugiat in, nunc. Morbi imperdiet augue quis tellus. Praesent mattis, massa quis luctus fermentum, turpis mi volutpat justo, eu volutpat enim diam eget metus.

### Blockquotes

> Lorem ipsum dolor sit amet, test link adipiscing elit. Nullam dignissim convallis est. Quisque aliquam.

## List Types

### Ordered Lists

1. Item one
   1. sub item one
   2. sub item two
   3. sub item three
2. Item two

### Unordered Lists

* Item one
* Item two
* Item three

## Tables

| Header1 | Header2 | Header3 |
|:--------|:-------:|--------:|
| cell1   | cell2   | cell3   |
| cell4   | cell5   | cell6   |
|----
| cell1   | cell2   | cell3   |
| cell4   | cell5   | cell6   |
|=====
| Foot1   | Foot2   | Foot3
{: rules="groups"}

## Code Snippets

{% highlight css %}
#container {
  float: left;
  margin: 0 -240px 0 0;
  width: 100%;
}
{% endhighlight %}

## Buttons

Make any link standout more when applying the `.btn` class.

{% highlight html %}
<a href="#" class="btn btn-success">Success Button</a>
{% endhighlight %}

<div markdown="0"><a href="#" class="btn">Primary Button</a></div>
<div markdown="0"><a href="#" class="btn btn-success">Success Button</a></div>
<div markdown="0"><a href="#" class="btn btn-warning">Warning Button</a></div>
<div markdown="0"><a href="#" class="btn btn-danger">Danger Button</a></div>
<div markdown="0"><a href="#" class="btn btn-info">Info Button</a></div>

## KBD

You can also use `<kbd>` tag for keyboard buttons.

{% highlight html %}
<kbd>W</kbd><kbd>A</kbd><kbd>S</kbd><kbd>D</kbd>
{% endhighlight %}

Press <kbd>W</kbd><kbd>A</kbd><kbd>S</kbd><kbd>D</kbd> to move your car. **Midtown Maddness!!**

## Notices

**Watch out!** You can also add notices by appending `{: .notice}` to a paragraph.
{: .notice}
