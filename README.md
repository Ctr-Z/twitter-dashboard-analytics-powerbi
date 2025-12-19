# twitter-dashboard-analytics-powerbi
Power BI Dashboard for Twitter Dashboard Analytics (with DAX time-based filters)


# Power BI Twitter Analytics – Task 1

This repository contains **Task 1** of a multi-part Power BI project for analyzing Twitter engagement data.

---

## Task 1 Objective

Create a **scatter chart** in Power BI to analyze the relationship between **Media Engagements** and **Media Views** for qualifying tweets.

---

## Business Rules & Filters

Only include tweets that meet **all** of the following:

| Condition | Description |
|------------|--------------|
| Replies > 10 | Only tweets that generated active discussion |
| Engagement Rate > 5 % | Highlight these tweets in a different color |
| Time window | Tweets posted **between 6 PM and 11 PM IST** |
| Odd-numbered date | Tweet day = 1, 3, 5, … |
| Word count > 50 | Only longer tweets |
| Dashboard visibility | The entire chart hides automatically **outside 6 PM–11 PM IST** |

---

## DAX Logic Used

### **Tweet Hour (IST)**
```DAX
Tweet_Hour_IST =
HOUR('SocialMedia (1)'[ConvertedDateTime] + TIME(5,30,0))
```

### **Tweet Day**
```DAX
Tweet_Day = DAY('SocialMedia (1)'[ConvertedDateTime])
```

### **Word Count**
```DAX
WordCount =
LEN(TRIM('SocialMedia (1)'[TweetText])) -
LEN(SUBSTITUTE(TRIM('SocialMedia (1)'[TweetText]), " ", "")) + 1
```

### **Highlight Flag**
```DAX
Highlight =
IF('SocialMedia (1)'[EngagementRate] > 5, "High Engagement", "Normal")
```

### **Row-Level Filter**
```DAX
Show_Tweet =
VAR HourIST = 'SocialMedia (1)'[Tweet_Hour_IST]
VAR IsOddDay = MOD('SocialMedia (1)'[Tweet_Day], 2) = 1
VAR InTimeRange = HourIST >= 18 && HourIST <= 23
VAR HasEnoughReplies = 'SocialMedia (1)'[Replies] > 10
VAR HasEnoughWords = 'SocialMedia (1)'[WordCount] > 20   // adjusted for dataset
RETURN
IF(HasEnoughReplies && HasEnoughWords && IsOddDay && InTimeRange, 1, 0)
```

### **Dashboard Visibility**
```DAX
ShowGraph =
VAR CurrentISTHour = HOUR(NOW() + TIME(5,30,0))
RETURN IF(CurrentISTHour >= 18 && CurrentISTHour <= 23, 1, 0)
```

---

## Scatter Chart Setup

| Visual Property | Field |
|-----------------|--------|
| X-Axis | `MediaViews` |
| Y-Axis | `MediaEngagements` |
| Legend | `Highlight` |
| Tooltip | `TweetText`, `EngagementRate`, `Replies`, etc. |
| Filters on Visual | `Show_Tweet = 1` and `ShowGraph = 1` |

---

## Notes
- The chart automatically hides outside 6–11 PM IST.  
- Tweets with an engagement rate > 5 % are highlighted.  
- Word-count threshold can be tuned later or replaced with a parameter.
- Currently, the scatter chart appears empty because no tweets meet the word-count filter (all are below 50 words).
---

<img width="1130" height="639" alt="image" src="https://github.com/user-attachments/assets/024ba6cc-28c2-480f-9adf-91a2b8c4738a" />


## Next Steps
Subsequent tasks (2–6) will add new analyses to this same dashboard.  
When all are complete, the final `.pbix` file and comprehensive README will be uploaded.

# Power BI Twitter Analytics – Task 2

This repository contains **Task 2** of a multi-part Power BI project for analyzing Twitter engagement data.

---

## Task 2 Objective

Create a **clustered bar chart** in Power BI that shows the sum of **URL clicks**, **user profile clicks**, and **hashtag clicks** by **tweet category** (e.g., tweets with media, tweets with links, tweets with hashtags).

---

## Business Rules & Filters

