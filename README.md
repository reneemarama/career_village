# Career Village Recommender

[TOC]

## Problem Statement

"CareerVillage.org is a nonprofit that crowdsources career advice for underserved youth. Founded in 2011 in four classrooms in New York City, the platform has now served career advice from 25,000 volunteer professionals to over 3.5M online learners. The platform uses a Q&A style similar to StackOverflow or Quora to provide students with answers to any question about any career.
"In this Data Science for Good challenge, CareerVillage.org, in partnership with Google.org, is inviting you to help recommend questions to appropriate volunteers. To support this challenge, CareerVillage.org has supplied five years of data." [1]

*Link to kaggle competition: https://www.kaggle.com/c/data-science-for-good-careervillage*

Due to a lack of time, I did not compete for the original problem statement and instead focused on developing a model to **prompt students with words that are more likely to help them get their questions answered**.

## The Data

As quoted from the Kaggle competition site:

"CareerVillage.org has provided several years of anonymized data and each file comes from a table in their database.

- **answers.csv:** Answers are what this is all about! Answers get posted in response to questions. Answers can only be posted by users who are registered as Professionals. However, if someone has changed their registration type after joining, they may show up as the author of an Answer even if they are no longer a Professional.
- **comments.csv:** Comments can be made on Answers or Questions. We refer to whichever the comment is posted to as the "parent" of that comment. Comments can be posted by any type of user. Our favorite comments tend to have "Thank you" in them :)
- **emails.csv:** Each email corresponds to one specific email to one specific recipient. The frequency_level refers to the type of email template which includes immediate emails sent right after a question is asked, daily digests, and weekly digests.
- **group_memberships.csv:** Any type of user can join any group. There are only a handful of groups so far.
- **groups.csv:** Each group has a "type". For privacy reasons we have to leave the group names off.
- **matches.csv:** Each row tells you which questions were included in emails. If an email contains only one question, that email's ID will show up here only once. If an email contains 10 questions, that email's ID would show up here 10 times.
- **professionals.csv:** We call our volunteers "Professionals", but we might as well call them Superheroes. They're the grown ups who volunteer their time to answer questions on the site.
- **questions.csv:** Questions get posted by students. Sometimes they're very advanced. Sometimes they're just getting started. It's all fair game, as long as it's relevant to the student's future professional success.
- **school_memberships.csv:** Just like group_memberships, but for schools instead.
- **students.csv:** Students are the most important people on CareerVillage.org. They tend to range in age from about 14 to 24. They're all over the world, and they're the reason we exist!
- **tag_questions.csv:** Every question can be hashtagged. We track the hashtag-to-question pairings, and put them into this file.
- **tag_users.csv:** Users of any type can follow a hashtag. This shows you which hashtags each user follows.
- **tags.csv:** Each tag gets a name.
- **question_scores.csv:** "Hearts" scores for each question.
- **answer_scores.csv:** "Hearts" scores for each answer. "

[1]

## Results/Abstract

The analysis contained in the modeling notebooks of this project aim to provide insight into the words that are highly correlated with questions being answered. This would enable CareerVillage.com to prompt students to include words that are more likely to help them get their questions answered.

Two models were created, one from un-sampled raw data (the whole data set) and one from a sample of the population with higher proportion of un-answered questions. The sampled data set was created to investigate whether the initial model was biased, as the Random Forest model test score was 0.9980.

### Raw Data Random Forest Confusion Matrix:

<img src=".\Plots\unsampled_random_forest_confusion_matrix.png" alt="unsampled random forest confustion matrix" width="600" style="float: left;"/>

### Sampled Data Random Forest Confusion Matrix:

<img src=".\Plots\sampled_random_forest_confusion_matrix.png" alt="sampled random forest confustion matrix" width="600" style="float: left;"/>

### Top 100 words correlated with answered questions

#TODO insert a table of the top 100 words generated from the Raw Data Random Forest model



## Modeling Prep

### Merging Data

<img src=".\Plots\join_methods.png" alt="join methods" width="600" style="float: middle;"/>

