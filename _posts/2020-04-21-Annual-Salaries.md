---
layout: post
title: Linear Regression Model Predicts Salaries on 7 Key Features
---
# The Origin Story

I don't think I'm the only upper-middle-class, college-educated millenial who has suffered the pains of career choice paralysis. Many options, little guidance, sparse work experience. The endless cheerleading of a family and culture that encourages "finding your own path" while simultaneously expecting that you land somewhere on the upper end of the immensely unequal compensation spectrum that values certain types of work, industries and job functions over others.

In this post, I'll spare you the details of my own job-seeking/soul-searching story and get to the point:
<div class="message">
Somewhere along the way, interspersed with personality tests, informational interviews and career coaching sessions, a couple people suggested I use O*net Online to browse occupations.
<a href='https://www.onetonline.org'>O*net</a> is a government database that provides detailed information about almost 1,000 occupations. The data it stores about those occupations formed the basis for this project.
</div>

# So what factors influence annual salary for American jobs?
The process I used to help answer this question is outlined below.

## 1. Data Scraping
I defined functions to parse the descriptors, number of elements and numerical features from each occupations' webpage and used BeautifulSoup to loop through job codes appended the extracted information to a dictionary for each page.

## 2. Data Cleaning & Initial Feature Selection

<strong>Cleaning Process</strong><br>
- Ordinal variables were set to 0 for all NaN values because they usually only have 1-4 possible attributes ranked out of 6 possible. Therefore the missing values should be 0 and not excluded.
- After looking at the distributions, most of these variables looked pretty thin, so I transformed many of them to simple 1/0 boolan dummy variables
- Annual Salary and Hourly Wage were nearly perfectly collinear. Hourly Wages had more missing values, so I dropped that column and chose Annual Salary as my target variable.
- I log transformed the target variable due to right-skew. I also removed outliers.

<strong>Initial Feature Selection</strong><br>
I iterated through a few pair plots and variable transformations. Ultimately <em>I used the pairplot to choose the top 12 variables to carry forward</em> into the analysis phase. This number was arbitrary. It represented all the variables that I had reason to believe were a) not collinear with another feature and b) had some non-random correlation with the transformed target variable.

## 3. Model Development
I used StatsModels linear regression to fit a training subsection of my data. The initial R2 and Ajusted R2 were pretty strong with relatively low F-statistic, AIC, BIC, etc so I focused on the P-values of the coefficients. One by one, I removed insignificant (P > 0.05) features. When I got down to 7 features, my model seemed to be as strong as it could be. 

I used cross-validation to compare the simple linear regression model to an Elastic Net model. The results were nearly identical, with slightly poorer results for the Elastic Net.

## 4. Results & Interpretation

<strong>The Numbers</strong><br>
The following table shows the features and resulting coefficients of the linear regression model predicting Annual Salary

> Adj R2 for Model: 0.67

<table>
  <thead>
    <tr>
      <th>Feature</th>
      <th>Raw Coefficient</th>
      <th>Transformed Coefficient</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Relationships</td>
      <td>-0.156</td>
      <td>-11.612%</td>
    </tr>
    <tr>
      <td>Achievement</td>
      <td>-0.114</td>
      <td>-9.347%</td>
    </tr>
    <tr>
      <td>Independence</td>
      <td>-0.103</td>
      <td>-9.229%</td>
    </tr>
       <tr>
      <td>Support</td>
      <td>-0.105</td>
      <td>-7.744%</td>
    </tr>
    <tr>
      <td>Social</td>
      <td>-0.033</td>
      <td>-7.402%</td>
    </tr>
    <tr>
      <td>Number Skills Listed</td>
      <td>0.151</td>
      <td>2.348%</td>
    </tr>
    <tr>
      <td>Job Zone</td>
      <td>0.207</td>
      <td>19.998%</td>
    </tr>
  </tbody>
</table>

<strong>The Meaning Behind it All?</strong><br>
Job Zone, the feature with by far the highest positive coefficient, is an <a href='https://www.onetonline.org/help/online/zones'>O*net ranked scale</a> from 1-5 that identifies the amount of education, training, and experience needed for a given occupation. 

It turns out that this model mostly reinforces traditional knowledge that to earn more in your job you need experience & education above all.

It also says, for an extra boost, add a few skills to your repertoire (no matter what they are!)

<strong>But wait - there's more</strong><br>
There is a lot of future work to be done to better understand this model. One major model assumption ought to be tested, namely, that the occupations listed in the O*net database are a representative (if not comphrensive) sample of all possible occupations in the U.S.

Furthermore, understanding the payoff of financial and temporal investments in building the education and experience needed for higher Job Zones would be a valuable addition. Indeed, understanding the salary arc for different career paths may be more indicative of lifetime earning potential than end-of-career salaries.

I would also like to segment by industry, job type, or even job zone to better understand the data and its implications.

Overall, this model provides some interpretable evidence to suggest that job-seekers, career-changers, and young adults focus on building skills, education and experience as much as they can if they want the highest chances of landing a well-paying job. 
