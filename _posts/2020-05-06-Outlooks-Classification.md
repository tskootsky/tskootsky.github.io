---
layout: post
title: Predicting Optimistic Outlooks in Americans
---

![Quote]({{tskootsky.github.io}}/images/nietzschequote.jpg)


<div class="message">
  Welcome! In this post I will describe my rationale and process building a classification model that predicts individuals' future outlooks.
</div>

## The Data

**Some background:** 
Prior to jumping into the world of data science, I took a foray into academia, where I worked with a number of professors at both the UC Berkeley Haas School of Business and the Stanford Graduate School of Business on projects related to management and organizational behavior. 
In one of my favorite projects, we used a wide variety of public sources to compile a data set of proxy measures for cultural constructs. (If you don't know what that means- don't worry! The details aren't so important here). 
The important part is that I was introduced to some fabulous research organizations, including the [Pew Research Center](https://www.pewresearch.org/), whose 2016 survey [The State of American Jobs](https://www.pewsocialtrends.org/dataset/state-of-american-jobs-survey/) formed the basis for this project.

#### What's in the survey?

I could talk for hours about survey design! While I'm not a fully-fledged expert (yet), I've worked with so many people who are and find it a fascinating art. I won't go into the details of that, but I would like to walk you through some more interesting bits about the data set.

**What are a few of the main interest points relevant to this project?**
- Form 1 and Form 2:
  - Unfortunately for me, the survey splits thier respondants into two groups. While they ask many questions to ALL participants, at least half of the questions are only asked to one half of the participants. This makes sense from a data collection standpoint because you can only ask so much of a person's time and mental energy. In my project, I only used questions that were asked to all participants in the survey.
  - What this means for my model is that more of the demographic variables and fewer of the job-specific variables were kept. That's okay for the model, but less exciting to my personal interests.

- Response distributions:
  - Many of the response options were ordinal choices, such as 
    * Very useful
    * Somewhat useful
    * Not too useful
    * Not at all useful
    * Prefer not to answer (only if volunteered)
  - More often than not, responses among options were highly skewed or erratic and resulted in some very thin categories. To combat this, I grouped a lot of responses based on a judgement/combination of target feature mean by group (i.e. grouping responses that had similar rates of "positive outlooks") as well as theoretical rationale (e.g. grouping ages by generation)

**What's left after all those changes and discards?**
- A couple general questions on life outlook (e.g. "Generally, how would you say things are these days in your life?")
- A few education questions (e.g. "What is the highest level of school you have completed?")
- A few job-related questions (e.g. "Looking ahead, how important do you think it will be for you to get training and develop new skills throughout your worklife..?")
- Demographic variables (from race and gender to political ideology and household information)
- All variables were dummy coded


## The Models

I built 6 different models using `GridSearchCV` to tune hyperparameters for F1 score.

> I chose to use F1 score in consideration of who might be interested in such a model. I think that if someone was looking to run a campaign or build political policy on this information, it would be about equally important to minimize false positives as false negatives.

The ROC curves lay relatively close to each other, with AUC scores hovering around 65-70%

![ROC_ALL]({{tskootsky.github.io}}/images/roc_all_SQL.png)

In the end I settled on the Logistic regression model because it both had the most promising metrics and also has the benefit of higher interpretability.

![confusion]({{tskootsky.github.io}}/images/lr_confusion_SQL.png)


## Conclusions

Through feature importance, I'm able to garner a pretty consolidated image of people who are more likely to report a positive future outlook (i.e. believe thier children will have a higher standard of living than their own) vs a negative future outlook.

### Features with strong positive impacts:
- Generally happy in life
> They do always say it's all in your attitude...
- More adults in household
- Actually a parent
> It's cool that perhaps having children requires you to find optimism and hope for thier futures!
- Not married
- Lower level of education and/or no primary job

### Features with strong negative impacts:
- Believe own life is not better than parents' was
> These people may already experience standard of living to be declining and expect this trend to continue
- Earn >150K per year
- Naturalized US citizen
- Republican or "other" (i.e. not moderate/liberal) political viewpoints
- White

### What can we take away?

First and foremost I think it's important to remember that these features are not explanatory in and of themselves. They interact together within the model to create a prediction that is only about 60-70% accurate. So, is an unmarried person with children who lives with 5 other adults under the same roof always going to be more optimistic than a married white republican in a well paying job? No, I don't think that's a fair conclusion. 

If anything, I think the most interesting conclusion is that in general, it seems that traits associated with higher personal standard of living (earning, relative privilege in american society, etc) may actually lead people to feel insecure about thier position in society - perhaps that things have already gotten pretty good and they likely won't get better - i.e. characteristics that make someone feel like they (or thier hypothetical children) *have something to lose*.

On the other hand, people who generally have a positive outlook on life and potentially have more personal or intergenerational aspirations that have yet to be realized are more likely to hold the hope - and expressed belief - that thier childrens' lives will be better than thier own.

And me? I prefer to stay in the present. The philosopher in me wants to know, what does "better" mean about the future anyway?