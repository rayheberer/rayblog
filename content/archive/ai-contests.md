---
author: Ray Heberer
date: 2018-03-20
linktitle: A Discussion of Machine Learning Competitions
title: A Discussion of Machine Learning Competitions
tags: ["AI", "Machine Learning", "Competitions", "Essay"]
highlight: false
---

![](/img/ai-competitions/real-steel.jpg)

Define some objective, then let a population of algorithms compete against one another, mixing and refining characteristics of the most successful of their predecessors, and occasionally introducing novel approaches. I'm speaking, of course, of machine learning competitions. They are rather ubiquitous in today's research and commercial environment, but what are they good for, and what should one know about them? I'm here to ask some of these questions, and maybe produce a few answers along the way.

Although it can be misleading to mix metaphors in a discipline that already overburdens its terminology over different granularities and levels of description, I invoked the essence of genetic algorithms in order to convince people that an observable of any recurring competition is that the metric it judges contestants by tends to be optimized over time. I also wanted to get the idea of diversity and its relationship with an incentivized selection process such as competition primed in our minds.

Now, it seems fairly obvious to say that competitors will show improving results with each iteration of a contest, but it's an important idea, and more subtle than it first appears. As we will find, machine learning competitions are not unambiguously good for progress and innovation, and a lot of what determines what sort of impact they will have is how well their objectives align with and are generalizable to useful things in the real world. 

If anything, I would like to discover with this discussion a more nuanced perspective on what goes into progress. I think we will see that progress and innovation are not machines, but deeply human activities. It's this humanity that I wish to begin uncovering.

