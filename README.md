# leibniz: Smartphone Sensing Panel Study (aggregated data)

Daily smartphone-sensing data and well-being and personality questionnaires from **553 adults in Germany**, recorded over **14 days** of everyday life. There are **7,635 participant-days** in total.

The data come from the **Smartphone Sensing Panel Study (SSPS)**, run by Ludwig-Maximilians-Universität München (LMU Munich) with the Leibniz Institute for Psychology. They were published alongside this paper:

> Zhu N, Schoedel R, Sust L, Bühner M, Terhorst Y. *Digital Phenotyping of Lifestyle Profiles and Mental Well-Being in German Adults: Prospective Longitudinal Cohort Study.* Journal of Medical Internet Research. 2026;28:e101850. https://doi.org/10.2196/101850

The preprint of the same paper was titled *"Can Digital Phenotyping Reveal Lifestyles That Reflect Mental Well-Being? Results From a Population-Matched Sample."* The dataset was released under that name, and that's the title used in the dataset description below.

## What's in this folder

| File | What it holds | Rows |
|---|---|---|
| `Sensing_Variables.csv` | One row per participant per day: 10 daily features from the phone's sensors and app logs | 7,635 |
| `Person_Variables.csv` | One row per participant: answers to a personality questionnaire (15 items) and a mental well-being questionnaire (14 items) | 553 |
| `Codebook.csv` | Short descriptions of every column in the two files above | — |

The two data files join on `user_id`. All 553 participants appear in both files.

## The study

These details come from the [JMIR article](https://www.jmir.org/2026/1/e101850):

- **Who:** 553 adults (mean age 42.1, 44.65% female), drawn from a sample recruited according to quotas designed to reflect the German population.
- **When:** the wider study collected data between May and November 2020. This dataset covers a 14-day window per person.
- **How:** participants installed *PhoneStudy*, an Android app made at LMU Munich, on their own phones. It recorded app use, GPS, activity recognition, ambient light, microphone loudness and screen state in the background. Questionnaires were filled in online, in validated German versions.
- **Which days were kept:** each participant has at least 8 valid days in the 14-day window. 39 participants have fewer than 14.

The dataset description says that age, gender, education, phone manufacturer and Android version were removed for privacy reasons, but can be made available on request. The raw sensor logs are not public.

## `Sensing_Variables.csv`

Each row is one participant-day. The data has no date column, so a participant's rows can't be matched to particular calendar days or days of the week. Definitions are from the article (Table 1 and Methods).

| Column | Feature | Definition in the article |
|---|---|---|
| `user_id` | Participant | Pseudonymous ID, recoded |
| `walking` | Walking probability | Daily average probability of the "walking" state, from Android activity recognition |
| `bicycle` | On-bicycle probability | Daily average probability of the "on bicycle" state |
| `distance` | Travel distance | Total distance between consecutive GPS coordinates |
| `gyratio` | Radius of gyration | Root-mean-square distance from each significant place to the participant's overall centre point, i.e. how spread out their movements were |
| `loudness` | Loudness | Average decibel level of the surroundings, from the microphone |
| `brightness` | Brightness | Average ambient light level |
| `communication_dur` | Communication app use | Total time in communication apps |
| `social_dur` | Social media app use | Total time in social media apps |
| `screen_duration` | Screen-on duration | Total time the screen was on |
| `Screen_frequency` | Screen session count | Number of separate times the screen was switched on |

**The values are not in original units.** According to the article, 8 of the 10 features were Box-Cox transformed because they were highly skewed, and then all 10 were z-scored. So a value of `0` means average and `1` means one standard deviation above average. You can't recover minutes, metres or decibels from this file.

From my own check of the file: each column has mean 0 and standard deviation 1 across all participant-days once `-99` values are removed. Participants' own averages still differ from each other. So the standardisation was done across the whole sample, not within each person.

### Missing values are coded as `-99`

Every cell is filled in, but `-99` means missing. Treat it as missing before doing any analysis; otherwise it will badly skew means and correlations. These are my own counts from the file:

| Column | `-99` values | Share of 7,635 days |
|---|---|---|
| `gyratio` | 1,514 | 19.8% |
| `distance` | 1,329 | 17.4% |
| `brightness` | 436 | 5.7% |
| `loudness` | 106 | 1.4% |
| all other columns | 0 | 0% |

The article reports that missing travel distance and radius of gyration were related to other observed variables, so they are probably not missing at random. Gaps in the other features were assumed to come from temporary device or app faults.

## `Person_Variables.csv`

One row per participant, with the answer to each questionnaire item. Every answer is a whole number from 1 to 5, and nothing is missing.

| Columns | Questionnaire | Items |
|---|---|---|
| `BFI_2XS_E1`–`E3` | Big Five Inventory-2, extra-short form (BFI-2-XS): **Extraversion** | 3 |
| `BFI_2XS_O1`–`O3` | BFI-2-XS: **Open-mindedness** | 3 |
| `BFI_2XS_C1`–`C3` | BFI-2-XS: **Conscientiousness** | 3 |
| `BFI_2XS_A1`–`A3` | BFI-2-XS: **Agreeableness** | 3 |
| `BFI_2XS_N1`–`N3` | BFI-2-XS: **Negative emotionality** | 3 |
| `WEMWBS_1`–`WEMWBS_14` | Warwick–Edinburgh Mental Well-Being Scale (WEMWBS) | 14 |

Notes on scoring:

- **Errors in the codebook:** `Codebook.csv` labels the C items "Consciousness". The trait is Conscientiousness. It also calls the O items "Openness", which this version of the questionnaire names Open-mindedness.
- **BFI-2-XS:** some items are worded in the opposite direction and need flipping (6 minus the answer) before you add up a trait score. The codebook doesn't say which ones. Check the BFI-2-XS scoring key (Soto & John, 2017) before scoring. I haven't confirmed whether the values in this file are already flipped.
- **WEMWBS:** the total is the sum of the 14 items, so it can range from 14 to 70. In this file, totals run from 16 to 70, with a mean of 49.0 (my own calculation).

## Things to know before using it

- **Each row in the sensing file is a day, not a person.** Most participants have 14 rows and some have as few as 8. If you link sensing data to the questionnaires, average each person's days first, or use a model that handles repeated days per person.
- **Recode `-99` first.** See above.
- **Not comparable with raw device data.** The transform and z-scoring mean you can't directly compare these values with sensor data you collect yourself.
- **Android only, Germany only, 2020.** The PhoneStudy app runs only on Android. Data were collected in 2020, during the COVID-19 pandemic, which may have changed how much people moved around and used their phones. That last point is my inference, not a claim from the article.

## Source, code and citation

- **Article:** https://www.jmir.org/2026/1/e101850 (open access, CC BY 4.0)
- **Analysis code and other materials:** https://osf.io/pzksa
- **Original data source:** the article says the aggregated dataset is published on PsychArchives, the Leibniz Institute for Psychology's repository. I couldn't find the dataset's own record or DOI there. Add it here once you have it.

When using the data, cite the article above, plus the dataset itself once you have its PsychArchives reference.
