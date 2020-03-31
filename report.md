2.0 Hidden Markov Model:
========================

Can we predict the performance of a hub compared to its average attendance using hub data?
------------------------------------------------------------------------------------------

Due to Covid time pressure we started with a single hub to examine the
potential of this approach and the results have not been cross
validated.

Using Hub data we implement a Hidden Markov Model to show that by
utilising observed hub activity can be used build a generalised method
for forecasting hub performance.

2.1 What is a Hidden Markov Model?
----------------------------------

A Hidden Markov Model uses Markov chains to try and make a prediction
about the future based on the sequence of events we have observed in our
data. To outline the relevance of this model we will highlight an
example described by Eisner (2002). Suppose we want to study the
sequence of Hot(H) and Cold(C) days to predict how much ice-cream our
Neighbour will eat every day. If we have observed how many ice-creams
our neighbour has eaten over three dats (e.g., 3 1 3 ) for a given
sequence of weather (e.g., H H C) we can estimate the likelihood of this
many ice-creams being eaten each day. As we build up more information we
can begin to forecast how many ice-creams our neighbour will eat.

2.2 How do HMM's apply to Community Hubs Data?
----------------------------------------------

Using a HMM we can forecast how a hub will perform in relation to its
own past performance and then cluster hubs with a similar performance
trajectory as well as hubs with similar predictors of performance.

What this means is that we want to group hubs with similar indicators
for how they are performing based on scheduled activities across time.

We beilieve that a HMM is both better suited to the categorical data and
also easier to implement than a Time Series Regression. When clustering
is performed using categorical variables, these clusters are incredibly
hard to transfer into a time series.We hope that applying a HMM will
solve this problem. We take inspiration from Ghassempour et al (2014)
who perform a time series cluster of medical data using the HMM.

This approach can be thought of as using machine learning to classify a
time series to observe how similar hubs are tracking.

We hope that this will provide information about what types of classes
are most in demand and what scheduling appears to be most accessible to
each school and community.

2.3 Data preparation
--------------------

First we choose a single hub 15994. Then we remove outliers and rows
with zero participants. Our suggestion for predicitng zero values is to
explain their presence with clustering and by incoperating important
dates such as holidays. This can be achieved with the R package Naniar.
(Tierney 2019)

Day of Week and Month of Year are taken from Activity Date as variables
for our model.

2.4 Building the Model
----------------------

We use the DepmixS4 package Visser, I., & Speekenbrink, M. (2010) in R
for Hidden Markov Models. We use a multinomial distribution to best
capture the categorical distribution of the high dimensional data. We
start with a 2 state model for simplicity. Our two states are State 1
Performing Below average. State 2 Performing at or above average

To build an equation we start with a small number of variables. We
choose variables which exist in all hubs and all years.  
"ShortName", "CategoryName", "Month" and "Day" Month and Day are taken
out of the timestamp using the Lubridate package in R and scaled from 0
to 1 for comparing the results between the two. Names are textual data
and don’t need to be scaled.

We use LogLikelihood calculated from the DepmixS4 packaged to order the
variables in our equation based on how they perform at predicting Hb
performance (from best to worst). This is done using a stepwise
approach. Find the first best variable, remove that from the process and
repeat to find the next best variable and so on.

We want to test the hidden states predicted from the sequence of these
variables against some actual states in the data.

For this we use an aggregate of “AdultParticipants” and “Child
Participants” We use the difference between the (sum of Adult and
Children participants for a single row) and (the mean Child and Adult
participation for the entire hub) as an indicator for how a hub is
performing compared to its own average. This is also scaled from 0 to 1.

2.5 Results
-----------

We find that our hidden states which actually match up to the
Participation data extremely well.

In state 1 there was a 66% probability of the hub having below average
attendance

In state 2 there was a 99% probability of having high attendance.

As we have trained on quite a small sample size of a single hub we are
quite sceptical of such a high probability for (State 2) and would
expect this to drop once we look at more hubs.

These results sound promising but this is only a training model so the
results are still quite unstable and need to be cross validated by
testing on other hubs.  
\#\# 2.6 Visual Comparison We use visuals to observe how well our
predicted states match up to the actual perfomance of Hubs implied by
the attendance figures. Given the large variation in attendance week to
week, these plots will look quite messy to if you havent seen them
before. For example what we want to see is that when there is a high
probability of the hub being in an above active state (State 2) that
this looks very similar to attendance at the same point on the X-axis.
When we have a longer period of being in State 2 in (Implied State plot)
we want to the corresponding proability line for State 2 (red) to be at
the top in the (Probabilites plot) this is largely what we see which is
promising.

### x.png

### y.png

2.7 Conclusion and Next Steps
-----------------------------

Run this training model for other hubs to cross validate the results.
Look to expand the equation to improve the predicition scores. And begin
to cluster the hubs.

2.8 References
--------------

Eisner, J. (2002, July). An interactive spreadsheet for teaching the
forward-backward algorithm. In Proceedings of the ACL-02 Workshop on
Effective tools and methodologies for teaching natural language
processing and computational linguistics (pp. 10-18).

Tierney, Nicholas, et al. "Naniar: Data structures, Summaries, and
Visualizations for Missing Data." R Package (2019).

Visser, I., & Speekenbrink, M. (2010). depmixS4: an R package for hidden
Markov models. Journal of Statistical Software, 36(7), 1-21.

Ghassempour, S., Girosi, F., & Maeder, A. (2014). Clustering
multivariate time series using hidden Markov models. International
journal of environmental research and public health, 11(3), 2741-2763.
