
# Mental Health in Technology Fields (OSMI Survey, 2016)
This data set was posted on [Kaggle.com](https://www.kaggle.com/) by Open Sourcing Mental Illness, LTD under a creative commons license which can be found [here](https://creativecommons.org/licenses/by-sa/4.0/). The primary goal of the data, as stated on the [page](https://www.kaggle.com/osmi/mental-health-in-tech-2016#mental-health-in-tech-2016-neo4j-20161114.zip) which this was uploaded too, is to use the data to raise awareness and to improve conditions for those with mental health disorders in the IT workplace.

*Disclosure* I, Corey Bryant, am not affiliated with this organization. All work here within is exploratory. All analyses will be conducted using Python; I will be using a Python package that I developed, *[researchpy](https://researchpy.readthedocs.io/en/latest/)*, for parts of this analysis.

**Loading the Data**


```python
import pandas as pd
import researchpy as rp
import numpy as np

# These are for running the model and conducting model diagnostics
import statsmodels.formula.api as smf
import statsmodels.stats.api as sms
from scipy import stats
from statsmodels.compat import lzip
```


```python
df = pd.read_csv("C:\\Users\\CoreySSD\\Google Drive\\Python for Data Science, LLC\\Data sets\\OSMI Mental Health in Tech Survey\\mental-heath-in-tech-2016.csv")

print(df.shape, "\n"*2, df.columns)
```

    (1433, 63) 
    
     Index(['Are you self-employed?',
           'How many employees does your company or organization have?',
           'Is your employer primarily a tech company/organization?',
           'Is your primary role within your company related to tech/IT?',
           'Does your employer provide mental health benefits as part of healthcare coverage?',
           'Do you know the options for mental health care available under your employer-provided coverage?',
           'Has your employer ever formally discussed mental health (for example, as part of a wellness campaign or other official communication)?',
           'Does your employer offer resources to learn more about mental health concerns and options for seeking help?',
           'Is your anonymity protected if you choose to take advantage of mental health or substance abuse treatment resources provided by your employer?',
           'If a mental health issue prompted you to request a medical leave from work, asking for that leave would be:',
           'Do you think that discussing a mental health disorder with your employer would have negative consequences?',
           'Do you think that discussing a physical health issue with your employer would have negative consequences?',
           'Would you feel comfortable discussing a mental health disorder with your coworkers?',
           'Would you feel comfortable discussing a mental health disorder with your direct supervisor(s)?',
           'Do you feel that your employer takes mental health as seriously as physical health?',
           'Have you heard of or observed negative consequences for co-workers who have been open about mental health issues in your workplace?',
           'Do you have medical coverage (private insurance or state-provided) which includes treatment of  mental health issues?',
           'Do you know local or online resources to seek help for a mental health disorder?',
           'If you have been diagnosed or treated for a mental health disorder, do you ever reveal this to clients or business contacts?',
           'If you have revealed a mental health issue to a client or business contact, do you believe this has impacted you negatively?',
           'If you have been diagnosed or treated for a mental health disorder, do you ever reveal this to coworkers or employees?',
           'If you have revealed a mental health issue to a coworker or employee, do you believe this has impacted you negatively?',
           'Do you believe your productivity is ever affected by a mental health issue?',
           'If yes, what percentage of your work time (time performing primary or secondary job functions) is affected by a mental health issue?',
           'Do you have previous employers?',
           'Have your previous employers provided mental health benefits?',
           'Were you aware of the options for mental health care provided by your previous employers?',
           'Did your previous employers ever formally discuss mental health (as part of a wellness campaign or other official communication)?',
           'Did your previous employers provide resources to learn more about mental health issues and how to seek help?',
           'Was your anonymity protected if you chose to take advantage of mental health or substance abuse treatment resources with previous employers?',
           'Do you think that discussing a mental health disorder with previous employers would have negative consequences?',
           'Do you think that discussing a physical health issue with previous employers would have negative consequences?',
           'Would you have been willing to discuss a mental health issue with your previous co-workers?',
           'Would you have been willing to discuss a mental health issue with your direct supervisor(s)?',
           'Did you feel that your previous employers took mental health as seriously as physical health?',
           'Did you hear of or observe negative consequences for co-workers with mental health issues in your previous workplaces?',
           'Would you be willing to bring up a physical health issue with a potential employer in an interview?',
           'Why or why not?',
           'Would you bring up a mental health issue with a potential employer in an interview?',
           'Why or why not?.1',
           'Do you feel that being identified as a person with a mental health issue would hurt your career?',
           'Do you think that team members/co-workers would view you more negatively if they knew you suffered from a mental health issue?',
           'How willing would you be to share with friends and family that you have a mental illness?',
           'Have you observed or experienced an unsupportive or badly handled response to a mental health issue in your current or previous workplace?',
           'Have your observations of how another individual who discussed a mental health disorder made you less likely to reveal a mental health issue yourself in your current workplace?',
           'Do you have a family history of mental illness?',
           'Have you had a mental health disorder in the past?',
           'Do you currently have a mental health disorder?',
           'If yes, what condition(s) have you been diagnosed with?',
           'If maybe, what condition(s) do you believe you have?',
           'Have you been diagnosed with a mental health condition by a medical professional?',
           'If so, what condition(s) were you diagnosed with?',
           'Have you ever sought treatment for a mental health issue from a mental health professional?',
           'If you have a mental health issue, do you feel that it interferes with your work when being treated effectively?',
           'If you have a mental health issue, do you feel that it interferes with your work when NOT being treated effectively?',
           'What is your age?', 'What is your gender?',
           'What country do you live in?',
           'What US state or territory do you live in?',
           'What country do you work in?',
           'What US state or territory do you work in?',
           'Which of the following best describes your work position?',
           'Do you work remotely?'],
          dtype='object')
    

There are 63 columns with a total of 1,433 observations in the data. The column titles are the wording used in the survey question - pretty long. I will shorten the variables of interests for cleanliness. Also, most of the responses are categorical, so some recoding will be required as I go along.

## Questions to be Answered
Reminder, the only insight I have to the questions are what is displayed above. I do not know if any scales are used within the survey, and I do not know if there are skip patterns present. Skip patterns could effect some statistical tests as I may be including individuals in the N that should not be there.

Since this is an exploratory analysis, I will provide some questions I know that I want to explore below, but I will also allow for data driven questions to come about and be answered as I go along. Each question will be a subsection in this document.

* General descriptives of the survey sample
    * Demographic information such as age, gender, country, number cases of mental health disorder (MHD), family history of MHD, and number of individuals working in IT field.

* Is there a relationship between having a MHD and family history of MHD?
    * There should be, most mental health disorders have a genetic component.
    
* Are those with a MHD more likely to talk to their supervisor than those without a MHD?
    * My hypothesis is that those with a MHD will be more reserved than those without a MHD

### Data Wrangling

#### Renaming columns


```python
df.rename(columns= {"What is your age?": "Age",
                   "What is your gender?": "Gender",
                   "Do you currently have a mental health disorder?": "Mental health disorder (MHD)",
                   "Do you have a family history of mental illness?": "Family history of MHD",
                   "What country do you work in?": "Country (Work)",
                   "Have you been diagnosed with a mental health condition by a medical professional?": "Diagnosed by medical professional",
                   "Is your employer primarily a tech company/organization?": "Employer primarily Tech",
                   "Would you feel comfortable discussing a mental health disorder with your coworkers?": "Comfortable talking about MHD w/co-workers",
                   "Would you feel comfortable discussing a mental health disorder with your direct supervisor(s)?": "Comfortable talking about MHD w/supervisor(s)",
                   "Do you feel that your employer takes mental health as seriously as physical health?": "Employer takes MHD as serious as PH"
                   }, inplace= True
         )
```

#### Cleaning the Data
This data is messy - which is good because it means it was uploaded in a pretty raw form. It's just going to take some time. I thought about having a seperate file to do the data cleaning, but decided to demonstrate everything in this document.

For gender, I have decided to collapse the response options into the categories of 'Male', 'Female', 'Other'. For individuals that responded that they transitioned from a gender to another, I classified them as the gender post-transition; all other gender identifications have been placed into the 'Other' category.


```python
def clean_gender(series):
    if type(series) is str:
        if series.upper().strip() in ['MALE', 'M', 'MAN', 'MALE (CIS)', 'MAIL', 'SEX IS MALE', 'MALR', 'MALE.', 'CIS MALE',
                                     'MALE (TRANS, FTM)', 'M|', 'CISDUDE', 'CIS MAN', 'DUDE',
                                     "I'm a man why didn't you make this a drop down question. You should of asked sex? And I would of answered yes please. Seriously how much text can this take?"]:
            return "Male" 
        
        elif series.upper().strip() in ['FEMALE', 'F', 'WOMAN', 'Transitioned, M2F', 'TRANSGENDER WOMAN',
                                       'FEMALE ASSIGNED AT BIRTH', 'CIS-WOMAN', 'GENDERQUEER WOMAN', 'FEMALE/WOMAN',
                                       'FM', 'CIS FEMALE', 'FEMALE (PROPS FOR MAKING THIS A FREEFORM FIELD, THOUGH)',
                                       'I IDENTIFY AS FEMALE.', 'CISGENDER FEMALE', 'FEM', 'MTF']:
            return "Female"
        
        else:
            return "Other"
    
    else:
        return series
    
df['Gender'] = df['Gender'].apply(clean_gender)
```


```python
def clean_country(series):
    if type(series) is str:
        if series.upper().strip() == 'UNITED STATES OF AMERICA':
            return 'USA'
        else:
            return 'Rest of the world'

df['Country (Inhabit)'] = df['What country do you live in?'].apply(clean_country)
```

## Descriptive Statistics of the Survey Sample


```python
df['Age'].describe()

print(df['Age'].describe(), "\n"*2,
      "Let's check for ages that could be considered as odd", "\n"*2, df['Age'][df['Age'] >= 80])
```

    count    1433.000000
    mean       34.286113
    std        11.290931
    min         3.000000
    25%        28.000000
    50%        33.000000
    75%        39.000000
    max       323.000000
    Name: Age, dtype: float64 
    
     Let's check for ages that could be considered as odd 
    
     372     99
    564    323
    Name: Age, dtype: int64
    

Well, there clearly are some incorrectly entered responses in this field - unless someone is a baby genius and the other has found the tree of life. I'm going to limit the analyses to individuals who are 19-80.


```python
df = df[(df['Age'] >= 19) & (df['Age'] <= 80)]

df['Age'].describe()
```




    count    1428.000000
    mean       34.086134
    std         8.086273
    min        19.000000
    25%        28.000000
    50%        33.000000
    75%        39.000000
    max        74.000000
    Name: Age, dtype: float64




```python
rp.summary_cat(df[['Gender', 'Mental health disorder (MHD)', 'Family history of MHD', 
                   'Country (Inhabit)', 'Employer primarily Tech', 
                   'Comfortable talking about MHD w/co-workers',
                   'Comfortable talking about MHD w/supervisor(s)',
                  'Employer takes MHD as serious as PH']])
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Variable</th>
      <th>Outcome</th>
      <th>Count</th>
      <th>Percent</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Gender</td>
      <td>Male</td>
      <td>1053</td>
      <td>73.89</td>
    </tr>
    <tr>
      <th>1</th>
      <td></td>
      <td>Female</td>
      <td>341</td>
      <td>23.93</td>
    </tr>
    <tr>
      <th>2</th>
      <td></td>
      <td>Other</td>
      <td>31</td>
      <td>2.18</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Mental health disorder (MHD)</td>
      <td>Yes</td>
      <td>574</td>
      <td>40.20</td>
    </tr>
    <tr>
      <th>4</th>
      <td></td>
      <td>No</td>
      <td>528</td>
      <td>36.97</td>
    </tr>
    <tr>
      <th>5</th>
      <td></td>
      <td>Maybe</td>
      <td>326</td>
      <td>22.83</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Family history of MHD</td>
      <td>Yes</td>
      <td>668</td>
      <td>46.78</td>
    </tr>
    <tr>
      <th>7</th>
      <td></td>
      <td>No</td>
      <td>486</td>
      <td>34.03</td>
    </tr>
    <tr>
      <th>8</th>
      <td></td>
      <td>I don't know</td>
      <td>274</td>
      <td>19.19</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Country (Inhabit)</td>
      <td>USA</td>
      <td>838</td>
      <td>58.68</td>
    </tr>
    <tr>
      <th>10</th>
      <td></td>
      <td>Rest of the world</td>
      <td>590</td>
      <td>41.32</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Employer primarily Tech</td>
      <td>1</td>
      <td>879</td>
      <td>76.97</td>
    </tr>
    <tr>
      <th>12</th>
      <td></td>
      <td>0</td>
      <td>263</td>
      <td>23.03</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Comfortable talking about MHD w/co-workers</td>
      <td>Maybe</td>
      <td>477</td>
      <td>41.77</td>
    </tr>
    <tr>
      <th>14</th>
      <td></td>
      <td>No</td>
      <td>391</td>
      <td>34.24</td>
    </tr>
    <tr>
      <th>15</th>
      <td></td>
      <td>Yes</td>
      <td>274</td>
      <td>23.99</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Comfortable talking about MHD w/supervisor(s)</td>
      <td>Yes</td>
      <td>425</td>
      <td>37.22</td>
    </tr>
    <tr>
      <th>17</th>
      <td></td>
      <td>Maybe</td>
      <td>381</td>
      <td>33.36</td>
    </tr>
    <tr>
      <th>18</th>
      <td></td>
      <td>No</td>
      <td>336</td>
      <td>29.42</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Employer takes MHD as serious as PH</td>
      <td>I don't know</td>
      <td>491</td>
      <td>42.99</td>
    </tr>
    <tr>
      <th>20</th>
      <td></td>
      <td>Yes</td>
      <td>349</td>
      <td>30.56</td>
    </tr>
    <tr>
      <th>21</th>
      <td></td>
      <td>No</td>
      <td>302</td>
      <td>26.44</td>
    </tr>
  </tbody>
</table>
</div>



### Summary of Descriptive Statistics

The majority of the sample is male, 73.89%, and lives in the United States of America, 58.68%. There are slightly higher more individuals who reported having a MHD compared to those that did not report having a MHD, (40.20% vs. 36.97% respectively) and slightly over a fifth of respondents stated they might have a MHD, 22.83%. Just under half, 46.78%, of the respondents reported having a family history of MHD with about a third, 34.03%, reporting no family history of MHD. I'm going to assume that a value of "1" indicates "Yes" and a value of "0" indicates "No"; with that, about three-quarters, 76.97%, of the respondents work at a company that is primarily in the technology field.

**MHD within the Workplace** <br>
Majority of the respondents feel that they don't know if their employer takes mental health as serious as physical health, 42.99%, with a little under a third feels that their employer does take mental health as serious as physical health. 37.22% feel comfortable with talking about MHD with their direct supervisor(s) and 23.99% feel comfortable with talking about it with co-workers.

## Relationship between MHD and Family History of MHD

Before the test for a relationship between self-reported cases of having a MHD and family history of MHD, I want to check the proportion of those that self-reported having a MHD and have self-reported being diagnosed by a medical professional.


```python
rp.crosstab(df['Diagnosed by medical professional'], 
            df['Mental health disorder (MHD)'], prop= 'row')
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="4" halign="left">Mental health disorder (MHD)</th>
    </tr>
    <tr>
      <th></th>
      <th>Maybe</th>
      <th>No</th>
      <th>Yes</th>
      <th>All</th>
    </tr>
    <tr>
      <th>Diagnosed by medical professional</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>No</th>
      <td>28.43</td>
      <td>62.61</td>
      <td>8.96</td>
      <td>100.0</td>
    </tr>
    <tr>
      <th>Yes</th>
      <td>17.23</td>
      <td>11.34</td>
      <td>71.43</td>
      <td>100.0</td>
    </tr>
    <tr>
      <th>All</th>
      <td>22.83</td>
      <td>36.97</td>
      <td>40.20</td>
      <td>100.0</td>
    </tr>
  </tbody>
</table>
</div>



Interestingly, 17.23% of those that have self-reported they have been diagnosed by a medical professional self-reported they might have a MHD and 8.96% of those that stated they have a MHD have not been diagnosed by a medical professional.

I'm going use the self-reported measure of having a MHD and limit the relationship test between those who did not respond with "Maybe" and "I don't know".


```python
crosstab, results = rp.crosstab(df['Family history of MHD'][df['Family history of MHD'] != "I don't know"], 
            df['Mental health disorder (MHD)'][df['Mental health disorder (MHD)'] != 'Maybe'], 
            test= 'chi-square', prop= 'cell')

print(crosstab, "\n"*2, results)
```

                          Mental health disorder (MHD)               
                                                    No    Yes     All
    Family history of MHD                                            
    No                                           32.58  10.11   42.69
    Yes                                          15.59  41.72   57.31
    All                                          48.17  51.83  100.00 
    
                     Chi-square test   results
    0  Pearson Chi-square ( 1.0) =   219.8647
    1                    p-value =     0.0000
    2               Cramer's phi =     0.4862
    

As expected, there is a strong relationship present between currently having a mental health disorder and having a family history of a mental health disorder, $\chi^2$(1) = 219.8647, p< 0.0001, $\phi$= 0.4862; 41.72% of the respondents who have a MHD have a family history of MHD.

## Work taking Mental Health as Serious as Physical Health

Again, I will be limiting these analysis to those that did not respond with "Maybe" to having a MHD.


```python
df['Employer takes MHD as serious as PH'].replace("I don't know", "DK", inplace= True)

crosstab, results = rp.crosstab(df['Employer takes MHD as serious as PH'], 
                                df['Mental health disorder (MHD)'][df['Mental health disorder (MHD)'] != 'Maybe'],
                                test= 'chi-square', prop= 'col')

print(crosstab.round(1), "\n"*2, results)
```

                                        Mental health disorder (MHD)              
                                                                  No    Yes    All
    Employer takes MHD as serious as PH                                           
    DK                                                          45.6   38.2   41.8
    No                                                          18.7   32.9   25.9
    Yes                                                         35.8   28.9   32.3
    All                                                        100.0  100.0  100.0 
    
                     Chi-square test  results
    0  Pearson Chi-square ( 2.0) =   23.4542
    1                    p-value =    0.0000
    2                 Cramer's V =    0.1624
    

There is a relationship between having a MHD and feeling that their employer takes mental health as serious as physical health, $\chi^2$(2)= 23.4542, p< 0.001, V= 0.1624. Since this is a 3x2 table, a post-hoc analysis will need to be conducted to further explore this. 

Normally I prefer to drop "Don't know" groups, but given that 42.99% of respondents stated they don't know if their employer takes mental health as serious as physical health, I will keep them in consideration - at least for now. With that, I will now test if there is a difference in the proportion of those who feel that their employer takes mental health as serious as physical health between those with and without a mental health disorder.


```python
rp.crosstab(df['Employer takes MHD as serious as PH'],
            df['Mental health disorder (MHD)'][df['Mental health disorder (MHD)'] != 'Maybe'])
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="3" halign="left">Mental health disorder (MHD)</th>
    </tr>
    <tr>
      <th></th>
      <th>No</th>
      <th>Yes</th>
      <th>All</th>
    </tr>
    <tr>
      <th>Employer takes MHD as serious as PH</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>DK</th>
      <td>200</td>
      <td>172</td>
      <td>372</td>
    </tr>
    <tr>
      <th>No</th>
      <td>82</td>
      <td>148</td>
      <td>230</td>
    </tr>
    <tr>
      <th>Yes</th>
      <td>157</td>
      <td>130</td>
      <td>287</td>
    </tr>
    <tr>
      <th>All</th>
      <td>439</td>
      <td>450</td>
      <td>889</td>
    </tr>
  </tbody>
</table>
</div>




```python
from statsmodels.stats.proportion import proportions_ztest

count = np.array([130, 157])
nobs = np.array([450, 439])

proportions_ztest(count, nobs)
```




    (-2.1916587789162105, 0.02840415427479463)



Compared to those without a mental health disorder, those with a mental health disorder are less likely to feel their employer takes mental health as serious as physical health, z= -2.19, p= 0.0284.

## Comfort in Discussing Mental Health Disorder at place of Employment

### Talking with Co-workers


```python
df['Comfortable talking to co-workers'] = df['Comfortable talking about MHD w/co-workers']

crosstab, results = rp.crosstab(df['Comfortable talking to co-workers'][df['Comfortable talking to co-workers'] != 'Maybe'],
                                df['Mental health disorder (MHD)'][df['Mental health disorder (MHD)'] != 'Maybe'],
                                test= 'chi-square', prop= 'col')

print(crosstab.round(1), "\n"*2, results)
```

                                      Mental health disorder (MHD)              
                                                                No    Yes    All
    Comfortable talking to co-workers                                           
    No                                                        52.9   57.5   55.2
    Yes                                                       47.1   42.5   44.8
    All                                                      100.0  100.0  100.0 
    
                     Chi-square test  results
    0  Pearson Chi-square ( 1.0) =    1.1343
    1                    p-value =    0.2869
    2               Cramer's phi =    0.0465
    

No relationship is present between having a MHD and feeling comfortable enough to talk to co-workers about it, $\chi^2$(1)= 1.1343, p= 0.2869.

### Talking with Supervisor


```python
df['Comfortable talking to supervisor'] = df['Comfortable talking about MHD w/supervisor(s)']

crosstab, results = rp.crosstab(df['Comfortable talking to supervisor'][df['Comfortable talking to supervisor'] != 'Maybe'],
                                df['Mental health disorder (MHD)'][df['Mental health disorder (MHD)'] != 'Maybe'],
                                test= 'chi-square', prop= 'col')

print(crosstab.round(1), "\n"*2, results)
```

                                      Mental health disorder (MHD)              
                                                                No    Yes    All
    Comfortable talking to supervisor                                           
    No                                                        38.9   46.0   42.6
    Yes                                                       61.1   54.0   57.4
    All                                                      100.0  100.0  100.0 
    
                     Chi-square test  results
    0  Pearson Chi-square ( 1.0) =    3.0874
    1                    p-value =    0.0789
    2               Cramer's phi =    0.0715
    

No statistically significant relationship is present between having a MHD and feeling comfortable enough to talk to a supervisor about it, $\chi^2$(1)= 3.0874, p= 0.0789. Since there appears to be some relationship present, there is a 7% difference between those with and without a MHD, I want to explore this further in a multivariate model.

Due to the requirements of variable names using patsy, I have to make the variable names have no spaces. During this time, I will also be removing "Don't know", "Other", and "Maybe" responses from co-variates in my model.


```python
df['MH_PH'] = df['Employer takes MHD as serious as PH'].replace({"DK": np.nan})
df['MHD'] = df['Mental health disorder (MHD)'].replace({"Maybe": np.nan})
df['Emp_in_tech'] = df['Employer primarily Tech']
df['Emp_size'] = df['How many employees does your company or organization have?']
df['Emp_MH_benefits'] = df['Does your employer provide mental health benefits as part of healthcare coverage?'].replace({"I don't know": np.nan,
                                                                                                                        "Not eligible for coverage / N/A": np.nan})
df['Talk_to_sup'] = df['Comfortable talking about MHD w/supervisor(s)'].replace({"Yes": 1, "No": 0, "Maybe": np.nan})
df['Talk_to_coworkers'] = df['Comfortable talking about MHD w/co-workers'].replace({"Maybe": np.nan})
df['Gender2'] = df["Gender"].replace('Other', np.nan) # Dropping group due to low sample size

model = smf.logit('Talk_to_sup ~ C(MHD) + C(MH_PH) + C(Emp_in_tech) + C(Gender2) + Age + C(Emp_size) + C(Emp_MH_benefits)', data= df).fit(maxiter= 3000)


# Coefficients in logistic regression models are not that intuitive to interprete. I will convert them to odds ratios so it will be easier.
model_odds = pd.DataFrame(np.exp(model.params), columns= ['aOR']) # Converting coef. to odds ratios for easier interpretation
model_odds['aOR'] = model_odds['aOR'].round(4)
model_odds['p-value']= model.pvalues
model_odds['p-value'] = model_odds['p-value'].round(4)
model_odds[['2.5%', '97.5%']] = np.exp(model.conf_int())

print("\n", f"Number of observations= {model.nobs}", "\n",
      f"F({model.df_model: .0f},{model.df_resid: .0f})", "\n",
      f"Log-likelihood p-value = {model.llr_pvalue: .4f}", "\n",
      f"Pseudo R-squared= {model.prsquared: .4f}", "\n"*3, 
     model_odds)
```

    Optimization terminated successfully.
             Current function value: 0.441062
             Iterations 6
    
     Number of observations= 259 
     F( 11, 247) 
     Log-likelihood p-value =  0.0000 
     Pseudo R-squared=  0.3118 
    
    
                                        aOR  p-value      2.5%      97.5%
    Intercept                       0.4034   0.4582  0.036662   4.439455
    C(MHD)[T.Yes]                   0.8492   0.6458  0.422892   1.705223
    C(MH_PH)[T.Yes]                18.3860   0.0000  8.855960  38.171411
    C(Emp_in_tech)[T.1.0]           1.1018   0.8203  0.477364   2.542991
    C(Gender2)[T.Male]              1.1834   0.6742  0.539686   2.595103
    C(Emp_size)[T.100-500]          1.1181   0.9072  0.171205   7.301672
    C(Emp_size)[T.26-100]           0.6233   0.6059  0.103478   3.755012
    C(Emp_size)[T.500-1000]         0.3780   0.3800  0.043083   3.316590
    C(Emp_size)[T.6-25]             0.4320   0.3674  0.069623   2.679937
    C(Emp_size)[T.More than 1000]   0.3732   0.2834  0.061634   2.259862
    C(Emp_MH_benefits)[T.Yes]       1.3458   0.4671  0.604451   2.996437
    Age                             1.0155   0.4412  0.976530   1.056014
    

The current model accounts for approximately 31.18% of the variance associated with feeling comfortable enough to talk to the direct supervisor(s) about a mental health disorder. The only significant predictor is feeling that the employer takes mental health as serious as physical health, aOR= 18.39 (8.9, 38.17), p< 0.0001.

I did some exploration with this variable earlier, but given this result, I want to explore it further. However for now, I am taking a break from this data and am going to eat some delicious gallahba.

## What makes Exmployees feel that Work takes Mental Health as Serious as Physical Health

Again, I will be limiting these analysis to those that did not respond with "Maybe" to having a MHD. When I continue!


```python

```
