## About Me

Hello! My name is Cillian Fisher.

I am currently pursuing my Bachelor of Science (BS) in Finance at Lehigh University’s School of Business, finishing my undergrad in the Spring of 2024. I am highly motivated with excellent communication and organization skills and a passion for business. Additionally, I have strong decision making and analytical skills and am excited to showcase them in my future career.

I plan to pursue a 1 year MS in Business Analytics at Lehigh University after completing my bachelors degree this year. By doing so, I'll be able to enhance my understanding of analytical tools and their application to the business world.

<!-- Upload your own photo and change the path -->

<p style="text-align:center;">
  <img class="img-circle" src="/images/1714330393277.jpeg" width="50%">
</p>


---

## Portfolio

<!-- You can link to other websites, PDFs in this repo, and other pages in this repo -->

_**[Data Science Project Using 10-k Filings](report.md)**_


In this project, I analyze the sentiment of S&P 500 companies' 10-K filings to predict stock price movements. By quantifying positive and negative sentiments, I explore their correlation with market performance. This approach aims to offer investors a novel tool for informed decision-making based on textual analysis of corporate disclosures.

I began by collecting the S&P 500 company data as well as 10-K filings

```python
sp500_file = 'inputs/sp500_2022.csv'
if not os.path.exists(sp500_file):
    url = 'https://en.wikipedia.org/w/index.php?title=List_of_S%26P_500_companies&oldid=1130173030'
    pd.read_html(url)[0].to_csv(sp500_file, index=False)
sp500 = pd.read_csv('inputs/sp500_2022.csv')
```

For sentiment analysis, I chose 3 topics of interest  to match specific financial sentiment terms within the filings. Here I was aiming to quantify positive and negative sentiments:

```python
sec_filings_dir = '10k_files/sec-edgar-filings'
topic3_regex = r'(climate change|global warming|carbon emissions|...'
formatted_BHR_negative_regex = [...]
near_regex_pattern = re.compile([...])
```

I merged date and ticker with sentiment scores, matching them to returns on their filing date:

```python
dates_df = pd.read_csv('output/correct_dates.csv')
sp500_df = pd.read_csv('sp500_2022.csv')
merged_df = pd.merge(dates_df, sp500_df[['CIK', 'Symbol']], on='CIK', how='left')
merged_df.to_csv('output/tickers_dates.csv', index=False)
```

The screenshots below show the correlation between several positive and negative sentiment dictionaries and 10-k filings.


<img src="images/scat1.png?raw=true"/>

---

_**[First Python Assignment](asgn01exercises.md)**_

I wanted to use this first assignment as a mark of my progression in just 3-4 months of coding. Having going from very straighforward code such as printing 'hello world', to a full on data sciecne project that I showcased above, it highlights both the effort and willingless I have to improve my coding abilities.

<img src="images/asgn01.png?raw=true"/>


---

## Career Objectives

Based on my time at Lehigh as well as my experience interning at Northwestern Mutual, I have a general idea of what I'd like to do in my career. This Summer, I'll be interning at a startup at the NASDAq Center in San Francisco, where I'll be working as a brand equity intern at a startup named 'The AutoNinjas'. 

I believe that through my past experiences as well as the experiences I am going to have, I'm capable of being in a role that combines a financial role with an emphasis on business analytics. 

---

## Hobbies

Some of my hobbies include:
- Golf
- Travel
- Music
- Drawing
  
---
<p style="font-size:11px">Page template forked from <a href="https://github.com/evanca/quick-portfolio">evanca</a></p>
<!-- Remove above link if you don't want to attibute -->
