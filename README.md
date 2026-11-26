# Filipino Well-Being Survey Analysis in R

Capstone project for the **Data Analytics Webinar Using R** by DICT Region 1 ICT Academy (November 23 to 25, 2026). Certificate: to follow.

I cleaned a messy survey dataset of 2,000 respondents from the Ilocos Region, then used charts, hypothesis tests and regression in R to look at stress, salaries and what predicts a respondent's happiness score.

**Tools:** R, R Markdown, ggplot2 (base R for cleaning and statistics)

**Files:**
- `DAW_Capstone_Sobreviñas_MarvicKaizz.Rmd`: the full analysis, with code and my reasoning at each step
- `DAW_Capstone_Sobreviñas_MarvicKaizz.pdf`: the knitted report with all outputs and charts

## About the data

The dataset was created by the training's trainer for practice, so it is synthetic, not a real survey. It is available on Kaggle: KAGGLE_LINK. It has 22 columns covering demographics (age, sex, marital status, province), body measurements, daily hours spent on work, sleep and personal activities, monthly salary, education, home and car ownership, a Perceived Stress Scale score and a Happiness Index.

Since the data is synthetic, the findings below describe this dataset only and shouldn't be read as facts about the Ilocos Region.

## Data cleaning

The data had the kinds of problems real survey data has, and I followed one rule throughout: values whose true answer can't be known are **set aside**, not guessed. I added a `valid` flag instead of deleting rows, so the original data stays intact.

- **Data types:** checked every column with `str()`. Numeric columns were already stored as numbers.
- **Impossible values:** ages ranged from 0 to 147 and weights went as low as 2.1 kg. I set aside ages below 16 or above 100 (the data includes work hours, so respondents should be of working age) and weights below 30 kg.
- **Inconsistent categories:** sex, marital status and province were written many different ways, such as "FEMALE", " female" and "F", "Sgl." and "Singl", or "ElYu" and "L.U.". I trimmed spaces, lowercased the text and mapped each variant to its proper category, then converted the columns to factors.
- **Missing values:** most columns were missing under 1.3%, so those incomplete rows were set aside. Five columns (salary, education, stress score, house and car ownership) were each missing about 36%. Checking the rows showed that 727 respondents had skipped all five, which points to an unanswered section of the survey rather than random gaps. Imputing over a third of these values would mean guessing, so these rows were set aside too.

**Result:** 1,096 of 2,000 responses (55%) were complete and valid for analysis.

## Analysis and findings

| Task | Method | Finding |
|---|---|---|
| BMI | Calculated from height and weight | Median BMI of 21.5 |
| Stress levels | Grouped Perceived Stress Scale scores into five levels | 92% of respondents fell in the Normal range |
| Work-life balance | Standard deviation of each respondent's work, sleep and personal hours (`Time_Imbalance`) | Used as a predictor in the regression |
| Sex | Pie chart | 51.5% female, 48.5% male |
| Stress by province | Stacked bar chart | Normal stress dominated in all four provinces |
| Age | Histogram with 5-year groups | Most respondents were in their 20s, with a long tail of older respondents |
| Salary | Boxplot | Right-skewed: median PHP 17,637, mean PHP 21,187, with 36 high earners above about PHP 56,000 |
| Education and number of children | Chi-square test (children grouped as 0, 1, 2, 3 and 4+ so every expected count was at least 5) | No significant association, χ²(8) = 11.41, p = 0.180 |
| Salary across provinces | Kruskal-Wallis test, chosen because the ANOVA residuals weren't normal (Shapiro-Wilk p < 0.001) | No significant difference, H(3) = 0.39, p = 0.943 |
| BMI and happiness | Simple linear regression | BMI did not predict happiness (p = 0.945) |

### What predicts happiness?

A multiple linear regression with six numeric predictors was significant, F(6, 1089) = 119.6, p < 0.001, and explained 39% of the variation in the Happiness Index (adjusted R² = 0.394). I first checked that no two predictors were strongly correlated (all |r| ≤ 0.37).

- **Higher happiness:** higher salary, older age and more children
- **Lower happiness:** higher stress and a more uneven split of time between work, sleep and personal activities
- **No effect:** BMI

The diagnostic plots showed no clear violations of the regression assumptions. The residuals departed slightly from normality (Shapiro-Wilk W = 0.984), but with over 1,000 observations this is unlikely to affect the results.

## What I'd do next

- Compare the respondents who skipped the survey section with those who answered it, to check whether setting them aside changed the sample
- Add the categorical predictors (education, home and car ownership) to the regression
