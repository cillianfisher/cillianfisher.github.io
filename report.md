---
layout: wide_default
---  
# Report

## 1. Summary Section


For this assignment, I looked at how the words in companies' within the S&P500 10-K filings might give us clues about how their stock prices will move/react. 

Which made the general question of: Can the sentiment expressed in 10-K reports reveal important information about a company's value?

For this assignment, I loaded different sentiment dictionaries for both positive and negative sentiments. From there, I counted the amount of positive and negative sentiment words were in each individual cleaned 10-K reports. I then compared this to the length of each document to get 4 different  sentiment variables (there were 2 dictionaries). Although, I did more than just counting this specific list of words. I chose words that make sense for finance and business talk — by focusing on the specific topics of COVID, Ukraine, and Climate Change. I found 6 different variables from this, finding that the most prominent lis t We used these smarter word lists to see if the way companies write their reports tell us anything about what happens to their stock returns. It’s important to note that I did some data scraping to find the filing date for each 10-K, using the CIK and Accession number to do so. When comparing the correlation of return to each sentiment variable, I found that the negative sentiment for the third topic (Climate change), was the highest (.107681), while the lowest correlation came from the first topic (COVID) (-.060988).


## 2. Data Section

**Whats the Sample?**

-  The sample is financial data from companies, particularly their 10-K filings —  annual reports filed to the U.S. Securities and Exchange Commission (SEC) that provide a comprehensive summary of a company's financial performance. 
-  The data is focused on sentiment analysis using word lists that categorize words as either positive or negative according to two different dictionaries, as well as 3 different topic lists.


In order to load the 10-Ks I used the code below:

```python
sp500_file = 'inputs/sp500_2022.csv'

if not os.path.exists(sp500_file):
    url = 'https://en.wikipedia.org/w/index.php?title=List_of_S%26P_500_companies&oldid=1130173030'
    pd.read_html(url)[0].to_csv(sp500_file,index=False)
    
sp500 = pd.read_csv('inputs/sp500_2022.csv')    

**How are the return variables built and modified?**

The return variables are originally stored in crsp_2022. I was able to find the filling dates (just the first version), by creating a loop that goes to the URL of each 10-K and scrapes the filing date (using the CSS). I did this by creating a URL structure that implements each CIK and Accession number - which was found through the structure of each 10-Ks directory. I originally had made a dataset that included the CIK and Accession number so it was easier to do.

Important to note that i added sleep(.5) and described who I was, name email, so that I could scrape all of the date, because at first it was limiting.

```python
url = f'https://www.sec.gov/Archives/edgar/data/{cik}/{accession_number_folder}/{accession_number_with_dashes}-index.html'


From here, I found the filing dates for each firm. With this I merged my dataset with the correct filing dates with crsp_2022, which had returns for several dates for each stock. The merge was done using the ticker, but it's important to note I renamed the 'Suymbol' from my dataset to 'ticker' so that it would successfully merge, as a common key was neccessary.

```python
import pandas as pd

dates_df = pd.read_csv('output/correct_dates.csv')
sp500_df = pd.read_csv('sp500_2022.csv')

dates_df['CIK'] = dates_df['CIK'].astype(str).str.zfill(10)
sp500_df['CIK'] = sp500_df['CIK'].astype(str).str.zfill(10)

merged_df = pd.merge(dates_df, sp500_df[['CIK', 'Symbol']], on='CIK', how='left')

merged_df.to_csv('output/tickers_dates.csv', index=False)

**How are the sentiment variables built and modified?**

The first 4 sentiment variables were more simple to produce. I first began by deifing lists for each of the 4 variables -- there were 2 dictionaries. The first dictionary, ML, was already in a nice sturctured list, from here I made sure it was lowercased, and separated each word with | - I did this for both the positive and negative list. Below is an example of how I made the list, and structured it.

```python
file_path = 'inputs/ML_negative_unigram.txt'

BHR_negative = []

with open(file_path, 'r') as file:
    BHR_negative = [line.strip() for line in file.readlines()]

BHR_negative_joined = '|'.join(BHR_negative)

formatted_BHR_negative = [BHR_negative_joined]
    

For the other dictionary, I had to read the literature and restructure the words based on the year. I then structured it like the other list. 

Once I made the lists, I ran a loop through each 10-K, using CIK as my key and making sure to clean the 10-Ks and lowercasing, then  counted the amount of times the words were present in each. When I found this score for all 4 variables, I found the word count of each 10-K, added it to my dataframe, and finally divided my sentiment scores (4), by the total word count of the document. This gave me 4 variables for each 10-K, all of which being relatively low fractions.


The contextual sentiment variables are constructed using regex patterns, which match positive and negative sentiment words from the predefined lists within the text of 10-K filings. 
Here is the step-by-step process:

1. Lists of positive and negative words (`BHR_positive`, `BHR_negative`) created (as mentioned previously).
   