Only include tweets that meet **all** of the following:

| Condition | Description |
|------------|--------------|
| At least 1 click type | Tweet must have one of URL clicks, profile clicks, or hashtag clicks |
| Time window | Tweets posted **between 3 PM and 5 PM IST** |
| Even-numbered date | Tweet day = 2, 4, 6, … |
| Word count > 40 | Only longer tweets |

---

## DAX Logic Used

### **Tweet Hour (IST)**
```DAX
Tweet_Hour_IST2 =
HOUR('SocialMedia (1)'[ConvertedDateTime] + TIME(5,30,0))
```

### **Tweet Day**
```DAX
Tweet_Day2 = DAY('SocialMedia (1)'[ConvertedDateTime])
```

### **Word Count**
```DAX
WordCount2 =
LEN(TRIM('SocialMedia (1)'[TweetText])) -
LEN(SUBSTITUTE(TRIM('SocialMedia (1)'[TweetText]), " ", "")) + 1
```

### **Tweet Category**
```DAX
Tweet_Category2 =
SWITCH(
    TRUE(),
    'SocialMedia (1)'[MediaEngagements] > 0 || 'SocialMedia (1)'[MediaViews] > 0, "Tweets with Media",
    'SocialMedia (1)'[UrlClicks] > 0, "Tweets with Links",
    'SocialMedia (1)'[HashtagClicks] > 0, "Tweets with Hashtags",
    "Other"
)
```

### **Row-Level Filter**
```DAX
Show_Tweet2 =
VAR HourIST = 'SocialMedia (1)'[Tweet_Hour_IST2]
VAR IsEvenDay = MOD('SocialMedia (1)'[Tweet_Day2], 2) = 0
VAR InTimeRange = HourIST >= 15 && HourIST < 17        // 3 PM – 5 PM IST
VAR HasClicks =
    ('SocialMedia (1)'[UrlClicks] > 0) ||
    ('SocialMedia (1)'[UserProfileClicks] > 0) ||
    ('SocialMedia (1)'[HashtagClicks] > 0)
VAR HasEnoughWords = 'SocialMedia (1)'[WordCount2] > 40
RETURN
IF(HasClicks && HasEnoughWords && IsEvenDay && InTimeRange, 1, 0)
```

---

## Clustered Bar Chart Setup

| Visual Property | Field |
|-----------------|--------|
| **Axis** | `Tweet_Category2` |
| **Values** | Sum of `UrlClicks`, `UserProfileClicks`, and `HashtagClicks` |
| **Filters** | `Show_Tweet2 = 1` |
| **Tooltip** | Tweet text, engagement metrics, etc. |

---

## Chart Observation

The clustered bar chart currently appears **empty** because of the strict filters applied.  
- Only **6 tweets** fall between **3 PM and 5 PM IST**.  
- Out of these, only **2 tweets** are on even-numbered days.  
- However, those 2 tweets do **not** have any URL, profile, or hashtag clicks.  
Hence, no tweets meet all conditions for display.
Additionally, the word count for all tweets is less than 40, which further contributes to the chart appearing empty since no tweets meet the minimum word count filter.

---


## Next Steps
Upcoming tasks will build additional visuals and KPIs based on other engagement metrics.  
All tasks will be integrated into one final Power BI dashboard.
<img width="1911" height="904" alt="image" src="https://github.com/user-attachments/assets/a7c6e78c-5dc5-46f1-864b-f81321791e2a" />





# Power BI Twitter Analytics – Task 3

This document describes **Task 3** of a multi-part Power BI project focused on analyzing Twitter engagement data using advanced DAX filters and time-based conditions.

---

## Task 3 Objective

Build a chart to identify the **Top 10 tweets** based on the **combined total of retweets and likes**, while applying multiple data and time constraints.

---

## Business Rules & Filters

Only tweets that meet **all** of the following conditions are included:

| Condition | Description |
|----------|-------------|
| Top 10 tweets | Ranked by **Retweets + Likes** |
| Weekdays only | Tweets posted on weekends are excluded |
| Time window | Tweets posted **between 3 PM and 5 PM IST** |
| Impressions | Tweet impressions must be an **even number** |
| Tweet date | Tweet must be posted on an **odd-numbered date** |
| Word count | Tweet text must contain **less than 30 words** |
| User profile | Display the user profile that posted each tweet |
| Dashboard visibility | Chart is visible only between **3 PM–5 PM IST** |

