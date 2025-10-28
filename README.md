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