2. These lists are transformed into regex patterns that match with a list of words pertaining to each of the 3 topics. Those topics being: COVID, Ukraine, Climate change
     
5. The sentiment counts are obtained by finding all instances where the regex patterns match the text -- by finding instances where the ML lists (positive and negative), are found close to the predefined topic lists -- we'll go into more detail later .

7. Sentiment ratios are calculated by dividing the sentiment scores by the total word count of each document.

Here is an example of me using a loop to get the negative sentiment scores for my third topic:

```python
sec_filings_dir = '10k_files/sec-edgar-filings'

topic3_regex = r'(climate change|global warming|carbon emissions|greenhouse gases|renewable energy|sustainability|...)'
formatted_BHR_negative_regex = r'(dynamics|confident|necessary|steps|factors|pronounced|need|expectations|...)'
near_regex_pattern = NEAR_regex([topic3_regex, formatted_BHR_negative_regex], max_words_between=5, partial=True, cases_matter=False)

def extract_text_from_html(file_path):
    with open(file_path, 'r', encoding='utf-8') as file:
        soup = BeautifulSoup(file.read(), 'html.parser')
    return soup.get_text()

sentiment_scores = []

for cik in os.listdir(sec_filings_dir):
    cik_path = os.path.join(sec_filings_dir, cik, '10-K')
    if os.path.isdir(cik_path):
        for accession_folder in os.listdir(cik_path):
            filing_path = os.path.join(cik_path, accession_folder, 'primary-document.html')
            if os.path.isfile(filing_path):
                text = extract_text_from_html(filing_path).lower() 
                
                matches = re.findall(near_regex_pattern, text)
                
                sentiment_scores.append({
                    'CIK': cik,
                    'Accession_Number': accession_folder,
                    'Sentiment_Score': len(matches)
                })

import pandas as pd
Topic3_negative_sentiment_df = pd.DataFrame(sentiment_scores)

Topic3_negative_sentiment_df.to_csv('output/Topic3_negative_scores.csv', index=False)

print(Topic3_negative_sentiment_df.head())


**Datapoints about sentiments**

- How many words are in the LM positive dictionary?
   - 3594
- How many words are in the LM negative dictionary?
   - 24884
- How many words are in the ML positive dictionary?
   - 617
- How many words are in the ML negative dictionary?
   - 792

  
- A description of how you set up the near_regex function (partial = true or false, distance = what) and why you chose the values you did.
   - max_words_between=5:  I chose 5 because I feel it helps to capture phrases where the sentiment-related words are in close enough proximity that indicates a stronger relationship between the sentiment and the context it's used in.

   - partial=Fals: I wanted to ensure that the sentiments I capture are accurate and not confounded by similar-looking word fragments.


```python
 max_words_between=5, partial=False, cases_matter=False

**Why did you choose the three topics you did for the “contextual sentiment” measures?**

Reasoning for each topic:

1. **COVID**: has had widespread impact on lives, economies, and societies around the world. I thought finding sentiments around COVID can give insight about public health concerns, economic optimism/pessimism, and societal resilience.

2. **Ukraine**: focal point for international relations and has significant affect on economies. 

3. **Climate Change**: long-term, global issue affecting the environment, economies, and policies. Sentiment analysis here can shed light on how different groups perceive the urgency of the issue, the effectiveness of responses, and the impact of climate change on company returns

| Measure                            | Count | Mean      | Std       | Min       | 25%       | 50%       | 75%       | Max       |
|------------------------------------|-------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|
| CIK                                | 498   | 800107.1  | 558627.4  | 1800      | 98432     | 889042.5  | 1140778   | 1868275   |
| Word_Count                         | 498   | 74457.25  | 31600.44  | 21827     | 54558.75  | 69252.5   | 84634.25  | 284663    |
| ML_Positive_Sentiment_Ratio        | 495   | 0.02283   | 0.00349   | 0.00389   | 0.0208    | 0.02306   | 0.025     | 0.03581   |
| ML_Negative_Sentiment_Ratio        | 498   | 0.02462   | 0.0033    | 0.0036    | 0.022725  | 0.02475   | 0.026405  | 0.03693   |
| LM_Positive_Sentiment_Ratio        | 498   | 0.004749  | 0.001287  | 0.00053   | 0.003953  | 0.00465   | 0.00536   | 0.01054   |
| LM_Negative_Sentiment_Ratio        | 498   | 0.015112  | 0.003541  | 0.00276   | 0.012805  | 0.015005  | 0.017048  | 0.0284    |
| Topic1_positive_sentiment_ratio    | 498   | 0.00012   | 0.00009   | 0.0       | 0.00006   | 0.0001    | 0.00017   | 0.00076   |
| Topic1_negative_sentiment_ratio    | 498   | 0.000344  | 0.000216  | 0.0       | 0.00018   | 0.0003    | 0.00044   | 0.00129   |
| Topic2_positive_sentiment_ratio    | 498   | 0.000011  | 0.000021  | 0.0       | 0.0       | 0.0       | 0.00002   | 0.00019   |
| Topic2_negative_sentiment_ratio    | 501   | 0.000012  | 0.000022  | 0.0       | 0.0       | 0.0       | 0.000017  | 0.000192  |
| Topic3_positive_sentiment_ratio    | 498   | 0.000063  | 0.000088  | 0.0       | 0.00001   | 0.00004   | 0.00008   | 0.00068   |
| Topic3_negative_sentiment_ratio    | 498   | 0.00011   | 0.000135  | 0.0       | 0.00002   | 0.00007   | 0.00014   | 0.00092   |
| ret                                | 498   | 0.001244  | 0.03446   | -0.24278  | -0.016275 | -0.0011   | 0.016287  | 0.16214   |


