This is all annotations from my jupyter notebooks (excluding some very code specific explanations)

Link to kaggle competition: https://www.kaggle.com/c/data-science-for-good-careervillage

# Problem Statement

CareerVillage.org is a nonprofit that crowdsources career advice for underserved youth. Founded in 2011 in four classrooms in New York City, the platform has now served career advice from 25,000 volunteer professionals to over 3.5M online learners. The platform uses a Q&A style similar to StackOverflow or Quora to provide students with answers to any question about any career.
In this Data Science for Good challenge, CareerVillage.org, in partnership with Google.org, is inviting you to help recommend questions to appropriate volunteers. To support this challenge, CareerVillage.org has supplied five years of data.
 


For the career village recommender system, we were given 15 CSV's to work with. To make future processing easier and to find significant relationships, merging relevant dataframes. After opening and playing around with the dataframes I realized that merging them wouldn't be as simple as I had hoped. The dataframes don't line up well, and when merging I'd either lose or make up information. I used several different merges of the dataframes to tell the story of the careerVillage data story. 

For general cleaning, EDA, and modeling I merged:
* answers.csv
* answer_scores.csv
* question_scores.csv
* questions.csv
* tag_questions.csv
* tags.csv
* professionals.csv

Other ways I merged the data


#### Unused CSV's

Given limited time, I only worked with 7/15 notebooks. 

#### Cleaning

Before conducting EDA and modeling, the data needed to be formatted in a way that won't throw errors. In a combination of initial exploration and later EDA the following steps are needed:

1. Create Boolean Columns 
2. Converting to Appropriate Data Type
3. Handling Nulls
4. Cleaning Text - NLP
5. Handling Duplicate Values Left Over from Merging 

#### Creating a "Was Answered" Column
    
Creating a column called `was_answered`, to indicate if the question was answered. `0` if there is no answer and `1` if there is. 

#### Exploring `was_answered` - if the question was answered

An initial look at `was_answered` stats shows that the majority of questions are answered.
[insert]

We can see that the average question score for where questions weren't answered was 0.75 and when answered 4.9 (the highest score is 125, with most between 0-10). The scores work similar to "upvotes" or "likes", 0 means 0 "upvotes", 5 means 5 "upvotes" etc. This intuitively makes sense, because questions with low or no upvotes are likely to go unanswered. 

An interesting thing to note is that the average answers_score is 0.45. Since it uses the same scale as above it looks like the majority of answers aren't getting "upvotes". 