For the career village recommender system, we are given 15 CSV's to work with. To make future processing easier and to find significant relationships, I merged datasets with information germane to my analysis effort. After opening and playing around with the datasets I realized that merging them would not be as simple as originally hoped. The datasets do not line up 1-1, and when merging multiple datasets I would either lose or duplicate information. 

After much consternation over how I could merge the disparate datasets, I was eventually able to combine datasets in various configurations. The combinations usually did not contain more than two types of data (e.g. questions + answers), or I was able to add a parameter representative of additional datasets for my desired analysis.

For general cleaning, EDA, and modeling I merged, in various combinations:

- answers.csv
- answer_scores.csv
- question_scores.csv
- questions.csv
- tag_questions.csv
- tags.csv

### Cleaning

#TODO : insert image of sweeping broom

Before conducting EDA and building a model the data needed to be formatted in a way that would not produce errors during the analysis. In a combination of initial exploration and later EDA the following steps were needed:

1. Converting to Appropriate Datatype
2. Sorting Data
   1. defining data types for numerical analysis and textual cleaning
3. Handling Nulls
4. Cleaning Text of HTLM artifacts

Cleaning of the data was performed in two iterations. The first iteration supported initial EDA and modeling, and the second iteration implemented lessons learned from the first dataframe. Specifically, during the second iteration of cleaning I merged the data so that I did not have any duplicates. This involved removing the text contributed from the answers and tags and applying a simple "was_answered" and "has_tag" flag to the questions. I was unable to perform more complex analysis due to the dimensional reduction of our dataframe in this case, but time was rather limited anyway and the resulting analysis sufficed.

### EDA

#### General Inspection

This was some of the more interesting work!

The journey starts off in the NLP EDA notebook with some general EDA to get a sense of the distributions among the numerical data. The histograms unfortunately provided little guidance on where our analysis should proceed.

<img src=".\Plots\3a_initial_hist_answered.png" alt="join methods" width="600" style="float: middle;"/>

*Fig. xx histograms of numerical columns for questions that have been answered*

#TODO : look into auto-numbering of figures: https://tamarisk.it/automatically-numbering-figures-in-markdown/

#### NLP EDA

Then, since we are dealing with questions and answers, I thought it would be useful to investigate how the sentiment of each question impacted the probability of being answered. To this end, I called the SentimentIntensityAnalyzer() from the Natural Language Toolkit (NLTK) to create sentiment scores for each question and answer. 

<img src=".\Plots\3a_answers_sentiment_hist.png" alt="join methods" width="500" style="float: middle;"/>

<img src=".\Plots\3a_questions_sentiment_hist_answered.png" alt="join methods" width="500" style="float: middle;"/>



<img src=".\Plots\3a_questions_sentiment_hist_answered_not.png" alt="join methods" width="500" style="float: middle;"/>

#### A Closer Look at Question and Answer Score Distributions

Another thing I did was look more closely into the histograms of the scores, since there was a more granular way to represent the data:

<img src=".\Plots\3a_answers_score_hist.png" alt="join methods" width="600" style="float: middle;"/>

<img src=".\Plots\3a_questions_score_hist.png" alt="join methods" width="600" style="float: middle;"/>

#### Correlation to Questions Getting Answered

Finally, I looked at the [Pearson coefficients](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient) (normalized covariance, looking at how the respective variables relate to `was_answered`). Unfortunately there was only a small amount of correlation and so further modeling using the question sentiment or other numerical variables for prediction was not pursued. Using `answers_sentiment` as a predictor is a non-starter since the only way for an answer to have a sentiment is for it to be answered in the first place.

<figure>
    <img src=".\Plots\3a_correlation_numerical_data.png" alt="join methods" width="600" style="float: middle;"/>
    <figcaption><bold>xx. </bold> Pearson Coefficients of the numerical data w.r.t. questions getting answered.<figcaption>
</figure>

#### Further Investigation into scores

##### General Stats for Question and Answer Scores

![image-20230601070302807](.\Plots\3b_general_stats_scores.png)

##### Answer Scores

