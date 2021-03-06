---
layout: post
title: "Eat In Las Vegas: A Reviews-Based Recommender"
---

I live in a big house with a bunch of my friends whose occupations primarily span the social and creative sectors. When they ask me about my data science work, I enjoy the opportunity to either explain a technical concept in the simplest english I can muster, or explain my experience in a universal metaphor.

When I was working on this project - my first ever exposure to NLP - two of these conversations stuck with me.

Conversation 1 went something like this...  
Housemate: Hey, Tamara, how is your project going?  
Me: You know, I feel like I am in a dark tunnel. I know I'm headed toward the exit, but I *really* don't know where I'm going. I feel like I'm feeling my way out with my hands on the rock, just trying to trust that eventually I'll see the light.

Conversation 2 went something like this...  
Housemate: Hey, Tamara, how is your project going?  
Me: You know, I figured something out today! Up until now in this bootcamp, I've been telling the computer what to do and the computer does it. But I realize that now, we are a *team*. I've been trying to figure out the answer on my own but I finally understand that the computer and I are working together. I'm like 'hey computer, what do you think of doing something like this?' and the computer goes, 'hmmm, based on what you told me, I think the answer is something like *this*!' and then I say, 'hmmm... that doesn't seem quite right to me, can we try it this way?' and so on until we find something that works for both of us.

So, what exactly did the computer and I actually do together, as we searched for meaning together?

### Part 1: Choosing a topic
Truthfully, the computer did not help me at all with this part. Lacking clarity of inspiration, I took an instructor's tip and just used a downloadable data set from [Yelp](yelp.com/datasets). Truth be told, I never thought I'd use a data set so clean and so "generic", but sometimes you have deadlines and need to get a move on and trusting that direction will reveal itelf over time is better than not getting started at all.

But what was I supposed to do with all this data? >1M Users, >3M Reviews, and >200K Businesses. I had to find some way to narrow it down.

### Part 2: Narrowing the scope
Using PyMongo, I extracted restaurants in Nevada:  
`rest = re.compile('.*Restaurant.*')`  
`businesses = db.businesses.find({'categories': rest,'state':{'$in': ['NV']}})`  
as well as the non-neutral reviews of those restaurants:  
`reviews = db.reviews.find({'useful':{'$gte': 1}, 'business_id': {'$in': bus_lst, 'stars': {'$ne': 3.0}})`

In Pandas, I further reduced the training set to focus on restaurants within a central range of the data (i.e. excluding restaurants with only a couple total reviews, as well as restaurants with thousands of reviews). I did this to ensure that my data wouldn't be terribly skewed by those outlier's (outlier in a colloquial sense here, because I didn't only exclude outliers in the statistical sense)

### Part 3: Topic Modeling & Recommendation System
The next step was the part most team-like between me and the computer. Together we iterated through cleaning steps, tokenizing and lemmatizing my data, adding stop words, trying out different dimension reduction models and parameters. 

Spoiler: We settled on an NMF model using TF-IDF for tokenization with a `binary=True` logic and some max and min document frequency parameters.  
>PS!! If you're newer to all this like I am and you keep getting different topics out of your model, make sure you've set the `random_state` for the model. I knew about random states but didn't know the model had one. Happy modeling!

Next, I popped the model's results into a simple cosine similarity recommendation function that would find the 4 most similar restaurants based on the topics I had modeled in the previous stage and then return the highest rated (per Yelp) as the recommendation
> Note: I explored a little here with using a more sophisticated recommendation. I considered adding or excluding features to the cosine similarity calculation, but decided all of the reduced topics were meaningful and I couldn't think of any additional ones to add that felt particularly important. I also wondered about using a composite score that considered additional metrics such as the percent positive vs negative reviews, or total number of reviews, when recommending a restaurant; however, I discovered that these largely all correlated with the star rating, so I erred toward simplicity and let the stars speak for themselves.


### Results & Conclusions:

Ultimately, with only 10 topics, not a lot of my total variance was explained by my model. What was explained was largely distinct and fell into two broad categories: 
1. food items (e.g. Mexican, Thai Lunch, NY Pizza etc)
2. meta categories (e.g. Good Service, Waste Time, Hidden Gem)

This UMAP projection provides a cool visual:  
![umap]({{tskootsky.github.io}}/images/umap.png)

My recommender basically works, but if you feed it a mediocre restaurant, the logic it uses will give you back a better, but still mediocre restaurant. Personally, I kind of like this about it, because mediocre restaurants might just be your kinda vibe, but then it's a little silly that I'm choosing the highest-rated one nearby :thinking: .

Overall, this project was a blast to work on. From the darkness and into the light. Just three weeks about, I had no idea the possibilities of NLP or recommendation systems. Using the bare bones to make something that works and having a mental schema to imagine what else I could make feels like a HUGE win.