In the NLP EDA notebook, I calculated the 'sentiment score' of the questions and answers body. I did this using the Natural Language Toolkit (NLTK) library. The sentiment score parses through the text and awards words a positive or negative score. Words like "like'', "love" or "happy" etc. are attributed to a positive score while "dislike" or "annoyed" etc. are given negative scores. Words like "the '', "it" etc. are considered neutral. NLTK then aggregates a score based on all the words in each passage. Though this is an imperfect approach (i.e. attributing a positive score to "don't like'' or the reverse), it provides a general understanding of the tone of the text. 

In the graph below, grouped by 'was_answered', one can see that the average questions_score is relatively similar for questions that were answered and were not answered. Since not all the questions are answered, there are all 0's in the `answers_sentiment` score where `was_answered` is 0.

[insert]

**The scores range from -1 to 1.** With 1 being positive, -1: negative.

#### Distribution of Answers Sentiment Scores:

The majority of `answers_sentiment` scores are close to 1, with a small bump just below 0. This is good and expected; the point of the platform is to empower and serve youth by answering their career questions. Negative sentiment scores would indicate that the professionals on the platform are not answering kindly, and should be flagged for investigation and possible removal from the platform. That said, humans are much better at dyshering tone from the text, and should be reviewed manually or with further analysis before any action is taken.

#### Graphing Questions Sentiment Scores

Though there is a general trend for questions to have a positive sentiment score, we can see that the distribution of scores varies more than the answer scores.

## Exploring Answers Authors

We wanted to know more about who's answering the questions. Below we're looking at how many people are answering questions and how many questions they're answering.

The total number of unique authors is 10,169.
[insert]


Professionals average answer score. Below are a few graphs that look top average `answers_score`.

319 out of 10169 professionals have an `answers_score` above 2.0
1017 out of 10169 professionals have answers_score above 1.0.

### Exploring Scores

Below is a look at the general stats, grouped by `was_answered` for question and answer scores. Unsurprisingly the average question score for questions that were answered is several points higher than wasn't answered. That said, it's interesting to see that the average score for questions that weren't answered was above 0, or almost zero. In looking at the max `question_score` for questions that weren't answered, we see a question had 123 "upvotes" and still wasn't answered. This is an anomaly, but interesting. 

#### General Stats for Question and Answer Scores:


The boxplot provides a good visual for how the answer scores and spread. The majority of scores are between 0 and 1. With outliers above about 2. Since there can't be negative scores, there are no outliers to the left of the graph.

The boxplot provides a good visual for how the question scores and spread. The majority of scores are between 2 and 4. With outliers above about 9. There are 75 unique question scores. 

[inset]

A deeper dive into the distribution of `questions_scores` shows just how many of the scores are between 0-5, and how few are above 5.

Creating a column called `time_to_answer` that is a calculation of the `answers_date_added` minus `questions_date_added` columns. The data is in date-time units.
[insert time]

General stats for how long it takes for a question to be answered. As we can see here that the mean is 142 days, while the median is 22 days. The average is heavily skewed by the max amount of days it took to answer a question; 2,562, which is about 7 years. This is highly suspicious that a question took 7 years to be answered. Additionally, the min is negative, and an answer to a question can't be posted before it was asked. Since the data was likely auto-generated from the site, it's unlikely it's an imputation error. It may be a merging error due to how messy the data has been in general. The other possibility is that if an answer was posted for a different similar question, it might be linked to the new question (this would have to be confirmed by careervillage.com).

# Modeling
## Predicting If the Question Was Answered

## Baseline Accuracy

Since most of the questions are answered the classes are highly unbalanced with 98% Baseline Accuracy Score

###Unsampled Data
For the first model, I grid-searched over Logistic Regression and began a K-Nearest Neighbors but after about 8 hrs I terminated the kernel - especially because Logistic Regression was providing 99% test accuracy (baseline of 98%). 

### Sampled Data
To better understand the impact of how the question was asked, I built a model to see how well I could predict if the question was answered. In this notebook, I grid-search over two sampled dataframes with several models.

In the unsampled model from the previous notebook, I grid-search over the whole data frame using `questions_body` and `questions_score` as features. Since the classes were highly unbalanced, 98% baseline score, I decided to sample the data to even out the classes testing to see if I can predict if a question was answered. In the second dataframe, there was the sampled data set, with only `questions_body` as a feature, since I hypothesized that `question_score` was a strong indicator if the question was answered. 

For the first dataframe, I grid-search over Logistic Regression, K-Nearest Neighbors, and Random Forest, with Random Forest Providing the Best Results. 

In the second dataframe I only used Logicstic Regression due to time. 

### Sampling the Data In Order to Create Even Classes

Since the classes above are so unbalanced I'm only taking a sample of the data were the question was answered. This creates a new baseline accuracy of 53% so we can actually model and test how much impact our features have on being able to predict if the question is answered or not (if we balance the classes the model could just predict was answered every time and would be 98% correct).

### Confusion Matrix

A confusion matrix provides evaluation metrics that highlight how the model is being accurate and erroneous. The confusion matrix below shows scores from the Random Forest Model which had the best prediction results.

After successfully modeling if a question will be answered or not based on question body and score, I wanted to know how we could predict just on question body since score was a likely tell. Below I modeled using a pipeline and grid-searching with logistic regression. Keep in mind, the below is with the sampled data and balanced classes.

#### Words Most Indicative To The Question Being Answered.
Below I set up and output a dataframe with the coefficients (words most indicative to questions being answered or not answered)

Recommendations



