This discussion is in a sense a long-form response to an exercise question posed by Peter Norvig and Stuart Russell in their seminal text, [Artificial Intelligence: A Modern Approach](http://aima.cs.berkeley.edu/). I will borrow from their ideas, and from many other pre-eminent figures in AI and machine learning. In what follows, I wish to briefly touch upon some historical competitions and their impact, then sample a handful of present-day competitions, and finally synthesize some of my own reactions and thoughts to what I have learned. Of course, I welcome any insights, objections, or comments from each reader.

---

### Background

![](/img/ai-competitions/imagenet.jpg)

These days, with platforms such as [Kaggle](http://kaggle.com/) and [DrivenData](https://www.drivendata.org/), competitions inviting people to innovate technical solutions to science and engineering problems are no longer just an exclusive activity for researchers. Discussing Kaggle competitions and their benefits and downsides for participants, corporations, the research community and others is a matter of great depth in itself. I will not be adding to this dialogue, save to observe that these sorts of "commercial" competitions often center themselves around new use cases that can be adequately solved with current ideas, and thus tend to correspond to a useful outcome, but do not inspire research or expose holes in current understanding.

I will be focusing on competitions that are designed and organized for the furtherment of AI and machine learning research. For a starter on thinking critically about Kaggle (in the specific light of how it can benefit beginner practitioners), I recommend reading [this article](https://hackernoon.com/no-kaggle-is-unsuitable-to-study-ai-ml-a-reply-to-ben-hamner-27283878cede), then looking back to what it was written in response to if you find it interesting. In any case, I think that the growth of competition platforms has shown that competitions meant to drive innovation can be brought to a wider community, and just because not many of the competitions are research-oriented at the moment, does not mean there cannot or will not be many going forward.

Also, commercially motivated competitions have the power to consolidate and add to the theory, as was the case for the [Netflix Prize](https://www.netflixprize.com/), which ran from 2006 to 2009. This competition definitely had a part to play in bringing light to the challenges, approaches, and implementation considerations for recommendation systems. In this case, posing a relevant problem at the correct time, given the state of human interest and knowledge, helped drive critical progress that benefited very much from scale.

Of course, I would be remiss if I did not mention the [ImageNet](https://qz.com/1034972/the-data-that-changed-the-direction-of-ai-research-and-possibly-the-world/) [Large Scale Visual Recognition Challenge](http://www.image-net.org/challenges/LSVRC/). This competition has had a very large measurable impact, with over 4,800 results for "ILSVRC" on Google Scholar, and about half of those papers being published since 2017. It was in this competition, using the ImageNet dataset, that [Alex Krizhevsky](https://www.cs.toronto.edu/~kriz/), [Ilya Sutskever](http://www.cs.toronto.edu/~ilya/), and [Geoffrey Hinton](http://www.cs.toronto.edu/~hinton/) first used deep convolutional neural networks to absolutely smash the previous state-of-the-art, helping to bring about the advent of deep learning with [their landmark paper](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks).

In 2014, [Andrej Karpathy](https://medium.com/@karpathy/) helped prompt people to think more systematically about [what "human" performance means](http://karpathy.github.io/2014/09/02/what-i-learned-from-competing-against-a-convnet-on-imagenet/). If you take Andrew Ng's [Deep Learning Specialization](https://www.coursera.org/specializations/deep-learning) on Coursera, which I highly recommend, you will see that the idea of human performance, and perhaps more relevantly that of measuring the performance of machine learning systems in systematic and actionable ways, is highly emphasized. In the last years of ImageNet, the idea of overtaking human performance on a perception task went from fantasy, to possibility, to reality, and I think it was important that someone like Karpathy went through the time and effort to clearly define this and get the community thinking about the implications.

ILSVRC ran for the last time in 2017. By then, it's objective (accuracy/top-5 accuracy) had been pretty much maximized, and interest in computer vision was already at such levels that it is very likely that the competition did not inspire new ideas so much as keep track of and reward them. To me, ILSVRC represents a solid instance of a competition which brought attention and talent to a problem, and concluded before it was no longer relevant. Of course, some might think that even 2017 was a few years beyond the expiry date.

The machine learning community, perhaps more so than other research communities, is remarkably conscious about the fact that  datasets will become stale with time. This is likely in part due to the fact that in working with machine learning algorithms, overfitting and allowing held-out data to bleed into training data through hyperparameter adjustment is a daily reality. Though competitions are slightly different things from benchmark datasets, they can be thought of in the same way especially if they use a persistent dataset. I am optimistic about the machine learning community and its ability to move on from competitions that are no longer useful or relevant.

---

### Examples

![](/img/ai-competitions/face-recognition.jpg)

In what follows, I will briefly overview a small selection of machine learning competitions, their aims, and what sort of results have emerged from them. I have sampled these from the broad divisions one often learns of when being introduced to machine learning - supervised learning, unsupervised learning, and reinforcement learning. I do this not because I think these terms represent anything on a fundamental level, or because I truly think the competitions can be completely characterized by them, but rather because I think that these somewhat loose categories are good anchor points for one confronting the question of what they are interested in. 

With such a thought in mind, I encourage you to investigate the competitions that catch your attention, and more importantly the bodies of knowledge underlying each. Let curiosity be your guide!

---

#### NIST Face Recognition Vendor Test

The [FRVT](https://www.nist.gov/programs-projects/face-recognition-vendor-test-frvt-ongoing) began in February 2017, and currently only has one track  - face verification. In the latest [FRVT report](https://www.nist.gov/sites/default/files/documents/2018/02/15/frvt_report_2018_02_15.pdf), the results from 54 algorithms were reported. Currently, the leaderboard is topped by [Megvii](https://megvii.com/), also known as Face++, a Chinese facial recognition startup. Also on the leaderboard are other startups such as [VisionLabs](https://visionlabs.ai/), larger corporations like [Toshiba](https://www.toshiba.co.jp/sis/en/scd/face/face.htm), and a team from Fudan University.

Large scale facial recognition is beginning to generate high amounts of commercial interest. It should not be too surprising that many of the teams on the NIST leaderboard are from China. There are numerous articles discussing the state of Chinese facial recognition, and the sources of their advantage, such as larger datasets and fewer privacy restrictions, but [here is one](https://www.washingtonpost.com/news/world/wp/2018/01/07/feature/in-china-facial-recognition-is-sharp-end-of-a-drive-for-total-surveillance/?utm_term=.7d10e93aee14) from the Washington Post to get you started. Andrew Ng also dedicates a section of his course to face recognition (here's a [lecture on siamese networks](https://www.coursera.org/learn/convolutional-neural-networks/lecture/bjhmj/siamese-network)). So it is clear that the NIST challenge is relevant to current interests.

What is less clear is whether the challenge will have much influence in driving progress. Many of those competing for the top ranks in the competition are either funded startups, or companies with face recognition products in development. This shows that interest in face recognition was already significant when the competition began. However, one should not overlook to the potential benefit of providing these startups and labs a platform to compete with each other. Also, since many of the competitors are for-profit corporations, their researchers will be unlikely to lose sight of the real-world applications of what maximizing the competition's objective will enable.

---

#### Hutter Prize

[The Hutter Prize](http://prize.hutter1.net/), or the 50'000€ Prize for Compressing Human Knowledge, is not so much a periodic competition, as a standing challenge. I include it as the competition that bears relation to unsupervised learning, but really it is an extremely open sort of endeavor. Their objective, the compression of [100MB of Wikipedia knowledge](http://mattmahoney.net/dc/textdata.html), is straightforward to measure, and founded on the assumption that better compression and decompression algorithms will be a path to Artificial General Intelligence. In their words:

>  If you can compress the first 100MB of Wikipedia better than your predecessors, your (de)compressor likely has to be smart(er).

This follows largely from the [ideas](http://www.hutter1.net/ai/uaibook.htm) of [Marcus Hutter](http://www.hutter1.net/). Regardless of the extent to which you believe this, it is a good perspective on how being better able to model the internal structure of some data is related to being able to intelligently leverage that data.

The leaderboard for this prize is rather lonely. [Alexander Rhatushnyak](https://www.linkedin.com/in/theinventor) has won prizes the last 4 times for improving upon previous results in 2006, 2007, 2009, and 2017.

As interest in unsupervised and representation learning grows (it is one of [Yoshua Bengio's](http://www.iro.umontreal.ca/~bengioy/yoshua_en/research.html) research topics, if that motivates anyone to also be interested), I anticipate that information theory and data compression might be studied by practitioners the same way that linear algebra and calculus now are. 

In my opinion, the Hutter Prize has generated less interest than it has the potential to - maybe people disagree with the correspondence between compression and intelligence, or maybe progress in compression algorithms is not being documented and findings disemminated in a way that those in the machine learning talent pool are encouraged to give it a try - but this also means that it is probably not narrowing the focus of researchers and directing them away from exploring other avenues of discovery. It is a competition that is so far not living up to its promise, but also not acting as a danger or distraction. 

---

#### General Video Game AI Competition

[GVG-AI](http://www.gvgai.net/) has been hosted at various conferences since 2014, and is sponsored by [DeepMind](https://deepmind.com), consistent with the idea of using games as a testing ground for developing AGI as is their *modus operandi*. There are multiple tracks - in 2018: Single Player Planning, 2-Player Planning, Rule Generation, and Level Generation.

The competition has spurred a respectable amount of research papers, with [18 listed](http://www.gvgai.net/papers.php) on the site itself, and over 130 results to a Google Scholar query of "GVGAI". While I cannot be completely certain, it seems like there is a large amateur or at least non-researcher population on the leaderboard. The UK and Germany have a strong showing, with many teams being affiliated with these nations.

GVG-AI is an instance of a competition which aims to draw in research talent. It openly promotes the University of Essex's [MSc on Computer Games](http://csee.essex.ac.uk/games/) and the [Centre for Intelligent Games and Game Intelligence PhD Program](http://www.iggi.org.uk/). 

People studying aspects of AI tend to agree that games are useful, partly because they provide a way to relax some of the difficulties of real-life: partial observability, stochasticity, many-agents... However, there is a spectrum of opinions on how difficult it will be to bridge the gap, with some people emphasizing the need to test in more general environments more than others. The interview excerpts in the last few paragraphs of [this article](https://www.theguardian.com/global/2017/mar/14/googles-deepmind-makes-ai-program-that-can-learn-like-a-human) illustrate this nicely.

---

### Discussion

Through this introduction to a small handful of competitions with some relation to machine learning, I hope that I've captured a little of the essence of what sort of competitions may exist, why they exist, and what they might encourage or inhibit. By now, it is clear that the manner in which a competition is hosted, how it is marketed, and how its results are publicized all play roles in how much influence it grows to have. We've pondered the fact that steadily improving results in a competition may not reflect that it had a causal effect in producing these - the old correlation/causation nonequivalence - and learned that the timing of when a competition begins and is retired is a matter deserving the utmost consideration.

The question of whether a specific competition is truly highlighting an overlooked area ripe for discovery or funneling undue amounts of talent or attention is a difficult one, and must be evaluated on a case-by-case basis, taking into account those matters of timing, relevance of the objective measure, and human incentives motivating the competition. 

One consequence of competitions that also holds for canonical benchmark datasets is that empirical results become more emphasized. It becomes commonplace to focus research and publish around small refinements that attain superior results, sometimes without a well-developed theoretical grounding. At the same time, tolerance for theory unsupported by results diminishes. Sara Sabour, Nicholas Frosst, and Geoffrey Hinton had to make the point in their [2017 Capsules Paper](https://arxiv.org/abs/1710.09829) that the accuracy of their network on MNIST was comparable to early ConvNet results, before large amounts of "engineering" had tweaked dataset performance ever higher.

No matter where one stands on how they weigh theoretical and empirical contributions relative to each other, it still is instructive to recognize that competitions exert some sort of selection pressure, rewarding tactics that climb the leaderboards and potentially discouraging exploring. Or, if I may take the liberty of introducing more potentially confusing analogies across levels of description and different disciplines, the human agents competing may settle into equilibria where no one is incentivized to deviate heavily from their current approach and its refinements and further iterations. 

There is something resembling an explore/exploit dilemma at play, and regardless of one's feelings toward it, it is good to be conscious of it. Perhaps surprisingly, it is the competitions with more explicit constraints that may end up encouraging novel approaches or inspire activity in sub-disciplines previously receiving little attention.

Moving beyond my own philosophizing, I would like to conclude simply by observing that it is a wonderful and exciting time to work with machine learning. New competitions are constantly emerging, and while some may be more relevant and influential than others, it is undeniable that the research environment is thriving and electric, welcoming to amateurs and practitioners, and moving forward very rapidly. 

At the end of the day, I cannot help but feel thankful.