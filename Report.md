# Summary Section
In this assignment, I wanted to discover whether there is a correlation between sentiment of the text in a 10-k file and the stock returns. I want to discover if there is a correlation between returns and positive and negative sentiment. In addition to that, I want to discover the correlation in text related to the topics of covid, government regulation, and technology. 

To do this I looped over a sample of 10-K documents and created 10 variables that measure sentiment as stated above. These variables present a sentiment score, which measure what percent of 'hits' of the sentiment variable divided by the total words in the 10-K file.

In terms of findings, I had major issues in my BuildSample file where my kernal would die before I could run my entire loop, which would find the sentiment variables for each 10-K. As a result, I do not have findings for my report. So, for the report when talking about findings, I will explain the hypotheticals finding as it relates to each question. In other words, I will explain how my data relates in certain scenarios.

## Data Section

# Explain what the sample is
The sample is a list of around 500 companies taken from crsp 2022 return data,.

# Explain how the return variables are built and modified

The return variables were calculated by creating two different rolling windows. First the data is extracted from the crsp 2022 returns url from github.
Next, the returns are found and added to my dataframe, return_2022 using these formulas:
  - return_2022['ret_first3'] = return_2022.groupby('ticker')['ret'].rolling(3).apply(lambda x:                 np.prod(1+x)-1).shift(-2).reset_index(0, drop=True)

 - return_2022['ret_next8'] = return_2022.groupby('ticker')['ret'].rolling(11).apply(lambda x:                 np.prod(1+x.iloc[3:])-1).shift(-10).reset_index(0, drop=True)

These formulas grab the returns grouped by ticker. The first formula finds the return from day 0 to day 2. The second formula finds the returns from day 3 to day 10. Finally, both formulas are shifted so the returns are on the index of the filing date. 


#  Explain how the sentiment variables are built and modified

- There are two groups of variables. First are the 4 sentiment variables that are simply built through existing word lists relating to 10-K sentiment. I created the variables bhr_positive, bhr_negative, LM_negative and LM_positive by reading in the documents in the repo. Finally, I had to adjust the variables format, so that the variables would be in the correct format for the NEAR_regex to use. 
- The next 6 variables are measuring sentiment based on three topics that are related to topics that could be presented in a 10-K document. The next six variables were created by listing a sample of key word that relate to the topic.
- An example formula that illustrates this is as follows:  
    -- gov_words = '(legislation|regulation|sanction|regulatory|political|lawsuit)' 
- After this, I created an object that compared the key words to either bhr_positive or bhr_negative:
    -- gov = [gov_words, '(' + "|".join(bhr_negative) +')']
- Next, I created an object that uses the Near_regex to find the amount of hits of the key word being near a negative or positive sentiment word:
    -- gov_positive = NEAR_regex(gov,max_words_between=4)
- Finally, to get the final sentiment variable, I divided the amount of hits by the length of the cleaned 10-K docuemnt.

# Why did I choose the three topics
- When choosing what topics to use, I decided to use three variables that are very different from each other, but all would be applicable to companies in various industries and sectors. 
- I chose to use covid data because even in 2022 covid still has an important impact on business operations. I wanted to explore whether companies still have negative or positive sentiment relating to the lingering effects of covid on business operations.
- I chose government regulation because this is a topic that can effect all types of companies. I wanted to see whether companies explicitly use negative language relating to potential government regulation that may affect business operations in the future. 
- Lastly, I chose technology because new technological advancements will have a large impact on business operations going into the future. I was interested in seeing whether the companies are optimistic or pessimistic about new technology as it relates to future returns

# Show and discuss summary stats
This section, I cannot complete due to my lack of findings. However, I included a screenshot of the one time when my loop ran completely without the kernal dying. I attached this screenshot into the repo title loop.output.png.



## Contextual sentiment tests
- To make sure that my contextual variables I tested the formulas with a sample sentence to see my code was working properly.
- For example, here is my code for positive government regulation analysis. 
    - gov_texts  = 'There was a revised strength lawsuit, which increase productivity'
      gov_words = '(legislation|regulation|sanction|regulatory|political|lawsuit)' 
      gov = [gov_words, '(' + "|".join(bhr_positive) +')']
      gov_positive = NEAR_regex(gov,max_words_between=4)
      print(len(re.findall(gov_positive,gov_texts)))   
-  I tested the variable against that sample sentence to see whether it will work for an entire 10-k docuemnt


# Potential caveats
- In terms of potential caveats, there is a few things to consider,
- First the limited words to describe the topic, can cause issues with the validity of the analysis. I tried to make sure the words used were very specific with what I was analyzing, so I did not use any words that could be used in a different way than intended. As a result, I may miss instances of the topic being discussed in a way that was outside my word list. Also, for the near_regex analysis, I used between 5 words, which should be somewhat comprehensive, but may miss or include erroneous hits. 
- Also, there lack of nuance in this analysis, may be a cause for concern. This code simply seems if a positive or negative word are in the sentence around a word that relates to a certain topic. However, this may not mean that the negative sentiment is directed at the topic word that is included. This code does a good job of overall scanning a 10-K document, but can be limited in its precise functionality


## Results Section


```python
# Table with correltion of each sentiment agaisnt 2 return 10x2

# Code to create the table: If I had one

merge_sp500 = correlation[['ret_first3', 'ret_next8', 'bhr_pos_sent',
                           'bhr_neg_sent', 'LM_pos_sent', 'LM_neg_sent', 
                           'gov_pos_sent', 'gov_neg_sent', 'covid_pos_sent', 
                           'covid_neg_sent', 'tech_pos_sent', 'tech_neg_sent']]


```


```python
# Scatterplot of each senitment measure

import seaborn as sns

sns.scatterplot(data=df, x='ret_3', y='bhr_pos_sent', hue='ret_8')
sns.scatterplot(data=df, x='ret_3', y='bhr_neg_sent', hue='ret_8')
sns.scatterplot(data=df, x='ret_3', y='LM_pos_sent', hue='ret_8')
sns.scatterplot(data=df, x='ret_3', y='LM_neg_sent', hue='ret_8')
sns.scatterplot(data=df, x='ret_3', y='gov_pos_sent', hue='ret_8')
sns.scatterplot(data=df, x='ret_3', y='gov_neg_sent', hue='ret_8')
sns.scatterplot(data=df, x='ret_3', y='covid_pos_sent', hue='ret_8')
sns.scatterplot(data=df, x='ret_3', y='covid_neg_sent', hue='ret_8')
sns.scatterplot(data=df, x='ret_3', y='tech_pos_sent', hue='ret_8')
sns.scatterplot(data=df, x='ret_3', y='tech_neg_sent', hue='ret_8')
```

# Discussion

#1 Compare and Contrast relationship between return and LM sentiment variables

For this section there is likely to be a slight correlation between return and LM sentiment variables. The LM packages is very expansive and over a large data set there should be a correlation between the positive sentiment words and returns.

#2 If comaprison conflcits with ML_JFE

In ML_JEFE table 3, there is a slight negative correlation between LM positive words and an even lower negative correlaton with LM negative and returns. 

#3 Discuss 3 contexual variables

 - In my opinion, the technology positive words would likely have the greatest correlation for return data, with the future outlook a large part in that. I think the negative variables relating to government would have the stongest correlation. The rest of the variables would have a correlation in negative or postive, but not a major correlation. The positive variables would also likely have a larger magnitude than the negative variables because of a company's liklihood to not use negative sentiment words, because they do not want to express potential downfalls in their company


```python

```
