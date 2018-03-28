# KKBox-s-Churn-Prediction-Project
binary classification



# Data Description

In this challenge, you are asked to predict whether a user will churn after his/her subscription expires. Specifically, we want to forecast if a user make a new service subscription transaction within 30 days after the current membership expiration date.

KKBOX offers subscription based music streaming service. When users signs up for our service, users can choose to either manual renew or auto-renew the service. Users can actively cancel their membership at any time.

The churn/renewal definition can be tricky due to KKBox's subscription model. Since the majority of KKBox's subscription length is 30 days, a lot of users re-subscribe every month. The key fields to determine churn/renewal are `transaction date`, `membership expiration date`, and `is_cancel`. Note that the `is_cancel` field indicates whether a user actively cancels a subscription. Subscription cancellation does not imply the user has churned. A user may cancel service subscription due to change of service plans or other reasons. **The criteria of "churn" is no new valid service subscription within 30 days after the current membership expires.**

UPDATE: As of November 6, 2017, we have refreshed the test data to predict user churn in the month of April, 2017

The training and the test data are selected from users whose membership expire within a certain month. The train data consists of users whose subscription expires within the month of February 2017, and the test data is with users whose subscription expires within the month of March 2017. This means we are looking at user churn or renewal roughly in the month of March 2017 for train set, and the user churn or renewal roughly in the month of April 2017. Train and test sets are split by transaction date, as well as the public and private leaderboard data.

In this dataset, KKBox has included more users behaviors than the ones in train and test datasets, in order to enable participants to explore different user behaviors outside of the train and test sets. For example, a user could actively cancel the subscription, but renew within 30 days.



# Data Extraction Details

We include the code "WSDMChurnLabeller.scala" for generating labels for the user of our interest. The code provided is the one we used to generate the label for the test data set. Note that the date values in the script is modified so it is easier to run on personal laptops. On our cluster, the log history starts from 2015-01-01 to 2017-03-31. With the provision of the user label generator, we encourage participants to generate training labels using data not included in our sample training labels.

One important information in the data extraction process is the definition of membership expiration date. Suppose we have a sequence for a user with the tuple of (`transaction date`, `membership expiration date`, and `is_cancel`):

(2017-01-01, 2017-02-28, false)

(2017-02-25, 0217-03-15, false)

(2017-04-30, 3017-05-20, false)

(data used for demo only, not included in competition dataset)

This user is included in the dataset since the expiration date falls within our time period. Since the subscription transaction is 30 days away from 2017-03-15, the previous expiration date, we will count this user as a churned user.

Let's consider a more complex example derive the last one, suppose now a user has the following transaction sequence

(2017-01-01, 2017-02-28, false)

(2017-02-25, 2017-04-03, false)

(2017-03-15, 2017-03-16, true)

(2017-04-01, 3017-06-30, false)

The above entries is quite typical for a user who changes his subscription plan. Entry 3 indicates that the membership expiration date is moved from 2017-04-03 back to 2017-03-16 due to the user making an active cancellation on the 15th. On April 1st, the user made a long term (two month subscription), which is 15 days after the "current" expiration date. So this user is not a churn user.

Now let's consider the a sequence that indicate the user does not falls in our scope of prediction

(2017-01-01, 2017-02-28, false)

(2017-02-25, 2017-04-03, false)

(2017-03-15, 2017-03-16, true)

(2017-03-18, 2017-04-02, false)

Note that even the 3rd entry has member ship expiration date falls in 2017-03-16, but the fourth entry extends the membership expiration date to 2017-04-02, not between 2017-03-01 and 2017-03-31, so we will not make a prediction for the user.