**Do your “contextual sentiment” measures pass some basic smell tests?**
- Smell tests: Is something fishy? (What you look for depends on the setting.)
   - A lot of the conextual sentiment ratios are either 0 or very close to zero. Which is expected, but at least I was able to produce some numbers.
- Do you have variation in the measures (i.e a variable is not all the same value)?
   - Yes, there is varation
- Are industries the industries you expect talking about your subject positively or negatively?
   - Hard to say given relatively low ratios/sentiment scores.

**Are there any caveats about the sample and/or data?**

It's impoertant to note that certain firms didn't have a 10-K report. To normalize this, I just made their sentiment ratios equal to zero.

## 3. Results

The table below shows the correlation between each sentiment score ratio and the return from the filing date (version 1): 

| Metric                          | Value     |
|---------------------------------|-----------|
| ML_Positive_Sentiment_Ratio     | 0.047408  |
| ML_Negative_Sentiment_Ratio     | 0.040311  |
| LM_Positive_Sentiment_Ratio     | -0.079896 |
| LM_Negative_Sentiment_Ratio     | -0.000087 |
| Topic1_positive_sentiment_ratio | -0.060419 |
| Topic1_negative_sentiment_ratio | -0.060988 |
| Topic2_positive_sentiment_ratio | 0.024592  |
| Topic2_negative_sentiment_ratio | 0.013919  |
| Topic3_positive_sentiment_ratio | 0.098640  |
| Topic3_negative_sentiment_ratio | 0.107681  |


Below are scatterplots measuring returns to each sentiment score. I struggled to add them so I just used screenshots, hope thats ok.

**When I look on GH, the screenshots dont seem to show up, but I added them to a folder called screenshots to show that I was able to create the scatterplots, I just struggled for them to show up. Hopefully that isn't a huge deduction.**

![Screenshot Description](inputs/scat1.png)


![Screenshot Description](inputs/scat2.png)


![Screenshot Description](inputs/scat3.png)


**Four discussion topics:**

1. Compare / contrast the relationship between the returns variable and the two “LM Sentiment” variables (positive and negative) with the relationship between the returns variable and the two “ML Sentiment” variables (positive and negative). Focus on the patterns of the signs of the relationships and the magnitudes. *
      

Just from looking at the scatterpolots, the ratio for the ML variables seem to be more to the right, indicating higher ratios, but the return for the both of them don't seem to be too different. The ML variables seem to be grouped much closer together, while the LM seems to be much mroe spread out. This indicates that the LM variable has a larger range of sentiment scores for both positive and negative. I would say the ML variables have slightly higher returns.

  2. If your comparison/contrast conflicts with Table 3 of the Garcia, Hu, and Rohrer paper (ML_JFE.pdf, in the repo), discuss and brainstorm possible reasons why you think the results may differ. If your patterns agree, discuss why you think they bothered to include so many more firms and years and additional controls in their study? (It was more work than we did on this midterm, so why do it to get to the same point?)
        

It does contrast to the table. Specifically the LM returns, as in the table they appear to be negative for the LM psoitive, LM negative, and ML negative. While in my scatterplots, the returns fluctuate around zero, being both positive and negative. I don't believe that the results are incredibly different, but the reason for these scattered returns  may be due to other economic conditions that affect the actual returns of each firm at the time.

3. Discuss your 3 “contextual” sentiment measures. Do they have a relationship with returns that looks “different enough” from zero to investigate further? If so, make an economic argument for why sentiment in that context can be value relevant.
       

My 3 contextual sentiment measures were: COVID, Ukraine, and Climate change. I don't beleive that they have a relationship that could declare anything or be used for predictions. But I think topic 3, referring to climate change, has good data to show that referring to climate change in a negative conotation, seems to have some correlation with increased returns. Maybe the economy responds positively to companies having a good corporate social responsibility, and discussing the harsh affects of climate change.

 4. Is there a difference in the sign and magnitude? Speculate on why or why not.
