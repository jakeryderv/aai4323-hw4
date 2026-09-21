# Assignment 4: PII, HIPAA, and Privacy

DSA5970 Assignment 4
Jake Van Slyke
jakev@ou.edu
2026-09-21

## 1. Employing the Safe Harbor Method

> You are given a file of patient data including PII, demographic information, and diagnosis codes from visits to a clinic. This file is `random_patient_data.tsv`, a tab delimited file. You want to pass this file to a researcher who will study it for relationships between demographics and illness. It is important that you preserve the ability to keep the relationships between the demographic information and the diagnosis codes intact. You also want to ensure that each row can be uniquely identified. However, you need to redact the dataset so no information that can identify the individual in each row is passed to the researcher. You decide to use the Safe Harbor method to redact the file.

> ### 1(a). Which fields in the dataset have to be redacted? (9 pts)

**Answer**

Eight fields require removal or generalization: `SSN`, `PhoneNumber`, `Name`, `StreetAddress`, `City`, `Zipcode`, `AccountNumber`, and `Birthdate`.

I would retain `State`, `Race`, `Employment`, `Income`, `Education`, and `Diagnosis1` through `Diagnosis3`. Here, `Employment` contains employment status, not employer names. Retention assumes no actual knowledge that the remaining information identifies someone. [HHS Safe Harbor guidance](https://www.hhs.gov/hipaa/for-professionals/special-topics/de-identification/index.html).

> ### 1(b). Describe how you will redact each field. (10 pts)

**Answer**

| Field(s) | Redaction method |
| --- | --- |
| `SSN`, `PhoneNumber`, `Name`, `StreetAddress`, `City`, `AccountNumber` | Remove these columns entirely from the research copy. |
| `Zipcode` | Remove the entire ZIP code; retain `State` for geographic comparisons. |
| `Birthdate` | Replace with birth year only. For anyone over 89, suppress the identifying birth year and use an age category of 90 or older. |

The birth years in this dataset range from 1950 to 2005, so no records currently need the 90-or-older category.

I would assign each row a unique random `RecordID` unrelated to personal information. Any re-identification key would remain private and separate from the research file. These choices follow the [HHS Safe Harbor and record-identification requirements](https://www.hhs.gov/hipaa/for-professionals/special-topics/de-identification/index.html).

I would keep the retained demographics and diagnosis codes together in their original rows, preserving their relationships for analysis.

## 2. Re-identifying Data

> It's possible to use different combinations of data from different files to determine the identity of otherwise unknown individuals. In this case, you will play a logic puzzle to properly match each individual to his or her disease.
>
> ### 2(a). The Setup
>
> Five individuals - Alex, Ben, Chloe, David, and Emily - each have a unique age (30, 35, 40, 45, 50) and a unique disease (Asthma, Diabetes, Gout, Hypertension, Migraines). Your task is to match each person to their correct age and disease.
>
> ### 2(b). The Clues
>
> 1. Ben is exactly 10 years older than the person with Gout.
> 2. The person with Diabetes is younger than Chloe, but older than the person who is 30.
> 3. Emily is 45, but she does not have Asthma or Migraines.
> 4. Alex is not 50 and does not have Hypertension.
> 5. David is older than the person with Asthma, but younger than the person with Migraines.
> 6. The 30-year-old does not have Hypertension.
> 7. Chloe is not 35 and does not have Diabetes.
>
> ### 2(c). Match each individual to his or her age and disease.
>
> Each correct individual/age/disease combination is worth 5 points for a total of 25 points.

**Answer**

| Individual | Age | Disease |
| --- | --- | --- |
| Alex | 30 | Asthma |
| Ben | 50 | Migraines |
| Chloe | 40 | Gout |
| David | 35 | Diabetes |
| Emily | 45 | Hypertension |

This assignment satisfies all seven clues. Ben (50) is exactly 10 years older than Chloe (40), who has Gout. David (35), who has Diabetes, is older than the 30-year-old but younger than Chloe. Emily is 45 and has neither Asthma nor Migraines. Alex is 30 and has Asthma, so he is neither 50 nor the person with Hypertension. David (35) is older than Alex (30), who has Asthma, and younger than Ben (50), who has Migraines. The 30-year-old does not have Hypertension, and Chloe is neither 35 nor the person with Diabetes.

## 3. Re-identification with Probability

> In this problem, you will determine the probability that customers have had COVID based upon data that doesn't directly state their COVID history.
>
> ### 3(a). Calculate the probability that each customer has had COVID. (21 points total)
>
> There are three individuals and three zipcodes as described in the tables below:
>
> **Percent of Population That Has Had COVID**
>
> | Zip Code | Residents Under 50 | Residents 50 and Older |
> | --- | --- | --- |
> | 97130 | 35% | 25% |
> | 97141 | 25% | 15% |
> | 97150 | 15% | 10% |
>
> **Probability of Living in Zip Code**
>
> | Customer | 97130 | 97141 | 97150 | Age |
> | --- | --- | --- | --- | --- |
> | Amy | 0.62 | 0.28 | 0.20 | 27 |
> | Bill | 0.15 | 0.55 | 0.30 | 52 |
> | Charlotte | 0.20 | 0.10 | 0.70 | 71 |
>
> **Show your work - if you don't show your work, you receive no points.** Hint: you will use conditional probabilities to determine the outcome.

**Answer**

Using the assignment's model, I multiply the probability of living in each ZIP code by the COVID rate for that ZIP code and the customer's age group, then add the three contributions:

$$
P(\text{COVID} \mid \text{customer})
= \sum_{z \in \{97130,\,97141,\,97150\}}
P(Z=z \mid \text{customer})\,P(\text{COVID} \mid Z=z,\,\text{age group})
$$

#### Amy

Amy is 27, so I use the rates for residents under 50:

$$
\begin{aligned}
P(\text{COVID} \mid \text{Amy})
&= (0.62 \times 0.35) + (0.28 \times 0.25) + (0.20 \times 0.15) \\
&= 0.217 + 0.070 + 0.030 \\
&= 0.317 = 31.7\%
\end{aligned}
$$

**Answer: 31.7%**

Note: Amy's ZIP probabilities in the table sum to more than 1:

$$
0.62 + 0.28 + 0.20 = 1.10
$$

so they are not a valid probability distribution as given. If her row is normalized so it sums to 1, the estimate becomes:

$$
\frac{0.317}{1.10} \approx 0.288 = 28.8\%
$$

I report 31.7% using the values as given, with 28.8% as the estimate after correcting for the table inconsistency.

#### Bill

Bill is 52, so I use the rates for residents 50 and older:

$$
\begin{aligned}
P(\text{COVID} \mid \text{Bill})
&= (0.15 \times 0.25) + (0.55 \times 0.15) + (0.30 \times 0.10) \\
&= 0.0375 + 0.0825 + 0.0300 \\
&= 0.1500 = 15\%
\end{aligned}
$$

#### Charlotte

Charlotte is 71, so I use the rates for residents 50 and older:

$$
\begin{aligned}
P(\text{COVID} \mid \text{Charlotte})
&= (0.20 \times 0.25) + (0.10 \times 0.15) + (0.70 \times 0.10) \\
&= 0.050 + 0.015 + 0.070 \\
&= 0.135 = 13.5\%
\end{aligned}
$$