---

## DAX Logic Used

### **Tweet Hour (IST)**
```DAX
Tweet_Hour_IST3 =
HOUR('SocialMedia (1)'[ConvertedDateTime] + TIME(5,30,0))
```

### **Tweet Day**
```DAX
Tweet_Day3 =
DAY('SocialMedia (1)'[ConvertedDateTime])
```

### **Weekend Flag**
```DAX
IsWeekend3 =
VAR DayOfWeek =
    WEEKDAY('SocialMedia (1)'[ConvertedDateTime], 2)
RETURN
IF(DayOfWeek >= 6, 1, 0)
```

### **Word Count**
```DAX
WordCount3 =
LEN(TRIM('SocialMedia (1)'[TweetText])) -
LEN(SUBSTITUTE(TRIM('SocialMedia (1)'[TweetText]), " ", "")) + 1
```

### **Total Engagement (Likes + Retweets)**
```DAX
Retweets_Likes_Total3 =
'SocialMedia (1)'[Retweets] + 'SocialMedia (1)'[Likes]
```

### **Row-Level Filter**
```DAX
Show_Tweet3 =
VAR HourIST = 'SocialMedia (1)'[Tweet_Hour_IST3]
VAR InTimeRange = HourIST >= 15 && HourIST < 17
VAR IsOddDay = MOD('SocialMedia (1)'[Tweet_Day3], 2) = 1
VAR IsWeekday = 'SocialMedia (1)'[IsWeekend3] = 0
VAR EvenImpressions = MOD('SocialMedia (1)'[Impressions], 2) = 0
VAR ShortTweet = 'SocialMedia (1)'[WordCount3] < 30
RETURN
IF(
    InTimeRange &&
    IsOddDay &&
    IsWeekday &&
    EvenImpressions &&
    ShortTweet,
    1,
    0
)
```

### **Dashboard Visibility**
```DAX
ShowGraph3 =
VAR CurrentISTHour = HOUR(NOW() + TIME(5,30,0))
RETURN IF(CurrentISTHour >= 15 && CurrentISTHour < 17, 1, 0)
```

---

## Chart Setup

| Visual Property | Field |
|----------------|------|
| Chart Type | Bar / Column Chart |
| X-Axis | Tweet Content |
| Y-Axis | Total Engagements (Likes + Retweets) |
| Tooltip | User Profile, Likes, Retweets |
| Visual Filters | `Show_Tweet3 = 1`, `ShowGraph3 = 1` |
| Ranking | Top 10 by `Retweets_Likes_Total3` |

---

## Chart Observation

The chart currently displays **only one bar** because only **one tweet** in the dataset satisfies all applied conditions. In particular, most tweets exceed the word-count threshold, and only one tweet has fewer than 30 words while also meeting the remaining filters (weekday, odd date, even impressions, and time window). This confirms that the DAX filtering logic is functioning correctly and the limited output is due to data availability.


---

## 📅 Next Steps

Upcoming tasks will introduce additional engagement-based analyses and visuals. All tasks will be consolidated into a final Power BI dashboard upon completion.

---

<img width="589" height="370" alt="image" src="https://github.com/user-attachments/assets/578f38ef-2587-45ff-870d-563785637912" />



# Power BI Twitter Analytics – Task 4

This document describes **Task 4** of a multi-part Power BI project focused on analyzing engagement trends using time-based, textual, and numeric filters.

---

## Task 4 Objective

Create a **line chart** showing the **monthly trend of average engagement rate**, with separate lines for:
- Tweets **with media content**
- Tweets **without media content**

---

## Business Rules & Filters

Only tweets that meet **all** of the following conditions are included:

| Condition | Description |
|----------|-------------|
| Time windows | Chart works only between **7 AM–11 AM IST** and **3 PM–5 PM IST** |
| Engagement | Tweet engagement value must be an **even number** |
| Tweet date | Tweet must be posted on an **odd-numbered date** |
| Character count | Tweet must have **more than 20 characters** |
| Text filter | Tweets containing the letter **“C” or “c” are excluded** |
| Media split | Separate lines for tweets **with media** and **without media** |
| Metric | Uses **average engagement rate**, grouped by month |

