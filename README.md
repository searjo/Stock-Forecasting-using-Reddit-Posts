# Stock-Forecasting-using-Reddit-Posts
Analyzed the text of all Reddit/WallStreetBets posts over an 8 month period and generated above-baseline stock trading returns based solely on the sentiment of the Reddit community

Data was sourced from Kaggle.com. It was compiled by Kaggle Grandmaster Gabriel Preda using
The Python Reddit API Wrapper (praw). The data set has 53,187 observations and the time period
included is from January 28th to August 16th 2021. It inludes the following features:
• Title - The Title of the Post on Reddit
• Score - The Score of the Post (Number of upvotes it has received)
• id - ID of the user who posted
• url - A link to either the text post within Reddit or to an external link posted by the user
• CommsNum - Number of comments on the post by Reddit users
• Body - The text of a post if it was posted within Reddit. This only has 24,000 non-null
observations out of the 53,000, as many posts are links or images that do not contain text.
• Timestamp - Date and second the post was generated
The combined title and body of the text (examples in figures 16 and 14 will be the corpus and
explanatory variable of our model. The text data is unique given the Redditors are using bespoke
lingo and often do not adhere to English-language norms. For example, there are some well-thought
out and descriptive text posts, while there are others that just say ”HOLYMOLY” on repeat hundreds
of times.
2


To inspect the text data, I first used Sci-Kit Learn CountVectorizer (results in figure 3 and TF-
IDFVectorizer (results in figure 4, the latter being the term-frequency inverse document frequency
vectorizer, which parses through each post and gives me insight into the number of times a term
appears in each document relative to the number of documents it appears in (words that appear in
more documents are weighted lower). Both analyses indicated that emojis and daily threads were
significant elements - the rocket ship emoji was by far the most important element in the corpus –
this aligns with anecdotal evidence after inspecting many of the posts. The text version of the
trigram had a TF-IDF score of .94, roughly 7x the next highest scoring trigram.


Sentiment analysis was conducted using two libraries.
1. NRCLex: The first is a library from the National Resource Council Canada Canada (2019). It
is a collection of seven lexicons including a word-emotion association lexicon and is based on
25,000 word senses. The emotions include anger, fear, anticipation, trust, surprise, sadness, joy,
and disgust) and two sentiments (negative and positive). One pitfall of this is the rocket emoji,
which clearly indicates positivity in this context, may not be factored in to its sentiment.
2. VADER: The second library we used is VADER, which stands for Valence Aware Dictionary and
sEntiment Reasoner from Hutto and Gilbert (2014). VADER is specifically tailored to social
media analysis, particularly tweets, and can decipher sentiment from emojis. Although Reddit
posts are generally longer than tweets, I thought it would be useful to compare with NRC - we
can see it in figure 6.

I plotted the sentiment scores to use as features when trying to achieve high returns. As seen in
figure 8, the sub-reddit has been classified by the NRC as overwhelmingly positive but this score is
volatile. Surprisingly for an anonymous social media site, trust is one of the most common sentiments -
this could be because the community acts tight-knit and talks to one another as if they are united. This
is followed by anticipation and negativity, fear, joy, sadness, surprise, and disgust. Conversely, VADER
is less volatile. Positivity outweighed negativity by less than the NRC. However, the compound score
is interesting to note. As Singh (2020) puts it: ”the compound score is the sum of positive, negative
neutral scores which is then normalized between -1(most extreme negative) and +1 (most extreme positive). 
The more the Compound score is closer to +1, the higher the positivity of the text.
Therefore I can see days in May, June and July that shot completely negative - when overlaying the
two scores in figure 7, these spikes were not obviously correlated. Further analysis revealed that the
lowest compound score only had a score of 9 for the day, which was the lowest in the data set. The
post volume was low - some Reddit comments indicate the site was having difficulties that day. May
20, the second lowest compound score, is also associated with low text volume. So perhaps just a few
angry posters overweighted the VADER for the day.


Once sentiment scores were extracted, I attempted to use them to predict whether the stock will
go up or down on the following day, with the goal of generating returns above a common baseline
strategy. I used two datasets for this - one featured all posts that had more than 50 upvotes. The
other was filtered to only posts that included a variation of 'gamestop’ in the text with no upvote
threshold. For sake of brevity, I only report the latter here. However, both results can be found in
the accompanying Jupyter notebook.


After using baseline models, I ran this using the Vader and NRC sentiment scores and saw significantly improved performance.
I combined all of the positive and negative categories by summing them up
and then calculated the percentage change of positive scores and the percentage change of negative
scores from day to day. The results are seen in figure 12.
LS Strategy: 259.37 USD LO Strategy: -22.14 USD
To summarize in 2 you can see that our sentiment trading strategies far outperformed the dual
moving average strategy. We also saw this when running the trade strategy across the entire corpus
instead of GameStop filtered texts, although the returns were not as high.
We should note that this strategy is back-tested and may not work in the future. The GameStop
trading period was a very unique time on Wall Street and was extremely volatile - so luck is more at
play. That said, it is encouraging to see that both the NRC and the Vader sentiment libraries were
able to generate features that could be used to extract positive returns from an extremely volatile
market.

A far more thorough analysis can be achieved through scraping all of the Reddit comments in addition
to the posts. There are daily discussion threads in this data set that can have thousands of comments
each which are not included in this data. Although computationally it would be extremely intensive,
analysis of such data would likely yield better results. Further, there are likely highly significant confounders at play. 
Although an AR1 is not being used, the sentiment on the Reddit forums very likely reflected the stock price movements and can serve as a
proxy for such. We also do not take into account the number of comments or upvotes on posts - giving
greater weight to the text with more visibility (comments and upvotes) would be another strategy to
fine tune the results.


