---
layout: post
cover-img: "assets/banner.jpg"
---
### <span style="color:red">Red</span> versus <span style="color:Blue">Blue</span>.
Differing in their ideals and philosophies, the republicans and the democrats have been at odds for over 200 years. But modern media has has surely changed the game. Specifically YouTube: the perfect platform for political soapbox and free expression to reach a large and diverse audience.

We took a plunge into the [YouNiverse](https://github.com/epfl-dlab/YouNiverse) dataset, comprising of metadata of **136,470** channels, **72,924,794** videos, and around **8.6 billion** comments to explore YouTube's projection of the American polital landscape. We wanted to know the answers to questions such as: How are the users distributed amongst the <span style="color:red">right wing</span>- versus <span style="color:Blue">left wing</span>-biased YouTube major news channels? Does YouTube trap users with a certain political bias in filter bubbles [1] [2] [3]? How do certain YouTube trends relate to real-world events? What main topics are discussed on the "political youtube"?

Let's begin this **explorative journey**...

[1]: https://www.ncbi.nlm.nih.gov/pmc/articles/PMC7201237/

[2]: https://www.pnas.org/doi/10.1073/pnas.2101967118#con1

[3]: https://www.asc.upenn.edu/news-events/news/cable-news-networks-have-grown-more-polarized-study-finds

## So many channels..which ones should we investigate?"

As we set out to explore the political landscape of YouTube, the first step was to identify which channels to examine. To do this, we turned to [AllSides](https://www.allsides.com/media-bias), a company that aims to provide balanced news and media analysis. They provided us with a list of American news channels, along with their respective [media bias ratings](https://www.allsides.com/media-bias/media-bias-rating-methods).

From this list, we were able to identify **142** YouTube channels in the YouNiverse dataset, out of a total of **877**. With this information in hand, we were ready to begin our journey through the complex and often polarizing world of political YouTube.

### Alright, so what have we got here..

Let's get a feel for the data. How many channels do we have for each bias? And how is the total view count split between them?

<img src="assets/bars_channel_count_by_bias.png" width="30%"/>

![](https://i.imgur.com/4tJc2QG.png)

<img src="assets/bars_share_of_view_count_by_bias.png" width="30%"/>

![](https://i.imgur.com/f2CbMBA.png)

It seems like our dataset includes much more <span style="color:Blue">left</span>-winged channels and consequently, a far higher share of the view counts are from <span style="color:Blue">left</span>-winged channels.

Let's take a look at some of the most popular channels
<img src="assets/bars_view_count_by_channel.png" width="30%"/>

![](https://i.imgur.com/GjpG5hK.jpg)

If we want to compare data about <span style="color:red">right-</span> and <span style="color:Blue">left-</span>winged channels and viewers, then this is going to be a *problem*. Luckily, there is a solution: [propensity score matching](https://en.wikipedia.org/wiki/Propensity_score_matching)! 
>For each channel with a <span style="color:red">right</span> bias, we find a channel on the <span style="color:Blue">left</span> that is as similar as possible in terms of: cumulative views, number of videos and creation date. Since we are only really interested in the extreme-bais cases, we discard channels that are considered center- or mixed-winged.

This leaves us with **62** channels to investigate: **31** <span style="color:red">right</span>-winged and **31** <span style="color:blue">left</span>-winged.


### Do people who watch videos from <span style="color:red">right</span>-winged channels also watch videos from <span style="color:blue">left</span>-winged channels (and vice-versa)?

Ideally, we would like to see exactly which users watched certain videos. Unfortunately this information isn't available. However the YouNiverse dataset does contain data about certain users commenting on certain videos. *We can thus imagine a connection between two channels when a viewer has commented under at least one video from each*. 

It sure would be nice to visualise these connections on some kind of **network diagram** in which
- the number of connections between two channels can be shown by the thickness of their connecting edge
- the size of the nodes are proporional to the cumulative view count of the channels


<img src="assets/graph_channel_full.png" width="90%"/>

**image not showing**


Nice! As we expect, <span style="color:blue">left</span>-winged channels dominate. Also, channels with higher cumulative view counts seem to have more connections..this makes sense.

Now that we have an idea of what this network looks like, let's get a bit more technical with it for a deeper understanding:

### Node degree distrubution

The **node degree** is the number of unique connections it has to other nodes. Let's see a histogram of this measure over the network:

<img src="assets/hist_node_degree.png" width="30%"/>

![](https://i.imgur.com/9XiH2J2.png)

It seems like we have many more channels that have many unique connections than channels who have less unique connections. The density measure of the graph is **0.89** and thus *nearly **90%** of all possible connections exist.*

Let's have a look at a distribution of the *number of total connections* - or in other words, edge weights. 

<img src="assets/hists_weight_distribution.png" width="50%"/>

![](https://i.imgur.com/i9UldcU.png)

It seems that although many channels are connected to many other channels, the number of connections between channels are often small. For example in the network graph above, the connection between "CNN" and "FOX" is much stronger than the connection between "The Economist" and the "Orlando Sentinal".

Now that we know this, it feels important to see the distribtuion of edge weights **for each node**.

<img src="assets/bars_edge_weights.png" width="50%"/>

![](https://i.imgur.com/5qzEGp1.png)

Unlike many real-world-networks, there are few weakly linked channels, a peak of channels centered roughly around the mean and few very well-connected channels.

## Are viewers stuck in filter bubbles?

filter bubble
*noun*
>"a situation in which an internet user encounters only information and opinions that conform to and reinforce their own beliefs, caused by algorithms that personalize an individual’s online experience."
*~Oxford Languages dictionary*

### Homophily
To investigate this question, let's use the measure of **homophily**, *which describes a node's similarity to the nodes to which it is connected*.
Specifically, we define a measure of "weighted homophily":
$$
\text{weighted homophily of node } i = \frac{\sum \text{connection weight to node of same bias}}{\sum\text{connection weight}}
$$

<img src="assets/bars_weighted_homophily_matched.png" width="30%"/>

![](https://i.imgur.com/RUtU4t2.png)

This suggests that <span style="color:red">right</span>-winged channels are more connected to other <span style="color:red">right</span>-winged channels than <span style="color:blue">left</span>-winged channels are connected to other <span style="color:blue">left</span>-winged channels! Can we say that viewers who watch <span style="color:red">right</span>-winged channels are stuck in a filter bubble? We were not quite convinced yet. Let's plot this same concept in a different way. How about for each channel, we show the number of unique connections to channels of the *same* bias versus the number of unique connections to channels of the *opposite* bias?

![](https://i.imgur.com/NenXQ4M.png)

Here we can see the same phenomenon. 

### Let's look more closely at the comments data

We have **18 million** comment authors in our dataset. The distribution of the number of comments for each of these authors is **extremely heavy tailed**.

<img src="assets/authors_comments.png" width="30%"/>

![](https://i.imgur.com/azdJlT4.png)

What about the distribution of the number of comments over the different biases?

<img src="assets/comments_by_bias.png" width="30%"/>

![](https://i.imgur.com/dybo3MB.png)

We want to see a distribution of the general bias of each comment author as this would give us a finer-grained insight about possible filter bubbles. In this study, we don't have access to the viewing history of individual users, but it is reasonable to assume that if a user comments on a video, they are *interested* in that video. If a user comments on a video with a certain bias, they are likely to share that same bias. 
We assigned a **score** to each comment author based on the bias of the videos on which they commented:
- videos with a <span style="color:blue">left</span>-winged bias were given a score of **-1**
- videos with a <span style="color:red">right</span>-winged bias were given a score of **+1**.

For example, a commenter with a score of **-400** has left **400** more comments on <span style="color:blue">left</span>-winged channels than on any other political channels. In other words, we consider this person to be <span style="color:blue">left</span>-wing biased . Similarly, a commenter with a score of 0 is considered to be unbiased.

<img src="assets/authors_bias_sum_by_author.png" width="30%"/>

![](https://i.imgur.com/l05Hl20.png) 

The result: a clear **bimodal** distribution! Our research suggests that the "filter bubble" effect is present. The distribution of commenters appears to be influenced by two distinct groups or "personas" - "democrats" and "republicans" - with one biased towards the <span style="color:blue">left</span> and the other towards the <span style="color:red">right</span>.


We also analyzed the commenter bias based on the **videos** on which the commenters commented, expressed as a ratio of <span style="color:blue">left</span>-wing to <span style="color:red">right</span>-wing videos. Here, each commenter was assigned a score based on this ratio: 
- a score of 0.0 indicates that only <span style="color:blue">left</span>-wing videos were watched
- a score of 1.0 indicates that only <span style="color:red">right</span>-wing videos were watched
- a score of 0.5 indicates that an equal number of each were watched. 

This allows us to see the direction of users' preferences, without considering the magnitude of their bias.

<img src="assets/authors_bias_ratio.png" width="30%"/>

![](https://i.imgur.com/KZ3ARDM.png) 

The result is striking, the filter bubble effect is even more pronounced when disregarding the magnitude of commenters' biases. Consistent with our previous result, the effect is greater for commenters on the right.

### So what news is hot and what news is not?

We know that the news channels report, well, *news*.. but the question is: what news do they focus on and how are these topis distributed between the <span style="color:blue">left</span>-wing and <span style="color:red">right</span>-wing biases?

We could think of a very useful tool to help us answer the first part of the question: [Latent Dirichelet Allocation (LDA)!](https://en.wikipedia.org/wiki/Latent_Dirichlet_allocation). After some natural language processing such as 
1. removing stopwords and punctuation
2. lemmatisation
3. constructing bigrams,

**the video titles** would give a good indication of the general video topic. These processed documents were then deconvoluting into a topic space of **20 topics**, with the most *meaningful* ones being handpicked out afterwards. Let's see the most common words in the selected topics:

![](https://i.imgur.com/OXJvFNJ.png)
It seems as though the news channels speak a lot about **politicians** themselves (Barack Obama and Hillary Clinton), North Korea, the stock market, Brexit and violence at schools..but *by far*, the topic with the the most heavy tailed word distribution is...
![](https://i.imgur.com/zqp7h8l.png)
Considering the time in which this data reflects (the beginning of YouTube until 2019), this does not seem surprising to us. Here you can see that the single word "Trump" captured over **20 %** of this topic space!
![](https://i.imgur.com/QfnYeC5.png)

Now we are in a position to answer the second part of our topic-based question, which is: how are these topis distributed between the <span style="color:blue">left</span>-wing and <span style="color:red">right</span>-wing biases?

First, we used an advanced Machine-Learning model which is the [Twitter-roBERTa-base model for sentiment analysis](https://huggingface.co/cardiffnlp/twitter-roberta-base-sentiment). Using this on the titles, we get a score indicating the extent to which each is talking about its topic in a positive or negative light. For some of the most prevalent words found in the topics discussed above, we calculated the **mean sentiment scores** for <span style="color:blue">left</span>-wing and <span style="color:red">right</span>-wing channels.
<img src="assets/bars_sentiment_for_key_words.png" width="30%"/>

![](https://i.imgur.com/ORrCpD4.png)

Here, the green and red bars indicate **relative difference in sentiment**. It seems like when considering all the titles, <span style="color:red">right</span>-wing channels tend to show a more negative sentiment compared to <span style="color:blue">left</span>-wing channels. Interestingly, the sentiment trend for "Trump" shows greater positivity in <span style="color:red">right</span>-wing channels and the sentiment trend for "Obama" shows greater positivity in <span style="color:blue">left</span>-wing channels! This makes sense since Barack Obama is indeed part of the Democratic party, while Donald Trump is part of the Republican party.

## The political YouTube landscape: how closely does it mimic reality?

As we entered into the world of political YouTube, we couldn't help but wonder - how tight is its **time-correlation** to the real world of politics? Furthermore..can YouTube **influence** public opinion, or can we infer public opinion by looking at YouTube data?

Considering that Donald Trump is such a hot topic, we compiled a **timeline** of video sentiment scores mentioning Donald Trump from the beginning of his time in office and compared them to the average weekly favorability of Trump based on polls (source?).

![](https://i.imgur.com/IcFngx5.png)

From the plot, a definite correlation between the two timeseries can be seen! They share a Pearson correlation coefficient of 0.225 with a P-value of just 0.0023. It is clear that the data from the YouTube video title sentiment lags behind the data from the polls..intuitively this suggests that the YouTube video title sentiment is more dependent on the state of the polls rather than the other way around.

Running the same analysis seperately for <span style="color:red">right</span>-wing and <span style="color:blue">left</span>-wing channels reveals that <span style="color:red">right</span>-wing media had a higher correlation with a significantly smaller p-value as compared to <span style="color:blue">left</span>-wing media:
- Pearson correlation for <span style="color:red">right</span>-wing media: 0.35, P-value = 0.000025
- Pearson correlation for <span style="color:blue">left</span>-wing media: 0.17 P-value: 0.048

Our analysis shows there is a relationship between video sentiment and favorability, but the direction and strength of this relationship can vary.

## So what's the verdict?

That was a lot of information from a lot of data. Let's try pick out the most **notable findings** from the data which we have explored:

- Many channels have **many unique** channel connections in that they sharing common commenters on their videos with many other channels.
- Many connections between channels are **not strong**, in that they do not share a high number of commenters.
- Viewers who watch <span style="color:red">right</span>-wing channels are **more likely** to watch videos from the same bias than viewers who watch videos from <span style="color:blue">left</span>-wing channels channels, implying a *stronger filter bubble for <span style="color:red">right</span>-wing viewers*.
- Hot topics among the news channels include: Donald Trump, Barack Obama, Hillary Clinton, North Korea, the stock market, Brexit and school shootings.
- The sentiment trend for "Trump" shows greater positivity in <span style="color:red">right</span>-wing channels and the sentiment trend for "Obama" shows greater positivity in <span style="color:blue">left</span>-wing channels.
- There is a correlation between the timelines of title sentiment scores mentioning Donald Trump from the beginning of his time in office compared to the average weekly favorability of Trump based on polls, although the title sentiment score signal lags behind.

There is still so much more to explore, and we have **so** many more questions..but let us heed the words of Donald Trump:

>"In the end, you're measured not by how much you undertake but by what you finally accomplish."

And thus concludes this **data story**!