---

## DAX Logic Used

### **Tweet Hour (IST)**
```DAX
Tweet_Hour_IST4 =
HOUR('SocialMedia (1)'[ConvertedDateTime] + TIME(5,30,0))
```

### **Tweet Day**
```DAX
Tweet_Day4 =
DAY('SocialMedia (1)'[ConvertedDateTime])
```

### **Tweet Month**
```DAX
Tweet_Month4 =
FORMAT('SocialMedia (1)'[ConvertedDateTime], "MMM")
```

```DAX
Tweet_Month_Num4 =
MONTH('SocialMedia (1)'[ConvertedDateTime])
```

### **Character Count**
```DAX
CharCount4 =
LEN('SocialMedia (1)'[TweetText])
```

### **Exclude Tweets Containing Letter “C”**
```DAX
Contains_C4 =
IF(
    CONTAINSSTRING(UPPER('SocialMedia (1)'[TweetText]), "C"),
    1,
    0
)
```

### **Media Classification**
```DAX
Media_Flag4 =
IF(
    'SocialMedia (1)'[MediaViews] > 0 ||
    'SocialMedia (1)'[MediaEngagements] > 0,
    "With Media",
    "Without Media"
)
```

### **Row-Level Filter**
```DAX
Show_Tweet4 =
VAR HourIST = 'SocialMedia (1)'[Tweet_Hour_IST4]
VAR InTimeRange =
    (HourIST >= 15 && HourIST < 17) ||
    (HourIST >= 7 && HourIST < 11)
VAR IsOddDay = MOD('SocialMedia (1)'[Tweet_Day4], 2) = 1
VAR EvenEngagement = MOD('SocialMedia (1)'[Engagements], 2) = 0
VAR HasEnoughChars = 'SocialMedia (1)'[CharCount4] > 20
VAR NoCInText = 'SocialMedia (1)'[Contains_C4] = 0
RETURN
IF(
    InTimeRange &&
    IsOddDay &&
    EvenEngagement &&
    HasEnoughChars &&
    NoCInText,
    1,
    0
)
```

### **Dashboard Visibility**
```DAX
ShowGraph4 =
VAR HourIST = HOUR(NOW() + TIME(5,30,0))
RETURN
IF(
    (HourIST >= 15 && HourIST < 17) ||
    (HourIST >= 7 && HourIST < 11),
    1,
    0
)
```

### **Average Engagement Rate**
```DAX
Avg_Engagement_Rate4 =
AVERAGE('SocialMedia (1)'[EngagementRate])
```

---

## Line Chart Setup

| Visual Property | Field |
|----------------|------|
| Chart Type | Line Chart |
| X-Axis | `Tweet_Month4` (sorted by `Tweet_Month_Num4`) |
| Y-Axis | `Avg_Engagement_Rate4` |
| Legend | `Media_Flag4` |
| Visual Filters | `Show_Tweet4 = 1`, `ShowGraph4 = 1` |

---

## Chart Observation

The line chart currently appears **empty** because **all tweets in the dataset contain the letter “C”**, and the applied text filter explicitly excludes any tweet that includes this character. As a result, no tweets qualify for the analysis after all conditions are applied.


---

## Next Steps

Subsequent tasks will continue building additional analytical visuals. All tasks will be consolidated into a final Power BI dashboard at the end of the project.

---



# Power BI Twitter Analytics – Task 5

This document describes **Task 5** of a multi-part Power BI project that focuses on comparing user interactions for tweets with high media engagement using advanced DAX filtering logic.

---

## Task 5 Objective

Develop a visualization that compares the **number of replies, retweets, and likes** for tweets that have received **media engagements greater than the median value**.

---

## Business Rules & Filters

Only tweets that meet **all** of the following conditions are included:

| Condition | Description |
|----------|-------------|
| Media engagement | Media engagements must be **greater than the median** |
| Date range | Tweets posted between **June and August 2020** |
| Time windows | Chart works only between **7 AM–11 AM IST** and **3 PM–5 PM IST** |
| Tweet date | Tweet must be posted on an **odd-numbered date** |
| Media views | Media views must be an **even number** |
| Character count | Tweet must contain **more than 20 characters** |
| Text filter | Tweets containing the letter **“S” or “s” are excluded** |
| Comparison metrics | Replies, Retweets, and Likes |

---

## DAX Logic Used

### **Tweet Hour (IST)**
```DAX
Tweet_Hour_IST5 =
HOUR('SocialMedia (1)'[ConvertedDateTime] + TIME(5,30,0))
```

### **Tweet Day**
```DAX
Tweet_Day5 =
DAY('SocialMedia (1)'[ConvertedDateTime])
```

### **Tweet Year**
```DAX
Tweet_Year5 =
YEAR('SocialMedia (1)'[ConvertedDateTime])
```

### **Tweet Month**
```DAX
Tweet_Month_Num5 =
MONTH('SocialMedia (1)'[ConvertedDateTime])
```

### **Character Count**
```DAX
CharCount5 =
LEN('SocialMedia (1)'[TweetText])
```

### **Exclude Tweets Containing Letter “S”**
```DAX
Contains_S5 =
IF(
    CONTAINSSTRING(UPPER('SocialMedia (1)'[TweetText]), "S"),
    1,
    0
)
```

### **Median Media Engagements**
```DAX
Median_Media_Engagements5 =
MEDIAN('SocialMedia (1)'[MediaEngagements])
```

### **Row-Level Filter**
```DAX
Show_Tweet5 =
VAR HourIST = 'SocialMedia (1)'[Tweet_Hour_IST5]
VAR InTimeRange =
    (HourIST >= 15 && HourIST < 17) ||
    (HourIST >= 7 && HourIST < 11)
VAR IsOddDay = MOD('SocialMedia (1)'[Tweet_Day5], 2) = 1
VAR EvenMediaViews = MOD('SocialMedia (1)'[MediaViews], 2) = 0
VAR EnoughChars = 'SocialMedia (1)'[CharCount5] > 20
VAR NoSInText = 'SocialMedia (1)'[Contains_S5] = 0
VAR InDateRange =
    'SocialMedia (1)'[Tweet_Year5] = 2020 &&
    'SocialMedia (1)'[Tweet_Month_Num5] >= 6 &&
    'SocialMedia (1)'[Tweet_Month_Num5] <= 8
VAR AboveMedianMedia =
    'SocialMedia (1)'[MediaEngagements] > [Median_Media_Engagements5]
RETURN
IF(
    InTimeRange &&
    IsOddDay &&
    EvenMediaViews &&
    EnoughChars &&
    NoSInText &&
    InDateRange &&
    AboveMedianMedia,
    1,
    0
)
```

---

## Visualization Setup

| Visual Property | Field |
|----------------|------|
| Chart Type | Clustered Column Chart |
| X-Axis | Interaction Type (Replies, Retweets, Likes) |
| Y-Axis | Total Interactions |
| Visual Filters | `Show_Tweet5 = 1` |
| Tooltip | Tweet text, media engagements |

---

## Chart Observation

The chart currently appears **empty** because all tweets in the dataset contain the letter **“S”**, which causes them to be excluded by the text-based filter. Even after removing this constraint, the visualization remains empty due to the cumulative effect of multiple strict filters, including the limited date range, time windows, odd tweet dates, even media views, and the requirement for media engagements to be above the median.

---

## Next Steps

The final task will complete the dashboard by introducing additional analytical perspectives. All tasks will be consolidated into a single Power BI report at the end of the project.

---




# Power BI Twitter Analytics – Task 6

This document describes **Task 6** of a multi-part Power BI project focused on comparing engagement rates for tweets based on app open activity using advanced filtering logic.

---

## Task 6 Objective

Analyze and compare the **average engagement rate** for:
- Tweets **with app opens**
- Tweets **without app opens**

> Note: Since the dataset does not contain a direct *App Opens* column, **User Profile Clicks** are used as a proxy to classify tweets.

---

## Business Rules & Filters

