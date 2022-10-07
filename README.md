# enron
Analysis of enron emails
------------------------------------
scripts (details below):

parser.ipynb

add_features.ipynb

exploratory_analysis.ipynb

external_classifier.ipynb

data files:

emails.csv*: original enron dataset

df_20.csv*: 20% of original enron dataset

df_20_features.csv*: df_20.csv with one feature (is_external) added from add_features.ipynb 

df_test.csv: 25% of df_20_features.csv (5% of original enron dataset)

df_test2.csv: 25% of df_test.csv (1.25% of original enron dataset)

df_test2_subject_cleaned.csv: null subjects removed from df_test2.csv

*not in repo

Overall summary

I used the enron dataset for this analysis 
(https://www.kaggle.com/datasets/wcukierski/enron-email-dataset). The code is written in 
Python 3 with jupyter notebook. 

parser.ipynb cuts down the original dataset of 500K emails by 20%, and also parses the 
messages by the email fields (e.g., 'To:', 'From:', 'Subject:', and body). 

The script add_features.ipynb adds one feature, whether the email is external or internal. 
This was done by looking at the domains of the 'To:' and 'From:' email addresses.

The script exploratory_analysis.ipynb looks at count of emails by email addresses, both 
overall and bucketing by internal and external emails. I also looked at the wordclouds 
and tfidfs of the body and subject of the emails. My speculation is that both those fields 
might differ for internal and external emails. Eyeballing the wordclouds and tfidfs, it 
seemed like there could be something there.

In external_classifier.ipynb I built binary classifier models for internal/external using 
Random Forest. An initial model for body with the default hyperparameters had substantial 
overfitting, but it looked like it had some promise. An initial model for subject with the 
default hyperparamaters also had substantial overfitting and did not look promising. I did 
one pass of tuning on the body using a grid search. The results did not look great. But 
given the preliminary results, it looks like a reasonable model could be found with more tuning.

------------------------------------
parser.ipynb

input: emails.csv

output: df_20.csv

1. Cut down enron file to 20% of original, selecting random entries.

2. Parse email messages into fields
New fields (columns):
email_to
email_from
cc
bcc
subject
x_from
x_to
date_sent
body

------------------------------------
add_features.ipynb

input: df_20 (103480 rows, 20% of enron dataset)

output: df_20_features.csv

Features added (only 1 for now):
is_external: email sent or received from outside of enron

------------------------------------
exploratory_analysis.ipynb

1. count of emails, by email account
        broken down by (to, from, total) X (external, internal, overall)
        input: df_20_features.csv, rows = 103480, 20% of original enron dataset, 
               is_external feature added
        output: email_count.csv
2. Wordcloud of body and subject (external, internal, overall)
        input: df_test.csv, rows = 25870, 25% of df_20_features.csv 
               (5% of original enron dataset)
3. tfidf analysis, body (external, internal, overall)
        input: df_test2.csv, rows = 6467, 25% of df_test.csv 
               (1.25% of original enron dataset)
4. tfidf analysis, body (external, internal, overall)
        input: df_test2_subject_cleaned.csv, rows = 6235, 
               null subjects removed from df_test2.csv

Summary:

1. From the count of emails analysis, two aspects stood out. First, there was quite a spread 
of email counts by email address. Given that the dataset came from a limited number of Enron 
executives. Second, amongst the top 50 (by count) email addresses, the percentage of external 
email varies (with most being below 50%). Tha is also not suprising, given the different 
roles within Enron.

2., 3., and 4. The main aspect I was exporing was the possible difference between internal 
and external email. My speculation is that the style of communication in the booy should 
differ across internal and external communications. Also, the subjects of the emails might 
differ internally and externally. The wordcloud and tfidf analysis suggest that some 
differences might be there. To run the tfidf analysis, I needed to cut down the dataset 
substantially.

------------------------------------
external classifier.ipynb

0. read in data (df_test2, df_test2_subject_cleaned)
1. classifier predicting is_external (binary) from body (using Random Forest)
        input: df_test2.csv, rows = 6467, 1.25% of original enron dataset
2. classifier predicting is_external (binary) from subject (using Random Forest)
        input: df_test2_subject_cleaned.csv, rows = 6235, 1.25% of original enron dataset
3. hyperparameter tuning of classifier predicting is_external (binary) from body 
   (using Random Forest, Grid Search)       

Summary:

Based on the results of the exploratory analysis, the prospect of building a classifer for 
is_external seemed possible. I built a few quick models on the substantially reduced dataset 
and using Random Forest. I used both the body and the subject as inputs.

The body model with the default hyperparameters had substantial overfitting 
(roc-auc-train = .925, roc-auc-test = .717), but the results looked like they had potential.

The subject model with the default hyperparameters also had substantial overfitting 
(roc-auc-train = .812, roc-auc-test = .580). The test results were also not promising.

I did one pass of hyperparameter tuning using GridSearch. The best results from that 
(roc-auc-train = .600, roc-auc-test = .552) were disappointing. But given the results of the 
fit with the default hyperparameters, it seems that a viable model is possible with more tuning.