In the following box plot, we can see that not many professionals received upvotes for their answers. Only very small percentage garnered an average answer score above 1. In fact, if we look at the actual counts of the number of the average scores, in the table below the plot, we can see that the majority of answers are not upvoted. This makes sense since only the top answer is usually upvoted in these community-driven forums. Perhaps CareerVillage.com could do better at disseminating answered questions though, since on sites like Stack Overflow, there are more often than not multiple upvotes for good answers. I would be curious to know if CareerVillage.com has a lot of repeated or very similar questions.



<figure>
    <img src=".\Plots\3b_average_answ_score_per_professional.png" alt="join methods" width="600" style="float: middle;"/>
    <figcaption>Fig. xx: Box plot of the average answer score per professional.<figcaption>
</figure>


| Answer Score | Count of Answers with Score |
| :----------- | --------------------------- |
| 0            | 7346                        |
| 1            | 2108                        |
| 2            | 135                         |
| 3            | 63                          |
| 4            | 19                          |
| 5            | 13                          |
| 6            | 11                          |
| 7            | 3                           |
| 8            | 2                           |
| 9            | 2                           |
| 10           | 1                           |


There are a total of 1017 professionals (out of ~10k) with an average answer score above 1. Only 319 averaged an answer score of 2 or more.

```python
data.groupby('answers_author_id').mean().sort_values('answers_score', ascending=False).loc[\
    data.groupby('answers_author_id').mean().sort_values('answers_score', ascending=False)['answers_score'] > 1].shape
```

`(1017, 7)`

#### Looking at distribution of time it takes to answer

General stats for how long it takes for a question to be answered. As we can see here the mean is 142 days, while the median is 22 days. The average is heavily skewed by the max amount of days it took to answer a question; 2,562, which is about 7 years. This is highly suspicious that a question took 7 years to be answered. Additionally, the min is negative, and an answer to a question can't be posted before it was asked. Since the data was likely auto-generated from the site, it's unlikely it's an imputation error. It may be a merging error due to how messy the data has been in general. The other possibility is that if an answer was posted for a different similar question, it might be linked to the new question (this would have to be confirmed by careervillage.com).

<figure>
    <img src=".\Plots\3b_general_stats_length_time_answered.png" alt="join methods" width="1000" style="float: middle;"/>
    <figcaption>Fig. xx: General statistics for the length of time to answering a question.<figcaption>
</figure>

Taking a look at the histogram of the number of minutes until a question is answered, we see that the majority of questions are answered soon after the question is posted, and then there are slight bumps in answer volume over time.

<figure>
    <img src=".\Plots\3b_hist_mins_to_answered.png" alt="join methods" width="600" style="float: middle;"/>
    <figcaption>Fig. xx: General statistics for the length of time to answering a question.<figcaption>
</figure>

Though not addressed in this analysis, it would be interesting to investigate how a question is phrased impacts the length of time to an initial answer. 

#### Tag EDA

23288 questions out of 23931 (97.31%) have tags related to them. However, I was not able to gain appreciable insight from the tags in the time allotted and continued forward with the other data.



## Modeling

Two sets of modeling notebooks were created for the two different sets of combined data, and the ultimate goal was to predict if a question would be answered or not. Initially, a set of data that contained many duplicates with respect to question ID was analyzed (since there were questions with multiple answers and tags). This proved to make rather good predictions on the test data, but I was worried that the duplicate sets of information might have crept their way into the training data and skewed the results. To resolve this, a second data set was put together that removed any duplicate question IDs. In order to put this data together though, I had to replace the tag and answer information with simple T/F indications. Additionally, I un-biased the training data by including all the un-answered questions and sampling the answered questions to a smaller set. The un-sampled data is 98% answered questions.

### Un-sampled modeling

The data columns used for modeling in the un-sampled data frame were:

```
questions_id
questions_author_id
questions_date_added
questions_title
questions_body
questions_score
tag_id
tag_name
was_answered
has_tag
```

**Random Forest Confusion Matrix Results:**

<img src=".\Plots\unsampled_random_forest_confusion_matrix.png" alt="unsampled random forest confustion matrix" width="600" style="float: left;"/>

**Top 100 Features For Getting a Question Answered**

<img src=".\Plots\4a_top_100_words_answer_yes.png" alt="" width="1000" style="float: left;"/>