Only tweets that meet **all** of the following conditions are included:

| Condition | Description |
|----------|-------------|
| App opens proxy | `UserProfileClicks > 0` → With App Opens |
| Posting window | Tweets posted between **9 AM and 5 PM** |
| Weekdays only | Weekend tweets are excluded |
| Dashboard visibility | Chart visible between **7 AM–11 AM IST** and **12 PM–6 PM IST** |
| Impressions | Tweet impressions must be an **even number** |
| Tweet date | Tweet must be posted on an **odd-numbered date** |
| Character count | Tweet text must contain **more than 30 characters** |
| Text filter | Tweets containing the letter **“D” or “d” are excluded** |

---

## DAX Logic Used

### **Tweet Hour (IST)**
```DAX
Tweet_Hour_IST6 =
HOUR('SocialMedia (1)'[ConvertedDateTime] + TIME(5,30,0))
```

### **Tweet Day**
```DAX
Tweet_Day6 =
DAY('SocialMedia (1)'[ConvertedDateTime])
```

### **Weekend Flag**
```DAX
IsWeekend6 =
VAR DayOfWeek =
    WEEKDAY('SocialMedia (1)'[ConvertedDateTime], 2)
RETURN
IF(DayOfWeek >= 6, 1, 0)
```

### **Character Count**
```DAX
CharCount6 =
LEN('SocialMedia (1)'[TweetText])
```

### **Exclude Tweets Containing Letter “D”**
```DAX
Contains_D6 =
IF(
    CONTAINSSTRING(UPPER('SocialMedia (1)'[TweetText]), "D"),
    1,
    0
)
```

### **App Opens Classification (Derived)**
```DAX
App_Open_Flag6 =
IF(
    'SocialMedia (1)'[UserProfileClicks] > 0,
    "With App Opens",
    "Without App Opens"
)
```

### **Row-Level Filter**
```DAX
Show_Tweet6 =
VAR HourIST = 'SocialMedia (1)'[Tweet_Hour_IST6]
VAR InPostingWindow = HourIST >= 9 && HourIST <= 17
VAR InDashboardWindow =
    (HourIST >= 12 && HourIST < 18) ||
    (HourIST >= 7 && HourIST < 11)
VAR IsOddDay = MOD('SocialMedia (1)'[Tweet_Day6], 2) = 1
VAR IsWeekday = 'SocialMedia (1)'[IsWeekend6] = 0
VAR EvenImpressions = MOD('SocialMedia (1)'[Impressions], 2) = 0
VAR EnoughChars = 'SocialMedia (1)'[CharCount6] > 30
VAR NoDInText = 'SocialMedia (1)'[Contains_D6] = 0
RETURN
IF(
    InPostingWindow &&
    InDashboardWindow &&
    IsOddDay &&
    IsWeekday &&
    EvenImpressions &&
    EnoughChars &&
    NoDInText,
    1,
    0
)
```

---

## Visualization Setup

| Visual Property | Field |
|----------------|------|
| Chart Type | Clustered Column / Bar Chart |
| X-Axis | App Opens Status |
| Y-Axis | Average Engagement Rate |
| Measure | `Avg_Engagement_Rate6` |
| Visual Filters | `Show_Tweet6 = 1` |

### **Average Engagement Rate Measure**
```DAX
Avg_Engagement_Rate6 =
AVERAGE('SocialMedia (1)'[engagement rate])
```

---

## Chart Observation

The chart shows a single bar for “Without App Opens”, indicating that only tweets without app opens meet all the applied filtering conditions. Tweets classified as “With App Opens” do not appear in the visualization because none of them satisfy the combined constraints, including weekday posting between 9 AM and 5 PM, odd tweet dates, even impressions, character count above 30, and exclusion of tweets containing the letter “D.” As a result, the average engagement rate is calculated only for tweets without app opens, confirming that the filtering logic is functioning correctly.

---

<img width="552" height="343" alt="image" src="https://github.com/user-attachments/assets/f8adc27b-37d3-4e89-878d-536ba8c1e0ea" />


## Next Steps

With Task 6 completed, all required analyses for the dashboard are finalized. The final step will be to consolidate all tasks into a single Power BI report.

---


