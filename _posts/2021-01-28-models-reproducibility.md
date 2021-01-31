---
layout: post
title: "On the replicability problem in machine learning research"
date: 2021-01-30
---

This note is inspired by the Denny’s Britz’s post [“AI Research, Replicability and Incentives”](https://dennybritz.com/blog/ai-replication-incentives/) where he analyzes reasons why some promising SOTA results from academia are sadly often hard to reproduce with the same performance as claimed in the corresponding **peer-reviewed** papers. Here I want to condense the ideas of Denny and add some of my personal thoughts.

It is quite true that AI research nowadays is often an empirical field. The number of arXiv papers uploads (cs.\[CV, CL, LG, AI, NE\]\stat.ML) has increased 5 times during the last few years with almost 2k per week (!).

<img src="/figs/300121/1.png" style="float: center; margin-left: 270px; width:50%" class="row"/>

 [[Source]](https://twitter.com/dennybritz/status/1262682676709990401)


A note aside, to cope with those amount of papers there is a [arXiv sanity page](http://www.arxiv-sanity.com), that helps to sort out more recent or hyped preprints. Also, [Ml papers digest (cases guides, and research) on Habr](https://habr.com/ru/users/worksolutions/posts/) (in Russian), `article_essence` channel on the ODS community in Slack (in Russian) are especially hepful anomg others.

With this, it becomes hard to sort out papers on DL that are:

- Make not replicable outlandish claims;

- Overfit test or benchmark datasets;

- Contain statistically insignificant results.


# Reasons behind replicability problem

These two concepts are in fact different but confused quite often:

- **Reproducibility** is running the **same model implementation** using the same data and obtaining the same results.

- **Replicability** is developing a **new model implementation** based on the original publication and obtaining close enough results. 

The problem of reproducibility is conceptually solved with the modern approach to open-source software. In contrast, **replicability is still a giant issue**. This post is concerned about reasons behind replicability problem.

# 1. Differences between frameworks

Conceptually the same model implemented using different libraries or frameworks will behave differently and therefore show different results. The reason is in **different frameworks implementations**, **hidden hyperparameters**, and **bugs** that are interrelated.  If to look at GitHub issues over popular open-source ML frameworks, we discover a lot of unexpected results. High-level frameworks that hide (or/and insufficiently document) implementation details with **implicit hyperparameter choices** already made for a user are the most common source of problem.

# 2. Hidden implementation details

**Minor implementation details can have a big impact**. Due to the complexity of algorithms and pipelines, papers do not describe every little implementation detail. Scientific publications are intended for communicating high-level ideas and conceptions. However, it is not always obvious which parts are important. Scientists may not know themselves and may not even be aware that they do not know. 

# 3. Biased random seeds

To illustrate, just take a look at this funny figure:

<img src="/figs/300121/2.png" style="float: center; margin-left: 270px; width:60%" class="row"/>

[[Source]](https://twitter.com/jakevdp/status/1247742792861757441)

And it is shown that difference in random seeds can lead to significantly different outcomes:

<img src="/figs/300121/3.png" style="float: center; margin-left: 270px; width:50%" class="row"/>

[[Source]](https://arxiv.org/abs/1708.04133)

A related problem is when it takes days, weeks or even months to train and experiment with models, it is natural that the issue of repeatability and confidence intervals construction is skipped. Moreover, error bars sometimes are not a requirement for a paper acceptance.


# 4. Hyperparameters and inductive biases

Hyperparameters control the training process but are not directly part of the model. Example of those can be optimizer choice, learning schedule, model size, activation functions, etc. In the strictest sense, everything not changed by the optimization algorithm that's not part of the model (or inductive bias) is a hyperparameter. Studies show that **proper tuning of hyperparameters of simpler models can lead to better results than using more sophisticated models** [[Source]](https://arxiv.org/abs/1711.10337).

By using high-level frameworks additional hyperparameters may be hidden because they are set as the default. It is infeasible to find all hyperparameters and vary them. Most authors make educated guesses about what parameters are important, separating them from what they believe the **inductive biases** to be (e.g. Occam's razor). But these are mostly guesses based on gut feeling. 


# 5. Winning lottery tickets 

Generalization is the goal after all. When reporting the results, papers typically claim to have found an algorithm that generalizes to unseen data. But in reality, **the researchers may have found a lottery ticket that happened to succeed on a specific problem under a specific set of hyperparameters**. It may not work so well already on a slightly different distribution. 

As a result **you only hear know the winning ticket, not the hudreds of losing tickets that did not work** -- architecture and hyperparameter variations authors tried out but failed, while evaluating them on the test set. Knowing about that evolution would make you quite skeptical. Ideally, it is **good to know the generative process of a paper and its code** -- the full path the researchers took in to arrive at their conclusion. 

Here a public commit history along with the data and hyperparameters tracked for all individual experiment runs (e.g. using [DVC](https://dvc.org) and [MLflow](https://mlflow.org)) would be especially helpful. But it is often impractical for researchers that needed to worry about how to succeed in a peer-reivew process.

# 6. Unknown computational cost

**Computational cost** (i.e. how easy it is to find a good hyperparameter) also affects the model training process. In [[Source]](https://arxiv.org/abs/1910.11758v3) it is found that optimizer hyperparameters are crucial when they evaluated the effect of larger computational budgets on optimizer performance. A **larger hyperparameter tuning budget can lead to quite different results**:

<img src="/figs/300121/4.png" style="float: center; margin-left: 270px; width:60%" class="row"/>

In spite of that, **computational budgets used for experiments are rarely reported in papers**. 

# 7. Choice of validation metrics

**Metric choices and baseline comparisons are also somewhat arbitrary**. In some fields, researchers can pick whatever comparison makes their model look best - and there may be dozens of metrics to choose from.

# 8. Test set leakage

It is widely known that the data should be split into training, validation, and test sets, and that **one should not use the test set to inform training decisions**.
While the test set should not be used by the training algorithm, it is **unclear to what extent it can be used by the researchers themselves**. 

In the field of metric learning, researchers found that several code bases trained directly with test set feedback: *”during training, the test set accuracy of the model is checked at regular intervals, and the best test set accuracy is reported. In other words, there is no validation set, and model selection and hyperparameter tuning are done with direct feedback from the test set. Some papers do not check performance at regular intervals, and instead report accuracy after training for a predetermined number of iterations. In this case, it is unclear how the number of iterations is chosen, and hyperparameters are still tuned based on test set performance. This breaks one of the most basic commandments of machine learning. Training with test set feedback leads to overfitting on the test set, and therefore brings into question the steady rise in accuracy over time”* [[Source]](https://arxiv.org/abs/2003.08505v1).

 Interestingly, **in quantitative finance results are sometimes adjusted based how often the test data was touched**, but such an approach would be difficult to implement in research environment.

# 9: Data pre-processing and augmentations

**Data is another place where replicability issues can arise.** As methods such as self-supervised objectives and data augmentations are becoming more mainstream, **a lot of complexity is moved into the data pipelines themselves**. 

This can create yet another source of variability. Image augmentations may move directly into the data pipeline, but implementation details may differ slightly. In NLP, this was the case with tokenization schemes. It was sometimes unclear which tokenizer and tokenization scheme was used during pre- and post-processing.

# 10: Bugs

When implementing complex Deep Learning models, bugs are more subtle and even invisible. Our model may learn just fine and the bug may only slightly affect the convergence rate and final results.  **Sometimes it even acts as a regularizer!** 

Nothing to say that testing and CI/CD practices in research are more like an aberration rather than a norm.  

# Open source misunderstood

**Open source code provides reproducibility, but not replicability**. Code includes hyperparameters and inductive biases from the authors. Only by forcing researchers to replicate results from the ground up can we find such issues. Open source available does not mean there are no bugs, or that authors did not train on test data. As discussed above, the code never tells the full story. The code you see published is just the final setting that the author -- who is the meta learner -- has found to work well.

Denny recalls a occasions where bugs or test set leakage were found in published code. *Sometimes these resulted in paper retractions, but sometimes such evidence was ignored by the authors, who had already moved on to other projects. Nobody really cared.*
Open source code however is a good negative filter. A result that comes without open source code should raise red flags immediately. 

# Research is art

Replicability and reproducibility are neither sufficient nor necessary conditions for impactful research. **Novel ideas, even without experiments or good results, can have a big impact.** Jürgen Schmidhuber -- famous for his work on LSTMs -- is known for publishing interesting ideas without extensive experiments and empirical results. Such research is closer to art than empirical science, which is obsessed with quantifiable facts and numbers. 

Both types of research are needed, but **current incentives seem to favor benchmark hill climbing over novel ideas.** Peer review is partly to blame for this -- it is difficult to evaluate novel ideas withough clear numerical scores. What is more, reviewers also have their own ego to fight with.

Researchers in industry and academia are often evaluated (for job applications and promotions) using metrics such as the number of published papers, citation count, publication venues, h-index, etc. This encourages the rapid production of papers, maximizing the chances of journals acceptances. 