**Top Feature is `word_count`**

<img src=".\Plots\4a_top_10_features_answer_yes.png" alt="" width="800" style="float: left;"/>

**Besides Word Count, the next top 10 features (words) have much less impactful coefficient values**

<img src=".\Plots\4a_top_10_features_answer_yes_no_word_count.png" alt="" width="800" style="float: left;"/>

**Bottom 100 Features For Getting a Question to NOT Get Answered**

<img src=".\Plots\4a_top_100_words_answer_no.png" alt="" width="1000" style="float: left;"/>

**Bottom 10 Features and Their Exponential Coefficients**

<img src=".\Plots\4a_top_10_features_answer_no.png" alt="" width="800" style="float: left;"/>

### Sampled modeling

To better understand the impact of how a question is asked without reams of duplicate data, I built a model to see how well I could predict if a question was answered based on the question text and the question score. I grid-searched over two dataframes with several models.

*Dataframe 1:* In the unsampled model from the notebook "4a_Unsampled_Modeling.ipynb", I performed a grid-search over the whole data frame using `questions_body` and `questions_score` as features. Since the classes were highly unbalanced (answerd vs. un-answered), with a 98% baseline bias, I decided to sample the data to even out the classes. Additionally, I removed duplicate data by removing answer and tag data and replacing them with a "was_answered" and "has_tag" boolean column. Both the `questions_body` and `questions_score` were kept as features.

*Dataframe 2:* In the second dataframe, I used the same sampled dataframe as in "dataframe 1" but dropped the `questions_score` feature, since I hypothesized that `question_score` was a strong indicator of a question being answered. Additionally, it is easier to get real-time feedback on the text of your question, versus waiting for others to up-vote it, in order to increase your chances of the question being answered.

For the first dataframe, I grid-searched over Logistic Regression, K-Nearest Neighbors, and Random Forest, with Random Forest Providing the best results.

In the second dataframe I only used Logistic Regression due to time.

The data columns used for modeling in the sampled data frame were:

```
questions_id
questions_author_id
questions_date_added
questions_title
questions_body
questions_score
was_answered
```

Note that `tag_id` is missing from this dataframe since all questions had tags associated due to the rules enforced by CareerVillage.com

**Random Forest Confusion Matrix Results (Dataframe 1):**

<img src=".\Plots\sampled_random_forest_confusion_matrix.png" alt="Sampled random forest confustion matrix" width="600" style="float: left;"/>

**Logistic Regression Confusion Matrix Results (Dataframe 2):**

<img src=".\Plots\4b_sampled_logreg_confusion_matrix.png" alt="sampled logistic regression confustion matrix" width="600" style="float: left;"/>

**Top 100 Features For Getting a Question Answered**

<img src=".\Plots\4b_top_100_words_answer_yes.png" alt="" width="1000" style="float: left;"/>

**Top 10 Features and Their Exponential Coefficients**

<img src=".\Plots\4b_top_10_features_answer_yes.png" alt="" width="800" style="float: left;"/>

**Bottom 100 Features For Getting a Question to NOT Get Answered**

<img src=".\Plots\4b_top_100_words_answer_no.png" alt="" width="1000" style="float: left;"/>

**Bottom 10 Features and Their Exponential Coefficients**

<img src=".\Plots\4b_top_10_features_answer_no.png" alt="" width="800" style="float: left;"/>



## Recommendations

Due the limitation in time to fully investigate the relationships among the given raw datasets, I would recommend the following activities to further explore how to prompt users to generate questions that are more likely to get answered:

1. Look at if there are specific tags that correlate with getting answered more frequently; professionals may have tag filters in place.
2. Look into if the sentiment of the question impacts how quickly it was answered.
3. Generally look into how quickly questions are answered and if we can predict it given the available features.
4. Incorporate more of the raw data sets into the analysis.

## References

1. Chris Crawford, Jared Chung. (2019) *Data Science for good: Careervillage.org*. Kaggle. (n.d.). Retrieved March 2, 2020, from https://www.kaggle.com/competitions/data-science-for-good-careervillage/overview
